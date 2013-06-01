Dispatcher
----

The Dispatcher is what handles the request on behalf of your component (be it a web request or an HMVC request). Its primary job is to decide which controller to create and which task to run. Its secondayr job is to handle transparent authentication which comes in really handy if you want to perform remote requests to your component, interacting with access-restricted data or actions (viewing items protected behind a login, performing privileged operations such as creating / editing / deleting records and so on).

## Class and file naming conventions

The convention for naming the dispatcher classes is `ComponentDispatcher`, e.g. `TodoDispatcher` for a component named com_todo. The last part MUST be Dispatcher.

The controller file name MUST be dispatcher.php. All Dispatcher files are located in your component's main front-end or back-end directories. If a file is not present in the front-end, it will be attempted to be loaded from the back-end but NOT vice versa. If the Dispatcher class is not loaded and a suitable file cannot be found FOF will fall back to creating a suitably configured instance of FOFDispatcher, using convention over configuration to determine what the Dispatcher object should do.

## Customising a specialised class

Unlike plain old Joomla! you are NOT supposed to copy and paste code when dealing with FOF. Our rule of thumb is that if you ever find yourself copying code from FOFDispatcher into your extension's specialised table class you're doing it wrong.

FOF dispatcher can be customised very easily using the `onBeforeDispatch` / `onAfterDispatch` methods. `onBeforeDispatch` runs before the dispatcher executes and `onAfterDispatch` runs right after the dispatcher executes. Returning false will result in a 403 Forbidden error. Specific implementation notes for each case can be found in the docblocks of each event method.

## Transparent authentication

Transparent authentication allows FOF to authenticate a user using Basic Authentication or URL parameters. This allows you to create web services or directly access pages which require a logged in users without using Joomla! session cookies.

The authentication credentials can be provided via two methods: Basic Authentication or a URL parameter. The authentication credentials can either be a username and password pair transmitted in plaintext (not recommended unless you are forcibly using HTTPS with a commercially signed SSL certificate) or encrypted. The encrypted information uses Time-Based One Time Passwords (TOTP) to allow you to communicate the credentials securely, without the burden of public key cryptography, while at the same time maintaining an intrinsically very narrrow window of opportunity. Furthermore, since the effective encryption key is modified every few seconds it makes an attack against it slightly harder than using regular symmetric AES-128 cryptography.

Transparent authentication is enabled by default, but doesn’t use TOTP.

### Setting it up

Setting up transparent authentication requires you to modify your component’s Dispatcher class, namely its __construct(), to change the values of some protected fields.

The available fields are:

* **$_fofAuth_timeStep**. The time step, in seconds, for the time based one time passwords (TOTP) used for encryption. The default value is 6 seconds. The window of opportunity for an attacker is 2x-3x as much, i.e. 12-18 seconds using the default value. This is adequately high to be practical and too low to allow a realistic attack by a hacker.
    **WARNING!** If you change this option you have to notify the consumers of the service to make the same change, otherwise your TOTPs will be vastly different and communication will fail.
* **$_fofAuth_Key**. The Base32 encoded key for TOTP. Please note that this is Base32, not Base64. Only required if you’re going to use encryption.
* **$_fofAuth_Formats**. Which result formats should be handled by the transparent authentication. This is an array, by default array('json', 'csv', 'xml', 'raw'). We recommend only using non-HTML formats in here.
* $_fofAuth_LogoutOnReturn. By default it’s true and it means that once the component finishes executing, FOF will log out the user it authenticated using transparent authentication. This is a precaution against someone intercepting and abusing the session cookie Joomla! will be sending back to the client, as well as preventing the sessions table from filling up.
* **$_fofAuth_AuthMethods**. An array of supported authentication methods. Only use the ones that make sense for your application. Avoid using the *_Plaintext ones, please. The possible values in the array are:
    * HTTPBasicAuth_TOTP  HTTP Basic Authentication using encrypted information protected with a TOTP (the username must be "_fof_auth")
    * QueryString_TOTP  Encrypted information protected with a TOTP passed in the _fofauthentication query string parameter
    * HTTPBasicAuth_Plaintext  HTTP Basic Authentication using a username and password pair in plain text
    * QueryString_Plaintext  Plaintext, JSON-encoded username and password pair passed in the _fofauthentication query string parameter

When you are using the QueryString_TOTP method you can pass your authentication information as GET or POST variable called _fofauthentication with the value being the URL encoded cryptogram of the authentication credentials (see further down).

### How to get a TOTP key

Any Base32 string can be used as a TOTP key as long as it expands to exactly 10 characters. If you don’t feel like guessing, you can simply do:

    $totp = new FOFEncryptTotp();
    $secret = $totp->generateSecret();

You have to share this secret key with all clients wishing to connect to your component via a secure channel. This secret key musy also be set in the _fofAuth_Key variable.

### How to construct and supply an authentication set

The authentication set is a representation of the username and password of the user you want FOF to log in using transparent authentication. Its format depends on the authentication method.

Before going into much detail, we should consider an FOF authentication key to be a JSON-encoded object containing the keys username and password. E.g.:

    { “username”: “sample_user”, “password”: “$3Cr3+” }

This is used with all but one authentication methods. Encryption of the FOF authentication key, used with all *_TOTP methods, is discussed further down this document.

If you are using HTTPBasicAuth_Plaintext method, you have to supply your username and password using HTTP Basic Authentication. The username is the username of the user you want to log in and the password is the password of the user you want to log in. This is the easiest and most insecure authentication method.

If you are using the HTTPBasicAuth_TOTP method, you have to supply a username of _fof_auth (including the leading underscore) and as the password enter the encrypted FOF authentication key.

If you are using the QueryString_Plaintext method you have to supply a GET or POST query parameter with a name of _fofauthentication (including the leading underscore). Its value must be the URL encoded FOF authentication key.

If you are using the QueryString_TOTP method you have to supply a GET or POST query parameter with a name of _fofauthentication (including the leading underscore). Its value must be the URL encoded FOF authentication key.

### Encrypting the FOF authentication key

Assuming you are doing this from a FOF-powered component, you can do something like this:

    $timeStep = 6; // Change this if you have a different value in your Dispatcher
    $authKey = json_encode(array(
      'username' => $username,
      'password' => $password
    ));
    $totp = new FOFEncryptTotp($timeStep);
    $otp = $totp->getCode($secretKey);
    $cryptoKey = hash('sha256', $this->_fofAuth_Key.$otp);
    $aes = new FOFEncryptAes($cryptoKey);
    $encryptedAuthKey = $aes->encryptString($authKey);

If you can get your hands on a TOTP and AES-256 implementation for your favourite programming language you can use talk to FOF-powered components through transparent authentication. Tip: TOTP libraries are usually labelled as being Google Authenticator libraries. Google Authenticator simply uses TOTP with a temp step of 30 seconds. Most such libraries are able to change the time step, thus possible to use with FOF. In fact, that's how FOF's TOTP library was derived.