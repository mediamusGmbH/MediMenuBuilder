# MediMenuBuilder

The MediMenuBuilder is for creating a menu inset your page. It needs the repeater and also the repeater matrix.

## Install

After installing, you need to go to the backend and create a new page with the "options_generals" template. Inside this page, you will find a tab labeled "Menü - Einstellungen." Here, you will see a repeater matrix field (matrix_menu).

Now you can add a new menu for the header or the footer.

The repeater contains a menu_select field, which includes a site tree to select the items needed in the menu.

If you want a submenu, you need to set a deeper depth.

## Call the Module

Once installed and integrated, you get a JSON string back from the module.

First of all, you need the options page to get the repeater matrix (matrix_menu). For example, like this:

```
$options_page = $pages->get("template=options_generals");
```
Alternatively, you can use this:
```
$options_page = $pages->get(your_page_id);
```
After this, you need to get the module with this:
```
$menu_builder = $modules->get('MediMenuBuilder');
```
Then you can call the function getMenu():
```
$menu_json = json_decode($menu_builder->getMenu($options_page->matrix_menu, 1));
```
The 1 inside the function call is for the header. You can also use 2 for the footer selection, which will get the elements of the footer from the options page.

The first parameter you need to pass to the function is the matrix from the options page.

## JSON - String

After this you esialy could use this with an foreach like this:
```

foreach ($menu_json->menu_elements as $element) {
    $html_submenu           = '';
    $html_submenu_item      = '';
    $css_toggler            = '';

    if ( count($element->menu_sub_elements) ) {
        $css_toggler = ' dropdown-toggle';

        foreach ( $element->menu_sub_elements as $sub_element ) {
            $html_subsubmenu        = '';
            $html_subsubmenu_item   = '';
            $css_toggler_sub        = '';

            if ( count($sub_element->menu_sub_sub_elements) ) {
                $css_toggler_sub = ' class="dropdown-sub-toggle"';

                foreach ( $sub_element->menu_sub_sub_elements as $subsub_element ) {
                    $html_subsubmenu_item   = '
                        <li>
                            <a class="dropdown-item" href="' . $subsub_element->url . '">
                                ' . $subsub_element->title . '
                            </a>
                        </li>';
                }

                $html_subsubmenu = '
                    <ul class="position-absolute p-3 z-3 dropdown-menu-sub">
                        ' . $html_subsubmenu_item . '
                    </ul>';
            }

            $html_submenu_item .= '
                        <li' . $css_toggler_sub . '>
                            <a class="dropdown-item" href="' . $sub_element->url . '">
                                ' . $sub_element->title . '
                            </a>
                            ' . $html_subsubmenu . '
                        </li>';
        }

        $html_submenu = '
            <ul class="position-absolute bg-white p-3 z-3 dropdown-menu">
                ' . $html_submenu_item . '
            </ul>';
    }

    $html_menu .= '
            <li class="position-relative px-3 nav-item">
                <a href="' . $element->url . '" class="nav-link' . $css_toggler . '">
                    ' . $element->title . '
                </a>
                ' . $html_submenu . '
            </li>';
}
```