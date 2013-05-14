### FOFDispatcher

**Instantiating and using the Dispatcher**

Following Joomla conventions, at the root of both the front- and
back-end of an extension is the entry point for that end of the
extension, named following the extension’s name.

For example, for com\_foobar, we would create foobar.php in the root of
each end. These files are not dispatchers, but they are the points in
the extension that include the FOF library, and create an instance of
FOF's dispatcher.

FOF is included with:

    // Load FOF
    include_once JPATH_LIBRARIES . '/fof/include.php';
    if (!defined('FOF_INCLUDED')) {
        JError::raiseError('500', 'FOF is not installed');
    }

Creating an instance of FOF's dispatcher for com\_foobar would be done
with:

    FOFDispatcher::getTmpInstance('com_foobar')->dispatch();

**What FOFDispatcher does**

FOFDispatcher's main concern is routing your component. It takes a look
at the input data and decides which Controller to instantiate and, if
there is no task, figure out which task you meant to use. Even though
it's commonly used with the request data, it can also operate on
arbitrary input data passed to it through the `$config['input']`
variable. This variable can be a simple hash array (array of key/value
pairs) or a FOFInput object. In any case it will be converted to a
FOFInput object and passed on to the other classes (Controller, Model,
Table, View) *by reference* so that they all operate on the same set of
data.

**Convention over Configuration**

The first thing you should know about is the naming convention used. All
Dispatchers are named after the component (without the com\_ prefix) and
the word Dispatcher. For example, com\_foobar's dispatcher is called
FoobarDispatcher. If you do not provide a specialised dispatcher class,
FOF will provide you with an automatically generated one.

You should be aware how the view (therefore the Controller) to be used
is selected when there is no view parameter defined in the input data.
FOF will use the `defaultView` variable which, by default, contains the
value `cpanel`. If you have defined a `default_view` parameter in
`fof.xml` it will use that value instead.

Another thing you should know is how the task is selected when you don't
specify one. Following the RESTful conventions, the task is chosen based
on the view name and HTTP verb used in the request. The following
possibilities are:

-   a DELETE request calls the delete task, deleting a record.

-   a POST or PUT request will issue the save task, saving or creating a
    record. Due to the poor support of the PUT HTTP verb in PHP we chose
    to map it to POST. If a new record is created, FOF will by default
    return a 201 Created HTTP header, whereas when a record is updated
    it will return a 200 OK HTTP header.

-   a GET request to a plural view (e.g. items) results in the browse
    task being called

-   a GET request to a singular view (e.g item) in the back-end results
    in the edit task being called, as long as you have a non-empty,
    non-zero input variable called id.

-   a GET request to a singular view (e.g item) in the front-end results
    in the read task being called, as long as you have a non-empty,
    non-zero input variable called id.

-   a GET request to a singular view (e.g item) in the front-end results
    in the add task being called, as long as you have an empty or zero
    input variable called id or if you skip it entirely.

Though technically not part of the Dispatcher, the view class to be
instantiated is also defined by the format input parameter. If none is
defined, `html` is assumed, creating an instance of FOFViewHtml. FOF
automatically supports two more formats you should know about, `json`
and `csv`.

**Specialising FOFDispatcher**

Create a file named `dispatcher.php` in your component's base directory.
There is a different dispatcher for the front- and back-end section of
your component.

See the methods below for more information about what you can do.

**FOFDispatcher methods you should know about**

public static function &getTmpInstance(\$option = null, \$view = null,
\$config = array())

Return an instance of a dispatcher object. The parameters are:

\$option
:   Required. The name of the component, e.g. com\_example.

\$view
:   Optional. Which view to instantiate. Use null to let the Dispatcher
    decide.

\$config
:   Optional. The array of configuration variables, discussed in the
    next chapter of this documentation.

public function onBeforeDispatch()

Runs before the dispatcher routes anything. Return true to allow the
dispatcher to run. Return false to have the dispatcher raise an
exception with code 403, usually resulting in a 403 Forbidden HTTP
header being emitted.

public function onBeforeDispatchCLI()
Runs before the dispatcher routes anything when running under the CLI.
Return true to allow the dispatcher to run. Return false to have the
dispatcher raise an exception with code 403, usually resulting in a 403
Forbidden HTTP header being emitted.

public function onAfterDispatch()
Runs after the dispatcher routes everything and before returning control
to the caller. Return true to allow the dispatcher to complete running.
Return false to have the dispatcher raise an exception with code 403,
usually resulting in a 403 Forbidden HTTP header being emitted.

public static function isCliAdmin()

Returns an array with two boolean values. The first one is true if the
component is running under CLI mode. The second one is true if the
component is running under the Joomla! administrator. This is used
throughout FOF to determine whether the component is running inside the
Joomla! front-end, back-end or a CLI app.

