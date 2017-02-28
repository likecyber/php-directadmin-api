# DirectAdmin API
Alternative PHP DirectAdmin API using Curl extension.

```php
require_once("directadmin.class.php");

$da = new DirectAdmin("https://HOST:2222", "USERNAME", "PASSWORD");
$result = $da->query("CMD_API_SHOW_USER_USAGE");

if (!$da->error) {
	print_r($result);
} else {
	die("Error!");
}
```

---

## Installation
It is pretty simple to utilize this class, you just need to require it.

```php
require_once("directadmin.class.php");
```

## Initialization
Simple initialization. You can even leave it nothing and set it later.

```php
// Initialize with Connect and Login in one function.
// - (string/null) $host : DirectAdmin URL. (HTTP and HTTPS are supported.)
// - (string/null) $username : DirectAdmin Username.
// - (string/null) $password : DirectAdmin Password or Login Key.
// - (bool) $ssl : Enable verify the peer's SSL certificate. (Enable by default.)
$da = new DirectAdmin($host, $username, $password, $ssl);

// Initialize without Connect and Login. (You need to set them later.)
$da = new DirectAdmin();
```
## Set/Change DirectAdmin URL
You can set/change DirectAdmin URL by $this->connect() function.

Please note that it doesn't reset credentials, you don't need to re-login if your credentials still valid.

```php
$da->connect("https://www.example.com:2222");
```

## Set/Change DirectAdmin Credentials
You can set/change DirectAdmin Username and Password by $this->login() function.

Please note that it will reset Login-As, but you can use $this->login_as() function later.

```php
$da->login("username", "password");
```

## Control User
You can Login-As any user under your authority level by $this->login_as() function.

Ths function allows you to control your users without having their passwords.

```php
$da->login_as("username");
```

## Logout from User
You can use $this->logout() function to logout from your Login-As account or even your own account.

If you want to logout on both accounts at the same time, you can use $this->logout(true) like below.

```php
$da->login("first", "password"); // Login on "first" account.
$da->login_as("second"); // Login on "second" account under "first" account.

// Logout from "second" account then logout "first" account again.
$da->logout(); // Logout from "second" account and return back to "first" account.
$da->logout(); // Logout from "first" account and reset DirectAdmin Credentials.
```

```php
$da->login("first", "password"); // Login on "first" account.
$da->login_as("second"); // Login on "second" account under "first" account.

// Logout from both accounts at the same time.
$da->logout(true); // Logout from both accounts and reset DirectAdmin Credentials.
```

## Execute Query
You can execute all commands those available under your authority level by $this->query() function.

```php
// - $command : DirectAdmin Command. (Should starts with "CMD_API_")
// - $form : Fields and Values to execute command. (Should be Array or Null)
// - $method : Method to execute command. (GET/POST)
$result = $da->query($command, $form, $method);
```

It isn't necessary to set $form and $method parameters on some commands.

You can leave it nothing like example below.

```php
// Example for Retrieve the user's usages. (There is no need for $form and $method parameters on this command.)
$result = $da->query("CMD_API_SHOW_USER_USAGE");

// Example for Retrieve the user's usages. (With all parameters)
$result = $da->query("CMD_API_SHOW_USER_USAGE", null, "GET");

// Example for Create subdomain. (sub.example.com)
$result = $da->query("CMD_API_SUBDOMAINS", array(
	"domain" => "example.com",
	"action" => "create",
	"subdomain" => "sub"
), "POST");
```

If the command is starts with "CMD_API_", it'll automatically parse the response to Array.

However, you can do manual parse with $this->parse() function below.

## Manual Parse
You can manual parse Query response to Array by $this->parse() function.

You don't need to parse the response if the command is starts with "CMD_API_".

```php
$da->parse($response);
```

It will not parse the response if it contains "\<html\>" tag.

You can force it to parse even it contains "\<html\>" tag by $this->parse($response, true).

```php
$da->parse($response, true);
```

---

## Editable Variables
You can update these variables to make it works better for you.

- (bool) $this->list_result : Return the list[] as result directly if it exists. (You can turn it off by set this to False.)
- (resource) $this->handle : Curl Handle. (You can set options to this handle.)

## Non-editable Variables
You can use these variables but you shouldn't update them.

- (bool) $this->login : Local Login State. (True on $this->login() function and False on $this->logout() function.)
- (bool) $this->error : If it found Error, it will become True. (This variable will update every execution/parse.)
- (string) $this->host : Current DirectAdmin URL.
- (string) $this->username : Current DirectAdmin Username.
- (string) $this->password : Current DirectAdmin Password.
- (bool/string) $this->login_as : Current DirectAdmin Login-As Username. (Should be False if it doesn't Login-As any user.)

---

### Note
- $this->query() function will automatically parse the response to Array if the command starts with "CMD_API_".
- $this->query() function will update $this->error to True if there is any problem while executing.
- It is better to check error by check $this->error variable. (Example: echo !$da->error ? "OK" : "Error"; )
- It will return plain response if there is any error or contains "\<html\>" tag.
- Connecting Timeout is 30 seconds and Timeout is 60 seconds by default. You can change it by set options to Curl Handle.
- If cacert.pem file exists, CURLOPT_CAINFO will set to cacert.pem's realpath by default for security reasons.
