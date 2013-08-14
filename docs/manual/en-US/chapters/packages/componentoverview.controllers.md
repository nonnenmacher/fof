Controllers
----

The Controller is the orchestrator of each view. You can call a specific task of the Controller –based on the input variables you pass to it– causing it to execute a specific method. The Controller's job is to create a Model and View object, set the state of the Model based on the request and then either call a Model's method to perform an action (e.g. save a record) or pass the View the Model object and tell it to render itself.

## Class and file naming conventions

The convention for naming the controller classes is `ComponentControllerView`, e.g. `TodoControllerItem` for a component named com_todo and a view named items. The last part SHOULD be singular. Support for plural named controllers (such as TodoControllerItems) will be dropped in a future version.

The controller file name MUST match the last part of the class name. This means that the file for `TodoControllerItem` MUST be `item.php`, whereas the file for `TodoControllerItems` MUST be `items.php`.

All Controller files are located in your component's `controllers` directories, in the front-end and back-end. If a file is not present in the front-end, it will be attempted to be loaded from the back-end and vice versa. If the Controller class is not loaded and a suitable file cannot be found FOF will fall back to one of the following, in this order:

1. The Default controller. This is a special controller class following the naming conventions `ComponentControllerDefault`, e.g. `TodoControllerDefault`, found in the `default.php` file inside your controllers directory.
2. If a default controller is not found, FOF will fall back to creating a suitably configured instance of FOFController, using convention over configuration to determine what the controller object should do.

## View names and handling by a single controller

The convention in FOF is that he view name is plural when you are executing the browse method (which returns multiple records) and singular in all other cases. Both views are considered to be part of the same triad and are handled by the same controller. For example, let's consider a component named com_todo and a view called items. The view name will be `items` when you are producing a list of all items (browse task), but `item` in all other cases. Both views will be handled by the `TodoControllerItem` class. This is different than plain old Joomla!. You do not need a different "list" and "form" controller. There's one and only one controller per view.

## Customising a specialised class

Unlike plain old Joomla! you are NOT supposed to copy and paste code when dealing with FOF. Our rule of thumb is that if you ever find yourself copying code from FOFController into your extension's specialised table class you're doing it wrong.

FOF controller can be customised very easily using the `onBeforeSomething` / `onAfterSomething` methods. The `Something` is the name of the controller task they are related to. For example, `onBeforeBrowse` runs before the browse task executes and `onAfterBrowse` runs right after the browse task executes. Returning false will result in a 403 Forbidden error. Specific implementation notes for each case can be found in the docblocks of each event method.

## Extending your controllers with plugin events

As a developer you've probably found yourself in a position like this: a component you found does *almost* what you want. In order to make it do exactly what you want you need to change how a controller handles a specific task in a specific view. But if you modify the controller ("hack core") you have upgrade and maintenance issues. You can make a feature request to the developer but you don't know if and when the feature will be implemented. If you are the developer of the component you are faced with the dilemma: do I let this client down or do I implement a feature that doesn't quite fit my extension and will become a maintenance burden? 

This is where FOF kicks in. Remember how you can customise a specialised class with onBefore / onAfter methods? Since FOF 2.1.0 you can handle these methods not only with a customised class but also with system plugin events. System plugins are always loaded early in the Joomla load process (as early as the onAfterInitialise call), making them an excellent choice for providing component customisation code, without the need to over-engineer FOF's handling of Controllers.

You will need to create a method named onBefore*Componentname*Controller*ViewnameTaskname*, e.g. onBeforeFoobarControllerItemsRead to handle the onBefore event of com_foobar, items view, read task. Returning false will prevent the task from firing. You can do the same for the onAfter event. Do note that plugin events run *after* the code in your controller and not instead of it or before it. The events must be implemented in system plugins so that they always get loaded by Joomla! before any controller gets the chance to run (remember, HMVC, you may end up calling a controller from a module or plugin).

The signature for these plugins methods is like this:

`onBeforeComponentnameControllerViewnameTaskname(FOFController &$controller, FOFInput &$input)`
Both parameters are passed by reference, meaning that you can modify them from your plugin. There's a caveat: by the time the onBefore plugin event is called the model and view instances have already been created with the previously existing FOFInput instance. If you need to modify the model's state you will have to do something like `$controller->getThisModel()->setState('foo', $myNewFooValue)`

`onAfterComponentnameControllerViewnameTaskname(FOFController &$controller, FOFInput &$input, &$ret)`
The $ret parameter contains the return value of the task method. It is passed by reference and you can modify it from your plugin.