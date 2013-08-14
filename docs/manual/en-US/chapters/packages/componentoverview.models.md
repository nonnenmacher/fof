Models
----

The Model is the workhorse. Business logic goes here. Models never interface input data directly or output data. They are supposed to read data from their state and push the results to their state.

## Class and file naming conventions

The convention for naming the model classes is `ComponentModelView`, e.g. `TodoModelItems` for a component named com_todo and a view named items. The last part SHOULD be plural. Support for singular named models (such as TodoModelItem) will be dropped in a future version.

The model file MUST match the last part of the class name. This means that the file for `TodoModelItems` MUST be `items.php`, whereas the file for `TodoModelItem` MUST be `item.php`.

All Model files are located in your component's `models` directories, in the front-end and back-end. If a file is not present in the front-end, it will be attempted to be loaded from the back-end and vice versa. If the Model class is not loaded and a suitable file cannot be found FOF will fall back to one of the following, in this order:

1. The Default model. This is a special model class following the naming conventions `ComponentModelDefault`, e.g. `TodoModelDefault`, found in the `default.php` file inside your models directory.
2. If a default model is not found, FOF will fall back to creating a suitably configured instance of FOFModel, using convention over configuration (explained below) to determine what the model object should do.

## Database table naming conventions

All FOF models connect, by default, to a database table. You can of course have a model whose corresponding table doesn't exist as long as you do not use its default data processing methods.

Database tables are named as `#__component_view`, e.g. `#__todo_items` for a component named com_todo and a view named items.

The auto increment field is named `component_view_id`, e.g. `todo_item_id` for a component named com_todo and a view named items. If your table does not have an auto incrementing field you will not be able to use the default implementation of FOF's data processing methods.

You can override defaults without copying & pasting code, ever. This is documented in [Configuring MVC](chapters/config/overview.md).

## Customising a specialised class

Unlike plain old Joomla! you are NOT supposed to copy and paste code when dealing with FOF. Our rule of thumb is that if you ever find yourself copying code from FOFModel into your extension's specialised model class you're doing it wrong.

FOF models can be customised very easily using the `onBeforeSomething` / `onAfterSomething` methods. The `Something` is the name of the model method they are related to. For example, `onBeforeSave` runs before the `save()` method executes its actions and `onAfterSave` runs right after the `save()` method executes its actions. Specific implementation notes for each case can be found in the docblocks of each event method.

## Customising using plugin events

FOF models are designed to call certain plugin events (of "content" plugins) upon certain actions. The events are defined in the model's protected properties as follows:

**event_before_delete** (default: onContentBeforeDelete) is triggered before a record is deleted.

**event_after_delete** (default: onContentAfterDelete) is triggered after a record is deleted.

**event_before_save** (default: onContentBeforeSave) is triggered before a record is saved.

**event_after_save** (default: onContentAfterSave) is triggered after a record is saved.

**event_change_state** (default: onContentChangeState) is triggered after a record changes state, i.e. it's published, unpublished etc.

**event_clean_cache** (default: none; doesn't run) is triggered when FOF is cleaning the cache.

Moreover, if you are using XML forms you will also see the **onContentPrepareForm** event which runs when the form is being pre-processed before rendering.

These are the same as the standard Joomla! plugin events. This ensures that a plugin written for a core Joomla! component can easily be extended to handle FOF components as well.

Whenever Joomla! requires us to pass a context to the plugin events we use the conventions `component.view` e.g. `com_todo.items` for a component name com_todo and a model for the items view.