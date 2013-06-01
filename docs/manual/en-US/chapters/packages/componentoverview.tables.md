Tables
----

Tables are strange beasts. They are part data adapter, part model and part controller. Confused? They are used to create an object representing a single record of a database table. They're typically used to check the validity of a record before saving it to the database and post-process a record when reading it from the database (e.g. unserialise a field which contains serialised or JSON data). They can come in very handy to perform automated ("magic") actions when creating / modifying / loading a database record.

## Class and file naming conventions

The conventions for naming the table classes is `ComponentTableView`, e.g. `TodoTableItem` for a component named com_todo and a view named items. The last part MUST be singular. It's logical: a table class operates on a single record, ergo it's singular.

The table file MUST match the last part of the class name. This means that the file for `TodoTableItem` MUST be `item.php`.

All Table files are located in your component's `tables` directories in the back-end. If the Table class is not loaded and a suitable file cannot be found FOF will fall back to creating a suitable configure instance of FOFTable, using convention over configuration (explained below) to determine what the table object should do.

## Database table naming conventions

It's exactly as described in the [Model](chapters/packages/componentoverview.models.md) reference.

## Magic fields

Magic fields have special meaning for FOF. They are:

- **title** The title of an item. It's used for creating a slug.
- **slug** That's the alias of an item, typically used as part of generated URLs by your components. By default, it will be generated out of the title using a very basic transliteration algorithm.
- **enabled** Is this record published or not? It's like the published column in core Joomla! components, but is only supposed to take values of 0 (disabled) and 1 (enabled).
- **ordering** The sort order of the record.
- **created_by** The ID of the user who created the record. Handled automatically by FOF.
- **created_on** The date when the record was created. Handled automatically by FOF.
- **modified_by** The ID of the user who last modified the record. Handled automatically by FOF. 
- **modified_on** The date when the record was last modified. Handled automatically by FOF.
- **locked_by** The ID of the user who locked (checked out) the record for editing. Handled automatically by FOF.
- **locked_on** The date when the record was locked (checked out) for editing. Handled automatically by FOF.
- **hits** How many read hits this record has received. Handled automatically by FOF.
- **asset_id** The ID in the `#__assets` table for the record. Handled automatically by FOF. Only required if you want per item ACL privileges.

You can always customise the magic fields' names in your Table class using the _columnAlias array property. For example, if your column is named published instead of enabled:

	$this->_columnAlias['enabled'] = 'published';

Now FOF will know that the published column contains the publish status of the record. This comes in handy when you're upgrading a component from POJ (plain old Joomla!) to FOF.

## Customising a specialised class

Unlike plain old Joomla! you are NOT supposed to copy and paste code when dealing with FOF. Our rule of thumb is that if you ever find yourself copying code from FOFTable into your extension's specialised table class you're doing it wrong.

FOF tables can be customised very easily using the `onBeforeSomething` / `onAfterSomething` methods. The `Something` is the name of the table method they are related to. For example, `onBeforeBind` runs before the `bind()` method executes its actions and `onAfterBind` runs right after the `bind()` method executes its actions. Specific implementation notes for each case can be found in the docblocks of each event method.

## Customising using plugins

You can customise the actions of tables by using standard "system" plugins. FOFTable will automatically create plugin events using a fixed naming prefix and appending them with the last part of the table's name. For example, if you hav a table called `TodoTableItem` FOF will attempt to run a system plugin event called `onBeforeBindItem`. For the sake of documentation we will be using the suffix TABLENAME.

The obvious drawback is the possibility of naming clashes. For example, given two tables `TodoTableItem` and `ContactusTableItem` the event to be called before binding data to either table is called `onBeforeBindItem`. How can you distinguish between the two cases? The first parameter passed to the plugin event handler is a reference to the table object itself, by convention called `$table`. You can do a `$table->getTableName()` which returns something like "#__todo_items". Just check if it's the database table you expect to be interacting with. If not, just return true to let FOF do its thing uninterrupted.

The complete list of events is:

**onBeforeBindTABLENAME** is triggered before binding data from an array/object to the table object.

**onAfterLoadTABLENAME** is triggered after a record is loaded

**onBeforeStoreTABLENAME** is triggered before a record is saved to the table

**onAfterStoreTABLENAME** is triggered after a record has been saved to the table

**onBeforeMoveTABLENAME** is triggered before a single record is moved (reordered)

**onAfterMoveTABLENAME** is triggered before a single record is moved (reordered)

**onBeforeReorderTABLENAME** is triggered before a new ordering is applied to multiple records of the table

**onAfterReorderTABLENAME** is triggered before a new ordering is applied to multiple records of the table

**onBeforeDeleteTABLENAME** is triggered before a record is deleted

**onAfterDeleteTABLENAME** is triggered after a record has been deleted

**onBeforeHitTABLENAME** is triggered before registering a read hit on a record

**onAfterHitTABLENAME** is triggered after registering a read hit on a record

**onBeforeCopyTABLENAME** is triggered before copying (duplicating) a record

**onAfterCopyTABLENAME** is triggered after copying (duplicating) a record

**onBeforePublishTABLENAME** is triggered before publishing a record

**onAfterResetTABLENAME** is triggered after we have reset the table object's state

**onBeforeResetTABLENAME** is triggered before resetting the table object's state

If you return boolean false from an onBefore event the operation is cancelled.

As you can easily understand this is an extremely powerful feature as it allows end users and site integrators (of a power user level, granted) to modify or extend the behaviour of FOF-powered extensions with great ease.