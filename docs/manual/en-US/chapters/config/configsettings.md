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

*	In $config: Specify the absolute path.

*	In fof.xml: Specify a path relative to the site’s root.

**cacheableTasks**
:   A comma separated list of tasks which support Joomla!’s caching.

**cid**
:   Define a comma separated list of item IDs to limit the view on.
    Normally this is empty. Only use when you want to limit a view to
    very specific items. Only valid in the `fof.xml` file.

**csrf\_protection**
:   Should we be doing a token check for the tasks of this view? The
    possible values are:

-	0 - no token checks are performed

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

* In $config: Specify an absolute path.

* In fof.xml: Specify a path relative to the component's directory.

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

* In $config: Specify an absolute path.

* In fof.xml: Specify a path relative to the component's directory.

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

* In $config: Specify the absolute path.

* In fof.xml: Specify a path relative to the component's root
    directory.

**table\_path**
:   The path where the Model will be looking for table classes. By
    default it's the `tables` directory inside your component's
    directory.

* In $config: Specify an absolute path.

* In fof.xml: Specify a path relative to the component's directory.

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

* In $config: Specify an absolute path.

* In fof.xml: Specify a path relative to the component's directory.

**view\_path**
:   The path where the Controller will be looking for View class files.
    By default it's the `views` directory inside your component's
    directory.

* In $config: Specify an absolute path.

* In fof.xml: Specify a path relative to the component's directory.

**viewName**
:   The name of the View class to load. Automatically defined based on
    the component and view names.