What is the `$config` array?
----------------------------

You may have observed that FOF’s MVC classes can be passed an optional
array parameter `$config`. This is a hash array with configuration
options. It is being passed from the Dispatcher to the Controller and
from there to the Model, View and Table classes. Essentially, this is
your view (MVC triad) configuration. Setting its options allows you to
modify FOF’s internal workings without writing code.

The various possible settings are explained in [The configuration
settings](chapters/config/configsettings.md) section below.