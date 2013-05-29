Models
----

The Model is the workhorse. Business logic goes here. Models never interface input data directly or output data. They are supposed to read data from their state and push the results to their state.

## Naming conventions for classes and files

The convention for naming the model classes is `ComponentModelView`, e.g. `TodoModelItems` for a component named com_todo and a view named items. The last part SHOULD be plural. Support for singular named models (such as TodoModelItem) will be dropped in a future version.

The model file MUST match the last part of the class name. This means that the file for `TodoModelItems` MUST be `items.php`, whereas the file for `TodoModelItem` MUST be `item.php`.

All Model files are located in your components `models` directories, in the front-end and back-end. If a file is not present in the front-end, it will be attempted to be loaded from the back-end and vice versa. If the Model class is not loaded and a suitable file cannot be found FOF will fall back to one of the following, in this order:

1. The Default model. This is a special model class following the naming conventions `ComponentModelDefault`, e.g. `TodoModelDefault`, found in the `default.php` file inside your models directory.
2. If a default model is not found, FOF will fall back to creating a suitably configured instance of FOFModel, using convention over configuration (explained below) to determine what the model object should do.

## Database table naming conventions

All FOF models connect, by default, to a database table. You can of course have a model whose corresponding table doesn't exist as long as you do not use its default data processing methods.

Database tables are named as `#__component_view`, e.g. `#__todo_items` for a component named com_todo and a view named items.

The auto increment field is named `component_view_id`, e.g. `todo_item_id` for a component named com_todo and a view named items. If your table does not have an auto incrementing field you will not be able to use the default implementation of FOF's data processing methods.

You can override defaults without copying & pasting code, ever. This is documented in [Configuring MVC](chapters/config/overview.md).

## Magic fields

Magic field are automatically updated by FoF as long as they exist in your extension table(s):
* slug
* enabled
* ordering
* created_by
* created_on
* modified_by
* modified_on
* locked_by
* locked_on
* hits

There is also another special field that, if present, enables the automatic asset tracking:

* asset_id

>Tip: You can always customise the magic fields' names in your Table class using the _columnAlias array property. For example, if your column is named published instead of enabled:
>
    $this->_columnAlias['enabled'] = 'published';
> Now FOF will know that the published column contains the publish status of the record. This comes in handy when you're upgrading a component from POJ (plain old Joomla!) to FOF.

Magic fields are further explained in [Tables](chapters/packages/componentoverview.tables.md)