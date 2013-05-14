Key features
------------

Some of the key features / highlights of FOF:

####Convention over configuration, Rails style. 
Instead of having to
painstakingly code every single bit of your component, it's sufficient
to use our naming conventions, inspired by Ruby on Rails conventions.
For example, if you have com\_example, the foobar view will read from
the \#\_\_example\_foobars table which has a unique key named
example\_foobar\_id. The default implementation of controllers, models,
tables and views will also cater for the majority of use cases,
minimising the code you'll need to write.

####HMVC today, without relearning component development.
There's a lot of talk about the need to re-engineer the MVC classes in Joomla! to
support HMVC. What if we could give you HMVC support using the existing
MVC classes, today, without having to relearn how to write components?
Yes, it's possible with FOF. It has been possible since September 2011,
actually. And for those who mumble their words and spread FUD, yes, it
IS HMVC by any definition. The very existence of the FOFDispatcher class
proves the point.

####Easy reuse of view template files without ugly include(). 
More often than not you want to reuse view template files across views. The
"traditional" way was by using include() or require() statements. This
meant, however, that template overrides ceased working. Not any more!
Using FOFView's loadAnyTemplate() you can load any view template file
from the front- or back-end of your component or any other component,
automatically respecting template overrides.

####Automatic language loading and easy overrides.
Are you sick and
tired of having to load your component's language files manually? Do you
end up with a lot of untranslated strings when your translators don't
catch up with your new features? Yes, that sucks. It's easy to overcome.
FOF will automatically handle language loading for you.

####Media files override (works like template overrides).
So far you
knew that you can override Joomla!'s view template files using template
overrides. But what about CSS or Javascript files? This usually required
the users to "hack core", i.e. modify your views' PHP files, ending up
in an unmaintainable, non-upgradeable and potentially insecure solution.
Not any more! Using FOF's FOFTemplateUtils::addCSS and
FOFTemplateUtils::addJS you can load your CSS and JS files directly from
the view template file. Even better? You can use the equivalent of
template overrides to let your users and template designers override
them with their own implementations. They just have to create the
directory templates/your\_template/media/com\_example to override the
files normally found in media/com\_example. So easy!

####Automatic JSON and CSV views with no extra code (also useful for web services). 
Why struggle to provide a remote API for your component?
FOF makes the data of each view accessible as JSON feeds opening a new
world of possibilities for Joomla! components (reuse data in mobile
apps, Metro-style Windows 8 tiles, browser extensions, mash-up web
applications, ...). The automatic CSV views work on the same principle
but output data in CSV format, suitable for painlessly data importing to
spreadsheets for further processing. Oh, did we mention that we already
have an almost RESTful web services implementation?

####No code view templates. 
Don't you hate it that you have to write a
different view template (in PHP and HTML) for each Joomla! version and,
worse, each template out there? Don't you hate it having to teach
non-developers how to not screw up your component with every update you
publish? We feel your pain. That's why FOF supports the use of XML files
as view templates, rendering them automatically to HTML. Not just forms;
everything, including browse (multiple items) and single item views.
Even better, you get to choose if you want to use traditional PHP/HTML
view templates, XML view templates or a combination of both, even in the
same view!

####No code routing, ACL and overall application configuration.
Since
FOF 2.1 you can define your application's routing, access control
integration and overall configuration without routing any code, just by
using a simple to understand XML file. It's now easier than ever to have
Joomla! extensions with truly minimal (or no) PHP code.