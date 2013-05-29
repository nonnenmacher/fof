XML Forms
=========
Traditionally, creating view templates involves a .php file where PHP and HTML code are intermixed to create the appropriate representation of the data to be served to a web browser. While this gives maximum flexibility to the developer it is also a drag, requiring you to write a lot of repetitive code.

Joomla! 1.6 and later is providing a solution to this problem, at least for edit views: JForm. With it it's possible to create an XML file which defines the controls of the form and have JForm render it as HTML.

Pros:

-	The view templates are easier to read
-	The HTML generation is abstracted, making it easier to upgrade to newer versions of Joomla! using a different HTML structure

Cons:

- You need to change your Controllers, Models and Views to cater for and display the forms
- They only apply to edit views

FOF takes this concept further with the FOFForm package. Not only can you create edit views, but you can also create browse (records listing) and read (single record display) views out of XML forms. Moreover, the forms are handled automatically by the FOF base MVC classes without requiring you to write any additional code. If you want you can always combine a traditional .php view template with a form file for maximum customisation of your view.