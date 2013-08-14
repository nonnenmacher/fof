Toolbar
----

The Toolbar is the part of your components which handles the display of the component's title and toolbar buttons, as well as the toolbar submenu (links or tabs under the toolbar). While usually used in the back-end of your site, FOF components can readily render a toolbar in the front-end part of the component as well. Do note that you will need to provide your own CSS to style the toolbar in the front-end as Joomla! templates lack such a styling.

## Class and file naming conventions

The convention for naming the toolbar classes is `ComponentToolbar`, e.g. `TodoToolbar` for a component named com_todo. The last part MUST be Toolbar.

The controller file name MUST be `toolbar.php`. All Toolbar files are located in your component's main directory, in the front-end and back-end. If a file is not present in the front-end, it will be attempted to be loaded from the back-end. If the Toolbar class is not loaded and a suitable file cannot be found FOF will fall back to creating a suitably configured instance of FOFToolbar, using convention over configuration to determine what the controller object should do.

## Customising a specialised class

FOF toolbar can be customised very easily using methods following one of the following conventions, from most specific to least specific:

*	`onViewnameTaskname`, for example `onItemsBrowse`. The name consists of the word `on` in lowercase, followed by camel cased view and task names, in this order. When the task is Browse the view name MUST be plural. For any other task the view name MUST be singular. For example: `onItemsBrowse` and `onItemAdd`
*	`onViewname`, for example `onItems`. The name consists of the word `on` in lowercase, followed by camel cased view name.
*	`onTaskname`, for example `onBrowse`. The name consists of the word `on` in lowercase, followed by camel cased task name.

The method to be called is selected from the most to the least specific. For example, if you have a component named `com_todo` and a view named `items`, with the task `browse` being called FOF will search for the following method names, in this order:
* `onItemsBrowse`
* `onItems`
* `onBrowse`

Please note that any of these methods should only modify the toolbar and not perform any other kind of data processing.

## Customising the link bar

The link bar is the area normally displayed right below the toolbar in the back-end of the site. It is usually rendered as flat links (Joomla! 2.5), a left-hand sidebar (Joomla! 3.0 and later) or tabs (when using Akeeba Strapper). The exact rendering depends on the template. The interesting thing is how these links are populated.

### Automatically populated link bar

FOF will normally look inside your component's `views` directory and look for plural views. These views are automatically added to the link bar in alphabetical order. Exception: a view called cpanel will always be added to the link bar.

If you want a view to not be included in the link bar, please create a file named `skip.xml` and put it inside its directory. FOF will see that and refrain from adding this view to the link bar.

If you want to modify the ordering of a view you have to create or modify the `metadata.xml` file inside your view's directory. The `<foflib>` section inside the metadata.xml file is read by FOF. For example:

	<?xml version="1.0" encoding="utf-8"?>
	<metadata>
		<foflib>
			<ordering>12</ordering>
		</foflib>
		<view title="COM_FOOBAR_VIEW_ITEMS_TITLE">
			<message><![CDATA[COM_FOOBAR_VIEW_ITEMS_DESC]]></message>
		</view>
	</metadata>

tells FOF that this view should be the 12th link in the link bar.

If you're not using a metadata.xml file and have a view called cpanel or cpanels then it will always be reordered to the top of the link bar list.

### Fully customised link bar

The automatically generated link bar is usually enough, but sometimes you want a more complex presentation. For example, you want to show different link bars depending on a configuration setting (e.g. a "Power user" switch in your component's options), or a drop-down menu. To this end, FOFToolbar provides the following methods.

`public function clearLinks()`
Removes all links from the link bar, allowing you to start from a clean slate.

`public function &getLinks()`
Returns the raw data for the links in the link bar. We recommend against using it as the internal data structure may change in the future.

`public function appendLink($name, $link = null, $active = false, $icon = null, $parent = '')`
Appends a link to the link bar. If you use the last option ($parent) you are creating a submenu item whose parent is the $parent item. You reference the parent item by its name (i.e. the $name parameter you used in the parent element). Drop-downs only work in a. Joomla! 3.0 and later without any additional requirements; or b. Joomla! 2.5 but only when using the optional Akeeba Strapper package which back ports jQuery and Bootstrap to Joomla! 2.5 sites.

In order to use these methods you will have to override the `renderSubmenu` method in FOFToolbar.

### When the link bar is rendered

The link bar is rendered in all HTML views, unless you are have an input variable named `tmpl` with a value of component. Typically, this means that you are passing a query string parameter &tmpl=component to the URL of your component.

You can force the entire toolbar (and, by extent, the link bar) to be displayed or hidden using the `render_toolbar` input variable. When you set it to 0 the toolbar and link bar will not be displayed. When you set it to 1 the toolbar and link bar will be displayed (even when you use tmpl=component).