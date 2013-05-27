XML Forms
=========
Traditionally, creating view templates involves a .php file where PHP and HTML code are intermixed to create the appropriate representation of the data to be served to a web browser. While this gives maximum flexibility to the developer it is also a drag, requiring you to write a lot of repetitive code.

Joomla! 1.6 and later is providing a solution to this problem, at least for edit views: JForm. With it it's possible to create an XML file which defines the controls of the form and have JForm render it as HTML.

Pros:

-	The view templates are easier to read
-	The HTML generation is abstracted, making it easier to upgrade to newer versions of Joomla! using a different HTML structure

Cons:

- You need to change your Controllers, Models and Views to cater for and display the forms
- They only apply to edit views

FOF takes this concept further with the FOFForm package. Not only can you create edit views, but you can also create browse (records listing) and read (single record display) views out of XML forms. Moreover, the forms are handled automatically by the FOF base MVC classes without requiring you to write any additional code. If you want you can always combine a traditional .php view template with a form file for maximum customisation of your view.

The different form types
------------------------

As implied above, there are three types of XML forms available in FOF: Browse, Read and Edit. Each one follows slightly different conventions and is used in different tasks of each MVC triad. In this section we are going to present what each of those types does and what is its structure.

There a few things you should know before we go into more details.

All form files are placed in your view's `tmpl` directory, e.g. components/com_example/views/items/tmpl.

All form files' names begin with `form.` and end with `.xml`. This is required for Joomla! to distinguish them from view metadata XML files. The middle part of their name follows the same convention as the regular view template files, i.e. "default" for browse tasks, "form" for edit tasks and "item" for read tasks.

For example, the browse form for com_example's items view is located in components/com_example/views/items/tmpl/form.default.xml whereas the form for editing a single item is located in components/com_example/views/item/tmpl/form.form.xml

### Browse forms

Browse forms are used to create a records list view. They are typically used in the back-end to allow the user to view and manipulate a list of records. A typical browse form looks like this:

	<?xml version="1.0" encoding="utf-8"?>
	<form
		lessfiles="media://com_todo/css/backend.less||media://com_todo/css/backend.css"
		type="browse"
		show_header="1"
		show_filters="1"
		show_pagination="1"
		norows_placeholder="COM_TODO_COMMON_NORECORDS"
	>
		<headerset>
			<header name="ordering" type="ordering" sortable="true" tdwidth="1%" />
			<header name="todo_item_id" type="rowselect" tdwidth="20" />
			<header name="title" type="fieldsearchable" sortable="true"
				buttons="yes" buttonclass="btn"
			/>
			<header name="due" type="field" sortable="true" tdwidth="12%" />
			<header name="enabled" type="published" sortable="true" tdwidth="8%" />
		</headerset>

		<fieldset name="items">
			<field name="ordering" type="ordering" labelclass="order"/>

			<field name="todo_item_id" type="selectrow"/>

			<field name="title" type="text"
				show_link="true"
				url="index.php?option=com_todo&amp;view=item&amp;id=[ITEM:ID]"
				class="todoitem"
				empty_replacement="(no title)"
			 />

			<field name="due" type="duedate" />

			<field name="enabled" type="published"/>
		</fieldset>
	</form>

You MUST have exactly one `<headerset>` and one `<fieldset>` tag. The `name` attribute of the `<fieldset>` MUST always be "items". Extra tags and/or `<fieldset>` tags with different name attributes (or no name attributes) will be ignored.

#### Form attributes

The enclosing `<form>` tag MUST have the following attributes:

**type**
:	It must be always set to "browse" for FOF to recognise this as a Browse form

The enclosing `<form>` tag MAY have one or more of the following attributes:

**lessfiles**
:	FOF allows you to include LESS files to customise the styling of your components. You can give a comma separated list of LESS files' identifiers (see the "Media files identifiers" section below) to be loaded by FOF. For example:

	media://com_example/less/backend.less

Compiled LESS files are cached in the `media/lib_fof/compiled` directory for efficiency reasons, using a mangled filename for privacy/security reasons. They are not written in your site's `cache` or `adminstrator/cache` directory as these directories *are not* supposed to be web-accessible, whereas the compiled CSS files, by definition, need to be web-accessible.

Since LESS files require a lot of memory and time to compile you can also provide an alternative pre-compiled CSS file, separated from your LESS file with two bars. For example:

	media://com_example/less/backend.less||media://com_example/css/backend.css

**cssfiles**
:	This works in the same manner as the *lessfiles* directive, but you are only supposed to specify standard CSS files. The CSS files are defined using identifiers, too. For example:

	media://com_example/css/backend.css

Please note that media file overrides rules are in effect for these CSS files.

