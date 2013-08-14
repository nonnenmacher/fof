HMVC
----

Before we say anything else, let's define what HMVC means in the context of FOF. The H stands for "Hierarchical". That is to say there's a hierarchy of MVC calls. In very simple terms, HMVC allows you to call an MVC triad from anywhere else.

Practical uses:

* Showing a component's view inside a module, without having to rewrite the model and view logic inside the module.
* Allowing a plugin (e.g. a system or content plugin) to use the rendered output of a component and inject it to the output of the page or send it as an email.
* Displaying a view of the same or a different component within a component.

The possibilities are endless.

## How to use it?

You already know it, you just didn't realise it. Here's the secret sauce:

	FOFDispatcher::getTmpInstance('com_foobar', 'items', array('layout' => 'fancy'))->dispatch();
	
You are simply creating an instance of the dispatcher of the component you want, telling it which view to render and giving it an option configuration array (the last argument in the method call). Then you just call the dispatch() method and let it render.

If you want to get the output in a variable you have to do something like this:

	@ob_start();
	FOFDispatcher::getTmpInstance('com_foobar', 'items', array('layout' => 'fancy'))->dispatch();
	$result = ob_end_clean();

If you need to pass input variables to the dispatcher you can do something like this:

	FOFDispatcher::getTmpInstance('com_foobar', 'items', array('input' => $input))->dispatch();

where `$input` can be an indexed array, a stdClass object or –preferred– a FOFInput or JInput instance. For example:

	$inputvars = array(
		'limit'			=> 10,
		'limitstart'	=> 0,
		'foobar'		=> 'baz'
	);
	$input = new FOFInput($inputvars);
	FOFDispatcher::getTmpInstance('com_foobar', 'items', array('input' => $input))->dispatch();

And, of course, you can mix and match all of the above ideas to something like:

	$inputvars = array(
		'limit'			=> 10,
		'limitstart'	=> 0,
		'format'		=> 'json'
	);
	$input = new FOFInput($inputvars);
	@ob_start();
	FOFDispatcher::getTmpInstance('com_foobar', 'items', array('input' => $input))->dispatch();
	$json = ob_end_clean();

See the awesome thing we just did? We got the first 10 items of com_foobar in JSON format in the $json variable. Just a side note. This example also screws up the document MIME type if you use it in an HTML view. Be warned.