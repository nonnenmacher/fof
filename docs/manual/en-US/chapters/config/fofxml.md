What’s the deal with the `fof.xml` file?
----------------------------------------

The `$config` array is a great idea but has a major drawback: you have
to create one or several `.php` files with specialized classes to use
it. Remember the FOF promise about not having to write code unless
absolutely necessary? Yep, this doesn’t stick very well with that
promise. So the `fof.xml` file was born in FOF 2.1.

The `fof.xml` file is a simple XML file placed inside your component's
**back-end** directory, e.g. `administrator/com_example/fof.xml`. It
contains configuration overrides for the front-end, back-end and CLI
parts of your FOF component.

### A sample `fof.xml` file

    <?xml version="1.0" encoding="UTF-8"?>
    <fof>
        <!-- Common settings -->
        <common>
        	<!-- Table options common to all tables -->
        	<table name="*">
        		<field name="locked_by">checked_out</field>
        		<field name="locked_on">checked_out_time</field>
        	</table>
        	<!-- Table options for a specific table -->
        	<table name="items">
        		<field name="enabled">published</field>
        	</table>
        </common>    
    
        <!-- Component back-end options -->
        <backend>
            <!-- Dispatcher options -->
            <dispatcher>
                <option name="default_view">items</option>
            </dispatcher>
        </backend>
                
        <!-- Component front-end options -->
        <frontend>
            <!-- Dispatcher options -->
            <dispatcher>
                <option name="default_view">item</option>
            </dispatcher>
            <!-- Options common for all views -->
            <view name="*">
                <!-- Per-task ACL settings. The star task sets the default ACL privileges for all tasks. -->
                <acl>
                    <task name="*">false</task>
                </acl>
            </view>
            <view name="item">
                <!-- Task mapping -->
                <taskmap>
                    <task name="list">browse</task>
                </taskmap>
                <!-- Per-task ACL settings. An empty string removes ACL checks. -->
                <acl>
                    <!-- Everyone, including guests, can access dosomething -->
                    <task name="dosomething"></task>
                    <!-- Only people with the core.manage privilege can access the somethingelse task -->
                    <task name="somethingelse">core.manage</task>
                </acl>
            </view>
        </frontend>
    </fof>

The `fof.xml` file has an `<fof>` root element. Inside it you can have
zero or one tags called `<frontend>`, `<backend>` and `<cli>` which
configure FOF for front-end, back-end and CLI access respectively. You may also have a tag named `<common>` which defines settings applicable for any mode of access. These common settings will be overridden by the corresponding settings defined in the `<frontend>`, `<backend>` and `<cli>`.
Please note that the CLI is yet another special case: it will mix the common,
back-end and CLI settings to derive the final configuration. In the
other two cases (front- or back-end access) only the common and the configuration for
this specific mode of access will be used.

### Dispatcher settings

You can configure the way the Dispatcher works using the `<dispatcher>`
tag. Inside it you can have one or more `<option>` tags. The name
attribute defines the name of the configuration variable to set, while
the tag's content defines the value of this configuration variable.

The available variables are:

default\_view
:   Defines the default view to show if none is defined in the input
    data. By default this is `cpanel`. In the example above we set it to
    items in the back-end and item in the front-end.

### Table settings

The table settings allow you to set up table options, in case you do not wish to use the default conventions of FOF. You define the table the options apply to using the `name` attribute of the view tag. Please note that this is the name used in the class, not in the database. So, if you have a database table named `#__example_items` and your class is named `ExampleTableItem` you must use `name="item"` in the `fof.xml` file.

The settings of each table are isolated from the settings of every other
table, with one notable exception: the star table, i.e. `name="*"`. This
is a placeholder table that defines the default settings. These settings
are applied to all tables. If you also have a table tag for a view, the
default settings (from the star table) and the settings for the
particular table  are merged together. This applies to all settings
described below.

#### Field map settings

The field map settings allow you to map specific magic field names to your table's fields, in case you do not use FOF's contentions. It works the same way as adding setting the `_columnAlias` array in your
specialized Table class.

The field map is enclosed inside the `<table>` tag itself. It consists of one or more `<field>` tags. The
`name` attribute defines the name of the magic (FOF convention) field to map, whereas the
content of the tag defines the name of the field in your database table.

### View settings

There are several options that are applied per view. In the context of
the `fof.xml` file, a “view” actually refers to an MVC triad, not just
the View part of the triad. In so many words, the options affect the
Controller, Model and View used to render this particular component
view. You define the view the options apply to using the `name`
attribute of the view tag.

The settings of each view are isolated from the settings of every other
view, with one notable exception: the star view, i.e. `name="*"`. This
is a placeholder view that defines the default settings. These settings
are applied to all views. If you also have a view tag for a view, the
default settings (from the star view) and the settings for the
particular view are merged together. This applies to all settings
described below.

#### Task map settings

The task map settings allow you to map specific tasks to specific
Controller methods. Other frameworks would call this the “routing”
feature. It works the same way as adding running `registerTask` in your
specialized Controller class.

The task map is enclosed inside a single `<taskmap>` tag. You can have
exactly zero or one `<taskmap>` tags inside each view tag.

Inside the `<taskmap>` tag you can have one or more `<task>` tags. The
`name` attribute defines the name of the task to map, whereas the
content of the tag defines the controller’s method which will be called
for this task.

#### ACL settings

The ACL settings can be used to override or fine-tune the access control
for each task of the particular view. Even though FOF comes with default
ACL mappings for its basic tasks, these are not always sufficient or
appropriate for all situations. Normally this is achieved by overriding
the `onBefore` methods in the Controller, e.g. `onBeforeSave` to set up
the ACL checks for the save task. You can use the ACL mappings in the
`fof.xml` instead of such checks. You can even use the ACL mapping in
`fof.xml` for custom tasks for which no `onBefore` method exists.

The ACL settings are enclosed inside a single `<acl>` tag. You can have
exactly zero or one `<acl>` tags inside each view tag.

Inside the `<acl>` tag you can have one or more `<task>` tags. The name
attribute defines the name of the task to apply the access control,
whereas the content of the tag defines the Joomla! ACL privilege
required to access this task. You can use any core ACL privilege or any
custom ACL privilege defined in your component's `access.xml` file. If
you leave the content blank then no ACL check is performed (the task is
always accessible by all users). If you use the special value false then
the ACL privilege is always going to fail, i.e. the task will not be
accessible by any user.

#### Option settings

The `<option>` tag can be used to set up a setting of this View. It is
equivalent to passing a value in the `$config` array. The `name`
attribute defines the name of the configuration setting you want to
modify. The content of the tag is the value of this setting.