#### Calling a Dispatcher from anywhere (a.k.a. HMVC)

You can call a dispatcher from anywhere in Joomla! and let it run the
component, rendering itself. This is useful in master/detail views,
creating modules rendering a component's view, plugins injecting a
component's view to an article... The possibilities are endless.

The main concept is that you need to instantiate the Dispatcher with the
data you need and tell it to Dispatch itself. Typically, this can be
accomplished like this:

    $config = array(
      'input' => array(
        'option' => 'com_example',
        'view' => 'items',
        'layout' => 'funky',
        'limit' => 10,
        'limitstart' => 42,
      )
    );
    FOFDispatcher::getTmpInstance('com_example', 'items', $config)->dispatch();

Do note that it's always a good idea to put the last line inside a
try/catch block, unless you really want to let a possible exception
bubble up to the application.

#### Web services

FOF is currently offering web services, but it's not entirely RESTful
(it lacks hypermedia support). Based on what you have already learned
you probably realised that you can use the HTTP verb, and the view and
format input variables to address everything through web services. For
example, something like
http://www.example.com/index.php?option=com\_example&view=items&format=json
would return a nice JSON-encoded (and paginated) list of records in the
\#\_\_example\_items table. Without writing any code. Not bad, huh?

This is all fine and dandy, but what about the need to authenticate the
user before accessing data? Let's say that we have a doctor's website
with an FOF-based component handling his appointments. We don't want
everyone to see all patients' information but we'd definitely want the
doctor to do so and each patient to have access to her own appointments.
We can easily build that feature in our component using the Model's
post-processing feature (more on that later), but in the end of the day
we need to have a logged in user to figure out who can see what.

Joomla! has a bad habit, like all web apps: it normally needs cookies to
know if you're logged in or not. This isn't a very good idea. In order
to pull your appointments you'd need at least three HTTP requests: one
to get the login form (and the all-necessary form token), one to login
(and get your session cookie) and one to pull the information. Never
mind it's not a RESTful approach, this is an utter hassle to begin with!
There has to be a better way. And there is.

FOF supports five different transparent authentication methods:

-   HTTP Basic Authentication using a username and password pair in
    plain text (a bad idea unless you're using HTTPS)

-   Plaintext, JSON-encoded username and password pair passed in the
    \_fofauthentication query string parameter (a bad idea unless you're
    using HTTPS)

-   Plaintext username and password in the \_fofauthentication\_username
    and \_fofauthentication\_username query string parameters (a bad
    idea unless you're using HTTPS)

-   Encrypted information protected with a TOTP passed in the
    \_fofauthentication query string parameter

-   HTTP Basic Authentication using encrypted information protected with
    a TOTP (the username must be "\_fof\_auth")

FIrst, what does "transparent" mean? Once the Dispatcher sees any of
this data and as long as the corresponding authentication method is
enabled it will try to log you in. If it succeeds, Joomla! now knows you
are logged in without sending you a cookie. The Dispatcher will then
happily route your component and just before it returns it will log you
out. You are being logged in and out of the Joomla! site transparently,
without the remote end having to know that the web service is
implemented using Joomla! and without having to mess around with
cookies. You're welcome!

Now, you might be wondering, what the heck is a TOTP and why is it a
good thing to have? TOTP stands for Time-based One Time Password. It's
the same stuff Google uses in its two-step authentication. Both parties
(the component and the remote client) know a secret key. With this key
and the current date a time a unique six digit password is created,
called a TOTP. This is used, together with some admittedly weakly
implemented AES-128 cryptography, to encrypt your username and password.
FOFDispatcher will decode it using the TOTP it generates itself.
Therefore even though a symmetrical encryption algorithm is used, the
encryption key is not exchanged between the two parties during the
request. The actual secret key can be made known to the remote client
through a secure channel – just think about how Google had you set up
Google Authenticator on your phone.

I know what you're thinking. Replay attacks. What if an attacker sits
between the FOF component and the remote client? The truth is that they
can't accomplish much, for two reasons. First it's the ephemeral nature
of a TOTP. It is only valid for a limited amount of time, 6 seconds by
default. The attacker needs to be really darn fast – not that it's hard
in the age of the WiFi Pineapple. I know, I know, 6 seconds is plenty
for a fast attacker to get back the data we fetched. How do we protect
it? If you use the encrypted authentication method all of your data is
automatically encrypted using the same key. Without the key, the data is
garbled. This is enough to deter simple attacks but of course won't hold
up a lot against a determined and resourceful opponent. Use HTTPS
instead. This kind of encryption implemented in FOF is designed as a
poor man's substitute for proper encryption, not a drop-in replacement.

@TODO Example of encrypted authentication