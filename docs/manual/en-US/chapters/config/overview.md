Configuring the MVC and associated classes
==========================================

All MVC and associated classes in FOF (Dispatcher, Controller, Model,
View, Table, Toolbar) come with a default behavior, for example where to
look for model files, how to handle request data and so on. While this
is fine most of the times –as long as you follow FOF’s conventions– this
is not always desirable.

For example, if you are building a CCK (something like K2) you may want
to look for view templates in a non-standard directory in order to
support alternative “themes”. Or, maybe, if you're building a contact
component you only want to expose the add view to your front-end users
so that they can file a contact request but not view other people's
contact requests. You get the idea.

The traditional approach to development prescribes overriding classes,
even to the extent of copying and pasting code. If you've ever attended
one of my presentations you've probably figured that I consider copying
and pasting code a mortal sin. You may have also figured that, like all
developers, I am lazy and dislike writing lots of code. Naturally, FOF
being a RAD tool it provides an elegant solution to this problem. The
`$config` array and its sibling, the `fof.xml` file.