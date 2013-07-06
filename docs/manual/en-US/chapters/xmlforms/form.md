Form field types reference
--------------------------

# Common fields for all types

For all following fields you can set the following attributes:

* **name** The name of the field. This has to match the table field name in the model.
	
	If you want to create a header for a calculated field or for a column that doesn't correspond to a table field please use a name that doesn't overlap with the name of a column in the table. If you want to list a field many times (e.g. display a row selection checkbox and the record ID at the same time) you will have to use the same `name` in both fields, but use a different `id` attribute.
* **type** The field type. See below for the available field types, as well as the options which can be specified in each one of them.
* **label** The language string which will be used for the label of the field; this is a language string that will be fed to JText::_() for translation.
* **id** The `id` attribute for this field. Skip it to have FOF create one based on the field name.

	If none is provided FOF will automatically create one using the convention component_modelname_fieldname_LABEL where component is the name of your component, modelname is the name of your model (usually equals to the view name) and fieldname is the name of the field. For example, for a component com_foobar, a view named items and a field named baz we get the language string COM_FOOBAR_ITEMS_BAZ_LABEL.
* **emptylabel** Set this to 1 if you intend to have a field without a label
* **description** The language string which will be used for the label of the field; this is a language string that will be fed to JText::_() for translation.
* **required** Set it to 1, yes or true to make this a required field. If you use the form validation then the form cannot be submitted unless this value is filled in.


> **IMPORTANT**
>
> The automatic label and description only apply if you are using Akeeba Strapper or if you are using Joomla! 3.0 and later. If you are using FOF on plain old Joomla! 2.5 you must provide the `label` and `description` attributes manually.

# Field types

### accesslevel

This will display a select list with existing Joomla! Access Levels.

You can set the following attributes:

* **class** CSS class (default '')

### button

This will display an input button.

You can set the following attributes:

* **class** CSS class (default '')
* **icon** Bootstrap icon to add to the button (default '')
* **onclick**  "onclick" attribute to add to the button (default '')
* **text** Button text value; this is a language string that will be fed to JText::_() for translation

### cachehandler

This will display a select list with available Joomla! cache handlers

You can set the following attributes:

* **class** CSS class (default '')

### calendar

This will display a calendar/date field.

You can set the following attributes:

* **class** CSS class (default '')
* **format** (defaults '%Y-%m-%d')
* **filter** can be one the following:
	* SERVER_UTC convert a date to UTC based on the server timezone
	* USER_UTC convert a date to UTC based on the user timezone

### captcha

This will display a captcha input.

You can set the following attributes:

* **plugin** The name of the CAPTCHA plugin to use. Leave empty to use whatever is the default on in the Global Configuration of the Joomla! site

### checkbox

This will display a single checkbox input.

You can set the following attributes:

* **class** CSS class (default '')
* **value** the input value
* **checked** the default status for input
* **disabled** Is this a disabled form element?

### components

This will display a select with a list of installed Joomla! components 

You can set the following attributes:

* **class** CSS class (default '')
* **client_ids** comma separated list of applicable client ids (note: 0 = admin, 1 = site)
* **readonly** is this a read only field?
* **disabled** Is this a disabled form element?
* **multiple** Should we allow multiple selections?
* **onchange** onchange JavaScript event

### editor

This will display a WYSIWYG edit area field for content creation and formatted HTML display.

You can set the following attributes:

* **class** CSS class (default '')
* **rows** How many rows the generated `<textarea>` will have, typically used when Javascript is disabled on the browser
* **cols** How many columns the generated `<textarea>` will have, typically used when Javascript is disabled on the browser
* **height** The height of the editor (default: 250)
* **width** The width of the editor (default: 100%)
* **asset_field** The name of the asset_id field in the form (default: asset_id)
* **created_by_field** The name of the created_by field in the form
* **asset_id** The Joomla! asset ID for this record. Leave empty to let FOF use the value of the asset field defined by asset_field.
* **buttons** Which buttons should we show (rendered by editor-xtd plugins)? Use 0, false or no to show now buttons, otherwise provide a comma separated list of button plugin names
* **hide** Which buttons should we hide? Similar to above.

### email

This will display a text input which expects a valid e-mail address.

You can set the following attributes:

* **class** CSS class (default '')
* **show_link** if true put a mailto: link around the address (default false)
* **size** Size of the text input in characters
* **maxlength** Maximum length of the input in characters
* **readonly** Is this a read only field?
* **disabled** Is this a disabled form element?
* **onchange** onchange Javascript event

### groupedlist

This will display a grouped drop down list.

You can set the following attributes:

* **class** CSS class (default '')

This element supports sub-elements organised in `<group>` and `<option>` tags. For more information please consult the documentation of Joomla!'s JFormFieldGroupedList element.

### hidden

This will display a hidden input.

You can set no attributes other than the common ones.

### image

This is an alias for the "media" field type (see below).

### imagelist

This will display a media selection field showing images from a specified folder.

You can set the following attributes:

* **class** CSS class
* **directory** folder to search the images in 
* **style** inline style
* **width** HTML width attribute
* **height** HTML height attribute
* **align** HTML align attribute
* **rel** HTML rel attribute
* **title** image title
* **filter** The filtering string for filenames to show. Default: `\.png$|\.gif$|\.jpg$|\.bmp$|\.ico$|\.jpeg$|\.psd$|\.eps$`
  
### integer

This will display a text input which expects a valid integer value.

You can set the following attributes:

* **class** CSS class (default '')
* **first** Starting number
* **last** Last number to show
* **step** Step for increasing the numbers

For example, when using first=10, last=20 and step=2 you get a list of 10, 12, 14, 16, 18, 20.

### language

This will display a select input of all available Joomla! languages

You can set the following attributes:

* **class** CSS class (default '')
* **client** Can take the values of 'site' or 'administrator' to show the available languages for the front- and back-end respectively.

### list

This will display a select input of generic options.

**IMPORTANT** The following attributes apply to all field types that present a drop-down list; they all descend from this field type.

You can set the following attributes:

* **class** CSS class (default '')
* **readonly** Is this a read-only field?
* **disabled** Is this a disabled form element?
* **multiple** Should we allow multiple selections?
* **onchange** The onChange Javascript event
* **url** URL template for each element (use [ITEM:ID] as a placeholder for the item id) 
* **show_link** if true, adds a link around each item based on the "url" attribute (default false)

This element has `<option>` sub-elements defining the available options. Please consult Joomla!'s own element of the same type for more information.

Since FOF 2.1.0 we allow you to use a programmatically generated data source instead of the hard-coded `<option>` tags. This can be used when you need your code to generate options based on some configuration data, data from the database and so on. You do that by supplying the name of a PHP class and a static method on that class which returns the data. The data must be returned in an indexed array where the key is the key of the drop-down list item and the value is the description (translation key or string). You may also use a simple array containing indexed arrays by using the `source_key` and `source_value` attributes.

The relevant attributes are:

* **source_file** (optional) The PHP file which provides the class and method. It is given in the pseudo-URL format e.g. `admin://components/com_foobar/helpers/mydata.php` or `site://components/com_foobar/helpers/mydata.php` for a file relative to the administrator or site root directory respectively.
* **source_class** (required) The name of the PHP class to use, e.g. `FoobarHelperMydata`
* **source_method** (required) The static method of the PHP class to use, e.g. `getSomeFoobarData`
* **source_key** (optional) If you are using an array of indexed arrays, this is the key of the indexed array that contains the key of the drop-down option.
* **source_key** (optional) If you are using an array of indexed arrays, this is the key of the indexed array that contains the value (description) of the drop-down option.
* **source_translate** (optional) By default all values are being translated, i.e. fed through JText::_(). If you don't want that, set this attribute to "false".

### media

This will display a media selection field.

You can set the following attributes:

* **class** CSS class
* **style** inline style
* **width** HTML width attribute
* **height** HTML height attribute
* **align** HTML align attribute
* **rel** HTML rel attribute
* **title** image title
* **asset_field** The name of the asset_id field in the form (default: asset_id)
* **created_by_field** The name of the created_by field in the form
* **asset_id** The Joomla! asset ID for this record. Leave empty to let FOF use the value of the asset field defined by asset_field.
* **link** The link to a media management component to use. Skip this to use Joomla!'s own com_media (strongly recommended!)
* **size** Field size in characters
* **onchange** The onChange Javascript event
* **preview** Should we show a preview of the selected media file?
* **preview_width** Maximum width of preview in pixels
* **preview_height** Maximum height of preview in pixels
* **directory** Directory to scan for images relative to site's root. Skip to use the site's images directory.

### model

Similar to the list field, but gets the options from a FOFModel descendant.

You can set the following attributes on top of those of the 'list' field type's:

* **model** The name of the model to use, e.g. FoobarModelItems
* **key_field** The name of the field in the model's results which is used as the key value of the drop-down
* **value_field** The name of the field in the model's results which is used as the label of the drop-down
* **translate** Should the value field's value be passed through JText::_() before being displayed?
* **apply_access** Should we respect the view access level, if an access field is present in the model
* **none** The placeholder to be shown if the value is not found in the data returned by the model. This placeholder goes through JText, so you can use a language string if you like.
* **format** See the text field type
* **show_link** See the text field type
* **url** See the text field type

In order to filter the model you can specify `<state>` sub-elements in the format:

	<state key="state_key">value</state>

Where state_key is the key of a state variable and value is its value. For instance, you could have something like:

	<state key="foobar_category_id">123</state>
	
### ordering

This will display an ordering field for your list, both in traditional Joomla! method and with a new ajax drag'n'drop method. We recommend placing this field first on your form, to respect Joomla! 3.0 and later's JUI (Joomla! User Interface) guidelines.

### password

This will display a password input field.

You can set the following attributes:

