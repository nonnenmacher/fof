Controllers
----

The Controller is the orchestrator of each view. You can call a specific task of the Controller –based on the input variables you pass to it– causing it to execute a specific method. The Controller's job is to create a Model and View object, set the state of the Model based on the request and then either call a Model's method to perform an action (e.g. save a record) or pass the View the Model object and tell it to render itself.

## Class and file naming conventions

The convention for naming the controller classes is `ComponentControllerView`, e.g. `TodoControllerItem` for a component named com_todo and a view named items. The last part SHOULD be singular. Support for plural named controllers (such as TodoControllerItems) will be dropped in a future version.

The controller file name MUST match the last part of the class name. This means that the file for `TodoControllerItem` MUST be `item.php`, whereas the file for `TodoControllerItems` MUST be `items.php`.

All Controller files are located in your component's `controllers` directories, in the front-end and back-end. If a file is not present in the front-end, it will be attempted to be loaded from the back-end and vice versa. If the Controller class is not loaded and a suitable file cannot be found FOF will fall back to one of the following, in this order:

1. The Default controller. This is a special controller class following the naming conventions `ComponentControllerDefault`, e.g. `TodoControllerDefault`, found in the `default.php` file inside your controllers directory.
2. If a default controller is not found, FOF will fall back to creating a suitably configured instance of FOFController, using convention over configuration to determine what the model object should do.

## View names and handling by a single controller

The convention in FOF is that he view name is plural when you are executing the browse method (which returns multiple records) and singular in all other cases. Both views are considered to be part of the same triad and are handled by the same controller. For example, let's consider a component named com_todo and a view called items. The view name will be `items` when you are producing a list of all items (browse task), but `item` in all other cases. Both views will be handled by the `TodoControllerItem` class. This is different than plain old Joomla!. You do not need a different "list" and "form" controller. There's one and only one controller per view.

## Customising a specialised class

Unlike plain old Joomla! you are NOT supposed to copy and paste code when dealing with FOF. Our rule of thumb is that if you ever find yourself copying code from FOFController into your extension's specialised table class you're doing it wrong.

FOF controller can be customised very easily using the `onBeforeSomething` / `onAfterSomething` methods. The `Something` is the name of the controller task they are related to. For example, `onBeforeBrowse` runs before the browse task executes and `onAfterBrowse` runs right after the browse task executes. Returning false will result in a 403 Forbidden error. Specific implementation notes for each case can be found in the docblocks of each event method.