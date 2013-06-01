Views
----

The Views are the last wheel of an MVC triad. Their sole purpose in life is to render the data in a suitable representation that makes sense. Usually this means rendering to HTML but they can also be used to render the data as JSON, XML, CSV or even as images, sound and video. It's up to you to decide what a "suitable" representation means in the context of your application.

## Class and file naming conventions

The convention for naming the view classes is `ComponentViewViewname`, e.g. `TodoViewItem` for a component named com_todo and a view named item. The last part MUST match the singular/plural name of the specific view you are rendering.

The view file name MUST follow the convention `view.FORMAT.php`, e.g. `view.html.php`. The FORMAT is the represantation format rendered by this view class. The most common formats –for which FOF provides default implementations– are html, json and csv. The format MUST match the value of the "format" input variable. If none is specified, "html" will be assumed. Exception: there is a format called "form" which is an HTML rendering and will be loaded when the value of the format input variable is set to "html" (or not set at all) as long as there is an XML form for this view.

All View files are located in your component's `views` directories, in the respective front-end and back-end directory, inside the respective view subfolder. For example, if you have a component called com_todo and a back-end view named items the view file for the HTML rendering is `administrator/components/com_todo/views/items/view.html.php`

If a file is not present in the front-end, it will be attempted to be loaded from the back-end and vice versa.  If the View class is not loaded and a suitable file cannot be found FOF will fall back to one of the following, in this order:

1. The Default view. This is a special controller class following the naming conventions `ComponentViewDefault`, e.g. `TodoViewDefault`, found in the `default/view.FORMAT.php` file inside your views directory.
2. If a default view  is not found, FOF will fall back to creating a suitably configured instance of a FOFView format-specific class, using convention over configuration to determine what the model object should do. For example, if th ecurrent format is html FOF will create an instance of FOFViewHtml. If there is no suitable class found you will get an error as FOF has no idea what to render.

## View template files and their location

FOF uses default names to generate a list or form to edit, these names are linked to the task being executed.

    Task name    Filename                           Description
    browse       default.php  OR  form.default.xml  This is the file that shows the list page
    edit         form.php  OR  form.form.xml        This is the file that shows the edit page
    read         item.php  OR  form.item.xml        This is the file that shows the data without any markup

The  location of the files is also pre-defined and based on the view name. This name comes in both singular and plural form where the singular name represents the edit page and the plural name represents the list page. Let's say our view is called todo, the template files can be found in the following location:

    views
    |-- todo
     |-- tmpl
         form.php  OR  form.form.xml
         item.php  OR  form.item.xml
    |-- todos
     |-- tmpl
         default.php  OR  form.default.xml

## Customising a specialised class

Unlike plain old Joomla! you are NOT supposed to copy and paste code when dealing with FOF. Our rule of thumb is that if you ever find yourself copying code from any FOFView class into your extension's specialised table class you're doing it wrong.

FOF controller can be customised very easily using the `onTASK` methods. The `TASK` here is the name of the controller task they are related to. For example, `onBrowse` runs when rendering the output of a browse task. There is a catch-all method called `onDisplay` which executes if no suitable method is found in the view class. Returning false from these methods will result in a 403 Forbidden error.

## Layouts, sub-templates and template overrides

The default filename of the template file to be used can be overriden with the layout input variable. For example if you feed an input variable named "layout" with a value of "foobar" FOF will look for "form.foobar.xml" and "foobar.php" in the tmpl directory.

You can also specify sub-templates using the tpl parameter when calling the display() method of your view class. By default FOF doesn't use it at all. You can only use it with custom controller tasks. In this case the tpl (a.k.a. subtemplate) will be appended to the layout name with an underscore in between. So for layout=foo and tpl=bar FOF will be looking for the "foo_bar.php" view template file.

All view template files are subject to template overrides. The view template will first be searched in the  `templates/TEMPLATE/html/COMPONENT/VIEW` directory where TEMPLATE is the name of your template, COMPONENT is the name of your component (e.g. com_todo) and VIEW is the name of your view. This allows end users and site integrators to provide customised renderings suitable for their sites.

# Joomla! version specific overrides

It is possible have different view templates per Joomla! version or version family. The correct view template is chosen automatically, without you writing a single line of code.

Let's say that you have a browse view with your lovely default.php view template file. And you want your component to work on Joomla! 2.5 and 3.x. Oh, the horror! The markup is different for each Joomla! version, Javascript has changed, different features are available… Well, no problem! FOF will automatically search for view template files (or XML forms) suffixed with the Joomla! version family or version number.

For example, if you're running under Joomla! 2.5, FoF will look for `default.j25.php`, `default.j2.php` and `default.php` in this order. If you're running under Joomla! 3.2, FOF will look for `default.j32.php`, `default.j3.php` and `default.php` in this order. This allows you to have a different view template file for each version family of Joomla! without ugly if-blocks and awkward code.

This feature also works with XML forms, e.g. on Joomla! 2.5 a browse form will be looked for in `form.default.j25.xml`, `form.default.j2.xml` and `form.default.xml` in this order.

## Automatic views and web services

FOF can automatically render your component's output in JSON and CSV formats. You do not have to write any code whatsoever. Just pass on an input variable named "format" with a value of "json" or "csv" respectively. In the typical case where you get the input variables from the request this means appending `&format=json` or `&format=csv` resepctively. YOu can, of course, customise the output of either format using view classes if you need to.

The JSON format can be used to provide web services with integrated hypermedia (following the HAL specification). All you need to do is to tell FOFViewJson to use hypermedia, either by setting `$this->useHypermedia = true;` in your specialised JSON view class or, much easier, using the fof.xml configuration file.