* **class** CSS class
* **size** Size of the field in characters
* **maxlength** Maximum length of the input in characters
* **autocomplete** Should we allow browser autocomplete of the password field?
* **readonly** Is this a read only field?
* **disabled** Is this a disabled form element?
* **strengthmeter** Should we show a password strength meter?
* **threshold** What is the minimum password strength we are supposed to accept in order to validate the field (default: 66)?

### plugins

This will display a select input with a list of all installed Joomla! package.

You can set the following attributes:

* **class** CSS class
* **folder** The plugin type to load, e.g. "system", "content" and so on.

The list field type's attributes apply as well.

### published

This will display a status toggle input field (each time you click on it it changes the status).

You can set the following attributes:

* **show_published** if true, the "published" status will be included in the toggle cycle (default true)
* **show_unpublished** if true, the "unpublished" status will be included in the toggle cycle (default true)
* **show_archived** if true, the "archived" status will be included in the toggle cycle (default false)
* **show_trash** if true, the "trash" status will be included in the toggle cycle (default false)
* **show_all** if true, all the available status will be included in the toggle cycle (default false)

The list field type's attributes apply as well.

### radio

This will display a radio selection input.

You can set the following attributes:

* **class** CSS class

### rules

Displays the ACL privileges setup user interface.

Please consult the documentation of JFormFieldRules for more information.

### selectrow

Displays a checkbox to select the entire row for toolbar button operations such as edit, delete, copy etc.

### sessionhandler

This will display a Joomla! session handler selection input.

You can set the following attributes:

* **class** CSS class

Please refer to Joomla!'s JFormFieldSessionHandler for more information.

### spacer

This will display a spacer (static element) between form elements.

You can set no attributes.

### sql

This will display a select input based on a custom SQL query

You can set the following attributes:

* **class** CSS class
* **key_field** the table field to use as key
* **value_field** the table field to display as text
* **query** the actual SQL query to run

We recommend avoiding this field type as the query is specific to a particular database server technology. Using the `model` or `list` type with a programmatic data source is strongly encouraged.


### tel

This will display a text input which expects a valid telephone value.

You can set the following attributes:

* **class** CSS class (default '')
* **show_link** if true, a "tel:" link will be appended around the field value (default false)
* **empty_replacement** a string to show in place of the field when it's empty

The text field type's attributes apply as well.

### text

This will display a single line text input.

You can set the following attributes:

* **class** CSS class (default '')
* **url** URL template for each element (use [ITEM:ID] as a placeholder for the item id). This goes through the field tag replacement (see below)
* **show_link** if true, a "tel:" link will be appended around the field value (default false)
* **empty_replacement** a string to show in place of the field when it's empty
* **size** The size of the input in characters
* **maxlength** The maximum acceptable input length in characters
* **readonly** Is this a read-only field?
* **disabled** Is this a disabled form field element?
* **format_string** A string or translation key used to format the text data before it is displayed. Uses the format() PHP function's syntax.
* **format_if_not_empty** Should we apply the format string even when the field is empty? Default: true
* **parse_value** If set to true, the value of the field will go through the field tag replacement (see below) Default: false

#### Field tag replacement for text fields

You can reference values from other fields inside your text. You can do that using the square bracket tag syntax, i.e. `[ITEM:fieldname]` is replaced with the value of the field `fieldname`. The tag must open with a square bracket, followed by the uppercase word ITEM, followed by a colon, the field name and closing with a square bracket. You must not use spaces in the tag.

FOF also recognises the special tag `[ITEM:ID]`, replacing it with the value of the key field of the table.

### textarea

This will display a textarea input.

You can set the following attributes:

* **class** CSS class (default '')
* **disabled** Is this  disabled form element?
* **cols** Number of columns
* **rows** Number of rows
* **onchange** The onChange Javascript event

### title

This is like a text field. On list views it will display a second line containing secondary information, e.g. the alias (slug) of the record.

The following attributes are used on top of the text field's attributes:

* **slug_field** The name of the field containing the slug or other secondary information to display. Default: slug
* **slug_format** The format string (string or translation key) for the secondary information line. Default: (%s)
* **slug_class** The CSS class of the secondary information line. Default: small

### timezone

This will display a select list with all available timezones.

You can set the following attributes:

* **class** CSS class (default '')

### url

This will display a text input which expects a valid URL.

You can set the following attributes:

* **class** CSS class (default '')
* **show_link** if true, an &lt;a&gt; link will be added around the field value (default false)
* **empty_replacement** a string to show in place of the field when it's empty

The text field type's attributes apply as well.

### user

This will display a select list with all available Joomla! users.

You can set the following attributes:

* **class** CSS class (default '')
* **show_username** if true, show the username (default true)
* **show_email** if true, show the username (default true)
* **show_name** if true, show the full name (default true)
* **show_id** if true, show the id (default true)
* **show_link** if true, add a link around the field value (default false)
* **show_avatar** if true, show the avatar (user picture). Default false.
* **avatar_size** size of the image in the avatar (avatars are square, so this is both the width and height of the avatar)
* **avatar_method** if set to "plugin" use FOF plugins, else fall back to a Gravatar based on the user's email address
