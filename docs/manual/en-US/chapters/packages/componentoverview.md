Overview of a component
-----------------------

FOF is an MVC framework in heart and soul. It tries to stick as close as
possible to the MVC conventions put forward by the Joomla! CMS since
Joomla! 1.5, cutting down on unnecessary code duplication. The main
premise is that your code will be DRY – not as the opposite of “wet”,
but as in Don’t Repeat Yourself. Simply put: if you ever find yourself
trying to copy code from a base class and paste it into a specialized
class, you are doing it wrong.

In order to achieve this code isolation, FOF uses a very flexible
structure for your components. A component's structure looks like this:

![](images/component-structure.png)

The Dispatcher is the entry point of your component. Some people would
call this a "front Controller" and this is actually what it is. It's
different than what we typically call a Controller in the sense that the
Dispatcher is the only part of your component which is supposed to
interface the underlying application (e.g. the Joomla! CMS) and gets to
decide which Controller and task to execute based on the input data
(usually this is the request data). No matter if you call it an entry
point, front controller, dispatcher or Clint Eastwood its job is to
figure out what needs to run and run it. We simply chose the name
"Dispatcher" because, like all conventions, we had to call it something.
So, basically, the Dispatcher will take a look at the input data, figure
out which Controller and task to run, create an instance of it, push it
the data and tell it to run the task. The Controller is expected to
return the rendered data or a redirection which the Dispatcher will
dully pass back to its caller.

Oh, wait, what is a Controller anyway?! Right below the Dispatcher you
will see a bunch of stuff grouped as a "triad". The "triad" is commonly
called "view" (with a lowercase v). Each triad does something different
in your component. For example, one triad may allow you to handle
clients, another triad allow you to handle orders and so on. Your
component can have one or more triads. A triad usually contains a
Controller, a Model and a View, hence the name ("triad" literally means
"a bunch of three things"). The only mandatory member is the Controller.
A triad may be reusing the Model and View from another triad – which is
another reason why DRY code rocks– or it may even be view-less. So, a
triad may actually be a bunch of one, two or three things, as long as it
includes a Controller. Just to stop you from being confused or thinking
about oriental organised crime and generally make your life easier we
decided to call these "views" (with a lowercase v) instead of "triads".
See? Now it is so much better.

FOF views follow the "fat Model - thin Controller" paradigm. This means
that the Controller is a generally minimalist piece of code and the
Model is what gets to do all the work. Knowing this very important bit
of information, let's take a look at the innards of a view.

In the very beginning we have the Controller. The Controller has one or
more tasks. Each task is an action of your component, like showing a
list of records, showing a single record, publish a record, delete a
record and so on. With a small difference. The Controller's tasks do not
perform the actual work. They simply spawn an instance of the Model and
push it the necessary data it needs. This is called "setting the state"
of the Model. In most cases the Controller will also call a Model's
method which does something. It's extremely important to note that the
Controller will work with *any* Model that implements that method and
that the Model is oblivious to the Controller. Then the Controller will
create an instance of the View class, pass it the instance of the Model
and tell it to go render itself. It will take the output of the View and
pass it back to the Dispatcher.

Which brings us to the Model. The Model is the workhorse of the view. It
implements the business logic. All FOF Models are passive Models which
means that they are oblivious to the presence of the Controller and
View. Actually, they are completely oblivious to the fact that they are
part of a triad. That's right, Models can be used standalone, outside
the context of the view or component they are designed to live in. The
Model's methods will act upon the state variables which have already
been set (typically, by the Controller) and will only modify the state
variables or return the output directly. Models must never have to deal
with input data directly or talk to specific Controllers and/or Views.
Models are decoupled from everything, that's where their power lies.

Just a small interlude here. Right below the Model we see a small thing
called a "Table". This is a strange beast. It's one part data adapter,
one part model and one part controller, but nothing quite like any of
this. The Table is used to create an object representing a single
record. It is typically used to check the validity of a record before
saving it to the database and post-process a record when reading it from
the database (e.g. unserialise a field which contains serialised or JSON
data).

The final piece of our view is the View class itself. It will ask the
Model for the raw data and transform it into a suitable representation.
Typically this means getting the raw records from the Model and create
the HTML output, but that's not the only use for a View. A View could
just as well render the data as a JSON stream, a CSV file, or even
produce a graphic, audio or video file. It's what transforms the raw
data into something useful, i.e. it's your presentation layer. Most
often it will do so by loading view templates which are .php files which
transform raw data to a suitable representation. If you are using the
XML forms feature of FOF, the View will ask the Model to return the form
definition and ask FOF's renderer to render this to HTML instead. Even
though the actual rendering is delegated to the Renderer (not depicted
above), it's still the View that's responsible for the final leg of the
rendered data: passing it back to its caller. Yes, the View will
actually neither output its data directly to the browser, nor talk to
the underlying application. It returns the raw data back to its caller,
which is almost always the Controller. Again, we have to stress that the
View is oblivious to both the Controller and the Model being used. A
properly written View is fully decoupled from everything else and will
work with any data provider object implementing the same interface as a
Model object and a caller which is supposed to capture its output for
further consumption.

> **Important**
>
> All classes comprising a view are fully decoupled. None is aware of
> the internal workings of another object in the same or a different
> view. This allows you to exchange objects at will, promoting code
> reuse. Even though it sounds like a lot of work it's actually less
> work and pays dividends the more features you get to add to your
> components.

There's another bit mentioned below the triad, the Toolbar. The Toolbar
is something which conceptually belongs to the component and only has to
do with views being rendered as HTML. It's what renders the title in the
back-end, the actions toolbar in the front- or back-end and the
navigation links / menu in the back-end. In case you missed the subtle
reference: FOFToolbar allows you to render an actions toolbar even in
the front-end of your component, something that's not possible with
plain old Joomla!. You will simply need to add some CSS to do it.

Finally we mention the Helpers. The Helpers are pure static classes
implementing every bit of functionality that's neither OOP, nor can it
be categorised in any other object already mentioned. For example,
methods to render drop-down selection lists. In so many words, "Helper"
is a polite way of saying "non-OOP cruft we'd rather not talk about".
Keep your Helpers to a minimum as they're a royal pain in the rear to
test.

Please do keep in mind that this is just a generic overview of how an
FOF-based component works. The real power comes from the fact that you
don't need to know the internal workings of FOF to use it, you don't
need to copy and paste code from it (woe is the developer who does that)
and quite possibly you don't even need to write any code. At all. It's
all discussed later on.