**jsfiles**
:	Works the same way as *cssfiles*, but it's used to load Javascript files. The Javascript files are defined using identifiers, too. For example:

	media://com_example/js/backend.js

Please note that media file overrides rules are in effect for these Javascript files.

**show_header**
:	Should we display the header section of the browse form? This is the place where the field titles are displayed.

**show_filters**
:	Should we show the filter section of the browse form? On Joomla! 2.5 this is the area below the header where the user can filter the display based on his own criteria. On Joomla! 3.0 and later this area is rendered in the sidebar, at the left hand side of the records list.

**show_pagination**
:	Should we show the pagination results? That's the links to the first, second, third, …, last page and the drop-down for the number of items per page. It is displayed below the list of records.

**norows_placeholder**
:	A translation key displayed instead of a records list when the current view contains no records, e.g. the table is empty or the filters limit display to zero records.

### Read forms

While browse views display a list of records, read forms will display just a single record. These are nowhere near as powerful as hand-coded PHP-based view templates but can be used to get a quick single item output in a snatch when prototyping a component or when your data is really simple. A typical read form looks like this:

	<?xml version="1.0" encoding="utf-8"?>
	<form
		lessfiles="media://com_todo/css/frontend.less||media://com_todo/css/frontend.css"
		type="read"
	>
		<fieldset name="a_single_item" class="todo-item-container form-horizontal">
			<field name="title" type="text"
				label=""
				class="todo-title-field"
				size="50"
			 />

			<field name="due" type="duedate"
				label="COM_TODO_ITEMS_FIELD_DUE"
				labelclass="todo-field"
				size="20"
				default="NOW"
			 />
			<field name="description" type="editor"
				label=""
			/>
		</fieldset>
	</form>

You MUST have at least one `<fieldset>` tag. The `name` attribute of the `<fieldset>` is indifferent.

#### Form attributes

The enclosing `<form>` tag MUST have the following attributes:

**type**
:	It must be always set to "read" for FOF to recognise this as a Read form

The enclosing `<form>` tag MAY have one or more of the following attributes:

**lessfiles**
:	FOF allows you to include LESS files to customise the styling of your components. You can give a comma separated list of LESS files' identifiers (see the "Media files identifiers" section below) to be loaded by FOF. For example:

	media://com_example/less/backend.less

Compiled LESS files are cached in the `media/lib_fof/compiled` directory for efficiency reasons, using a mangled filename for privacy/security reasons. They are not written in your site's `cache` or `adminstrator/cache` directory as these directories *are not* supposed to be web-accessible, whereas the compiled CSS files, by definition, need to be web-accessible.

Since LESS files require a lot of memory and time to compile you can also provide an alternative pre-compiled CSS file, separated from your LESS file with two bars. For example:

	media://com_example/less/backend.less||media://com_example/css/backend.css

**cssfiles**
:	This works in the same manner as the *lessfiles* directive, but you are only supposed to specify standard CSS files. The CSS files are defined using identifiers, too. For example:

	media://com_example/css/backend.css

Please note that media file overrides rules are in effect for these CSS files.

**jsfiles**
:	Works the same way as *cssfiles*, but it's used to load Javascript files. The Javascript files are defined using identifiers, too. For example:

	media://com_example/js/backend.js

Please note that media file overrides rules are in effect for these Javascript files.

### Edit forms

