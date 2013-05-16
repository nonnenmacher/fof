Configuring the MVC and associated classes
==========================================

All MVC and associated classes in FOF (Dispatcher, Controller, Model,
View, Table, Toolbar) come with a default behavior, for example where to
look for model files, how to handle request data and so on. While this
is fine most of the times –as long as you follow FOF’s conventions– this
is not always desirable.

For example, if you are building a CCK (something like K2) you may want
to look for view templates in a non-standard directory in order to
support alternative “themes”. Or, maybe, if you're building a contact
component you only want to expose the add view to your front-end users
so that they can file a contact request but not view other people's
contact requests. You get the idea.

The traditional approach to development prescribes overriding classes,
even to the extent of copying and pasting code. If you've ever attended
one of my presentations you've probably figured that I consider copying
and pasting code a mortal sin. You may have also figured that, like all
developers, I am lazy and dislike writing lots of code. Naturally, FOF
being a RAD tool it provides an elegant solution to this problem. The
`$config` array and its sibling, the `fof.xml` file.

What is the `$config` array?
----------------------------

You may have observed that FOF’s MVC classes can be passed an optional
array parameter `$config`. This is a hash array with configuration
options. It is being passed from the Dispatcher to the Controller and
from there to the Model, View and Table classes. Essentially, this is
your view (MVC triad) configuration. Setting its options allows you to
modify FOF’s internal workings without writing code.

The various possible settings are explained in “The configuration
settings” section below.

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
configure FOF for front-end, back-end and CLI access respectively.
Please note that the CLI is a special case: it will mix both the
back-end and CLI settings to derive the final configuration. In the
other two cases (front- or back-end access) only the configuration for
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

The configuration settings
--------------------------

The following settings can be used either in the `$config` array passed
to a Dispatcher, Controller, Model or View class or in the `fof.xml`
file’s `<option>` tags inside the `<view>` tags.

**autoRouting**
:   A bit mask which defines the automatic URL routing of redirections.
    A value of 1 means that front-end redirections will be put through
    Joomla!’s JRoute::\_(). A value of 2 means that back-end
    redirections will be put through Joomla!’s JRoute::\_(). You can
    combine multiple values by adding them together.
    
**asset_key**
:	The key to be used for ACL assets. This is typically in the form `component.view`, e.g. `com_example.item` This is only used for per-item ACL privileges. If you do not specify an asset key, the default `component.view` convention will be used instead.

**base\_path**
:   The base path of the component.

    In \$config: Specify the absolute path.

    In fof.xml: Specify a path relative to the site’s root.

**cacheableTasks**
:   A comma separated list of tasks which support Joomla!’s caching.

**cid**
:   Define a comma separated list of item IDs to limit the view on.
    Normally this is empty. Only use when you want to limit a view to
    very specific items. Only valid in the `fof.xml` file.

**csrf\_protection**
:   Should we be doing a token check for the tasks of this view? The
    possible values are:

    -   0 - no token checks are performed

    -   1 - token checks are always performed

    -   2 - token checks are always performed in the back-end and in in
        the front-end, but only when the request format is 'html'
        (default setting)

    -   3 - token checks are performer only in the back-end

**default\_task**
:   The task to execute if none is defined. The default value is
    `display`.

**event\_after\_delete**
:   The content plugin event to trigger after deleting the data.
    Default: onContentAfterDelete

**event\_after\_save**
:   The content plugin event to trigger after saving the data. Default:
    onContentAfterSave

**event\_before\_delete**
:   The content plugin event to trigger before deleting the data.
    Default: onContentBeforeDelete

**event\_before\_save**
:   The content plugin event to trigger before saving the data. Default:
    onContentBeforeSave

**event\_change\_state**
:   The content plugin event to trigger after changing the published
    state of the data. Default: onContentChangeState

**event\_clean\_cache**
:   The content plugin event to trigger when cleaning cache. There is no
    default value.

**helper\_path**
:   The path where the View will be looking for helper classes. By
    default it's the `helpers` directory inside your component's
    directory.

    In \$config: Specify an absolute path.

    In fof.xml: Specify a path relative to the component's directory.

**id**
:   Define an item ID to limit the view on. Normally this is empty. Only
    use when you want to limit a view to one single item. Only valid in
    the `fof.xml` file.

**ignore\_request**
:   Set to 1 to prevent the Model's populateState() method from running.
    By default the method is empty and does nothing, as the Model is
    supposed to be decoupled from the request information, having the
    Controller push state variables to it.

**layout**
:   The default layout to use for this view. This is normally determined
    automatically based on the task currently being executed.

**model\_path**
:   The path where the Controller will be looking for Model class files.
    By default it's the `models` directory of the component's directory.

    In \$config: Specify an absolute path.

    In fof.xml: Specify a path relative to the component's directory.

**model\_prefix**
:   The naming prefix for the Model to be loaded by the Controller. The
    default option is `Model` where Componentname is the name of the
    component without the com\_ prefix.

**modelName**
:   The name of the Model class to load. Automatically defined based on
    the component and view names.

**searchpath**
:   The path where Controller classes will be searched for. By default
    it's the `controllers` directory inside your component's directory.

    In \$config: Specify the absolute path.

    In fof.xml: Specify a path relative to the component's root
    directory.

**table\_path**
:   The path where the Model will be looking for table classes. By
    default it's the `tables` directory inside your component's
    directory.

    In \$config: Specify an absolute path.

    In fof.xml: Specify a path relative to the component's directory.

**table**
:   Set the name of the table class the Model will use. Please note that
    the the component name is added to this name automatically. For
    example, given a component com\_example and a table setting of
    `foobar` the actual table class which will be used will be
    `ExampleTableFoobar`.

**tbl**
:   The name of the database table to use in the table class of this
    view. It is in the format of \#\_\_tablename, e.g.
    \#\_\_example\_items

**tbl\_key**
:   The name of the key field of the database table to use in the table
    class of this view. It is in the format of component\_view\_id, e.g.
    example\_item\_id.

**template\_path**
:   The path where the View will be looking for view template (.php) or
    form (.xml) files. By default it's the `tmpl` directory inside the
    current view's directory.

    In \$config: Specify an absolute path.

    In fof.xml: Specify a path relative to the component's directory.

**view\_path**
:   The path where the Controller will be looking for View class files.
    By default it's the `views` directory inside your component's
    directory.

    In \$config: Specify an absolute path.

    In fof.xml: Specify a path relative to the component's directory.

**viewName**
:   The name of the View class to load. Automatically defined based on
    the component and view names.