Edit forms are used to edit a single record. They are typically used in the back-end. If you want to use an Edit form in the front-end you will need to specialise your Toolbar class to render a front-end toolbar in the edit task of this specific view, otherwise the form will not be able to be submitted (unless you do other tricks, outside the scope of this developers' documentation).

An edit form looks like this:

	<?xml version="1.0" encoding="utf-8"?>
	<form
		lessfiles="media://com_todo/css/backend.less||media://com_todo/css/backend.css"
		validate="true"
	>
		<fieldset name="basic_configuration"
			label="COM_TODO_ITEMS_GROUP_BASIC"
			description="COM_TODO_ITEMS_GROUP_BASIC_DESC"
			class="span6"
		>
			<field name="title" type="text"
				class="inputbox"
				label="COM_TODO_ITEMS_FIELD_TITLE"
				labelclass="todo-label todo-label-main"
				required="true"
				size="50"
			 />

			<field name="due" type="calendar"
				class="inputbox"
				label="COM_TODO_ITEMS_FIELD_DUE"
				labelclass="todo-label"
				required="true"
				size="20"
				default="NOW"
			 />
			<field name="enabled" type="list" label="JSTATUS"
				labelclass="todo-label"
				description="JFIELD_PUBLISHED_DESC" class="inputbox"
				filter="intval" size="1" default="1"
			>
				<option value="1">JPUBLISHED</option>
				<option value="0">JUNPUBLISHED</option>
			</field>
		</fieldset>
		<fieldset name="description_group"
			label="COM_TODO_ITEMS_GROUP_DESCRIPTION"
			description="COM_TODO_ITEMS_GROUP_DESCRIPTION_DESC"
			class="span6"
		>
			<field name="description" type="editor"
				label=""
				class="inputbox"
				required="false"
				filter="JComponentHelper::filterText" buttons="true"
			/>
		</fieldset>
	</form>

#### Form attributes

The enclosing `<form>` tag MAY have one or more of the following attributes:

**lessfiles**
:	FOF allows you to include LESS files to customise the styling of your components. You can give a comma separated list of LESS files' identifiers (see the "Media files identifiers" section below) to be loaded by FOF. For example:

	media://com_example/less/backend.less

Compiled LESS files are cached in the `media/lib_fof/compiled` directory for efficiency reasons, using a mangled filename for privacy/security reasons. They are not written in your site's `cache` or `adminstrator/cache` directory as these directories *are not* supposed to be web-accessible, whereas the compiled CSS files, by definition, need to be web-accessible.

Since LESS files require a lot of memory and time to compile you can also provide an alternative pre-compiled CSS file, separated from your LESS file with two bars. For example:

	media://com_example/less/backend.less||media://com_example/css/backend.css

**cssfiles**
:	This works in the same manner as the *lessfiles* directive, but you are only supposed to specify standard CSS files. The CSS files are defined using identifiers, too. For example:

	media://com_example/css/backend.css

Please note that media file overrides rules are in effect for these CSS files.

**jsfiles**
:	Works the same way as *cssfiles*, but it's used to load Javascript files. The Javascript files are defined using identifiers, too. For example:

	media://com_example/js/backend.js

Please note that media file overrides rules are in effect for these Javascript files.

**validation**
: Set it to true to have Joomla! load its unobtrusive Javascript validation script. Please note that FOF does not perform automatic server-side validation checks. This is the responsibility of your specialised Table class and its check() method.

### Formatting your forms

#### Assigning classes and IDs to `<fieldset>`s

@TODO fieldset options for formatting

#### Mixing XML forms with PHP-based view templates

@TODO mixing forms and PHP


Header field types reference
----------------------------

### accesslevel

### field

### fieldsearchable

### fieldselectable

### fieldsql

### filtersearchable

### filterselectable

### filtersql

### language

### ordering

### published

### rowselect

Form field types reference
--------------------------

### accesslevel

### button

### cachehandler

### calendar

### captcha

### checkbox

### editor

### email

### groupedlist

### hidden

### image

### imagelist

### integer

### language

### list

### media

### model

### ordering

### password

### plugins

### published

### radio

### rules

### selectrow

### sessionhandler

### spacer

### sql

### tel

### text

### textarea

### timezone

### url

### user

Media files identifiers
-----------------------

FOF expects you to give an abstracted path to your media (CSS, Javascript, image, …) files, also called an "identifier". These identifiers are in the form:

	area://path
	
Where the `area` can be one of:

**media**
: The file is searched inside your site's `media` directory. FOF will also try to locate it in the media overrides directory of your site, e.g. `templates/your_template/media` where your_template is the name of the currently active template on your site.

In this case the `path` is the rest of the path relative to the media or media override directory. The first part of your path SHOULD be your extension's name, e.g. com_example.

Example: `media://com_example/css/style.css` will look for the file `templates/your_template/media/com_example/css/style.css` or, if it doesn't exist, `media/com_example/css/style.css`

**admin**
: The file is searched for in the administration section of your extension. The first part of the path MUST be your extension's name. The file is first searched for in your template override directory.

Example: `admin://com_example/assets/style.css` will look for the file `administrator/templates/your_template/com_example/assets/style.css` or, if it doesn't exist, `administrator/components/com_example/assets/style.css`

**site**
: The file is searched for in the front-end section of your extension. The first part of the path MUST be your extension's name. The file is first searched for in your template override directory.

Example: `site://com_example/assets/style.css` will look for the file `templates/your_template/com_example/assets/style.css` or, if it doesn't exist, `components/com_example/assets/style.css`

> **IMPORTANT**
> 
> FOF cannot know what is the other side's template. Let's put it simply. If you are in the front-end, your template is called "foobar123" and you use the identifier  `admin://com_example/assets/style.css`, FOF will look for the template override in `administrator/templates/foobar123/com_example/assets/style.css`. Of course this is incorrect, but there is no viable way to know what the back-end template in use is from the site's front-end and vice versa. As a result, we strongly recommend only using `media://` identifiers for media files.
> 
> On top of that there is a security aspect as well. The front-end of your component should never try to load media files from the back-end of the component. Many web masters choose to conceal the fact that they are using Joomla! by means of password protection or redirection of the `administrator` directory.