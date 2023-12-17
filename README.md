<div align="center">

[![kittyauth Logo](https://kotelek.dev/kittyauth/assets/logo.png)](https://kotelek.dev/kittyauth/)

[![CI Status][build-badge]][build-link] [![Website][website-badge]][website-link] [![Docs][docs-badge]][docs-link]


[build-badge]: https://img.shields.io/github/actions/workflow/status/xKotelek/kittyauth/blank.yml?branch=main&logo=Github&logoColor=611aa8&labelColor=0c0d10&color=1d0732&style=for-the-badge
[build-link]: https://github.com/xKotelek/kittyauth/actions/workflows/blank.yml

[website-badge]: https://img.shields.io/badge/website-green?labelColor=0c0d10&color=1d0732&style=for-the-badge&logo=firefoxbrowser&logoColor=611aa8
[website-link]: https://kotelek.dev/kittyauth/

[docs-badge]: https://img.shields.io/badge/docs-green?labelColor=0c0d10&color=1d0732&style=for-the-badge&logo=readthedocs&logoColor=611aa8
[docs-link]: https://docs.betterdiscord.app


Kittyauth is a easy-to-use & secure authorization system. Prove of security (hashes usage) below.

[![Hashes Usage Prove](https://kotelek.dev/kittyauth/assets/hashes-usage.png)](https://kotelek.dev/kittyauth/)

</div>

# Usage

If you want detailed guide go to our [wiki](https://github.com/xKotelek/kittyauth/wiki). Simple guide available for PHP is below:

## First steps

First initiate your PHP file and start a session <br>
```php
<?php

session_start();

if(!isset($_SESSION['logged'])) { $_SESSION['logged'] = false; }

?>
```
Next step is to make an example login button that redirect us to kittyauth. This php file will also check if you are logged to display logout button.
```php
<?php

session_start();

if(!isset($_SESSION['logged'])) {
    $_SESSION['logged'] = false;
}

?>

<!DOCTYPE html>
<head>
  <title>kittyauth example</title>
</head>
<body>
  <?php if($_SESSION['logged'] = false) { ?>
    <button><a href="https://kotelek.dev/kittyauth/?login&next=https://your-site.com/"></a>Login</button><br>
    <button><a href="https://kotelek.dev/kittyauth/?register&next=https://your-site.com/"></a>Register</button><br>
  <?php } else { ?>
    Hi, <?php echo $_SESSION['username']; ?>
    Your email is: <?php echo $_SESSION['usermail']; ?>
    <button><a href="https://kotelek.dev/kittyauth/?logmeout&next=https://your-site.com/?logged_out">Logout</a></button>
  <?php } ?>
</body>
</html>
```
So we have login, register and if logged logout button, but how to get user information after logging?<br>
We are gonna add listening to GET parameter named "access_token" that's token that we can use to get all of user data.<br>
We also add listening to GET parameter named "logged_out" to reset SESSION logged state to false!
```php
<?php

session_start();

if(!isset($_SESSION['logged'])) {
    $_SESSION['logged'] = false;
}

if(isset($_GET['logged_out'])) { $_SESSION['logged'] = false; header('Location: ./ '); };

if(isset($_GET['access_token'])) {
    $url = 'https://kotelek.dev/kittyauth/api/?get=access_token&toget=userdata&access_token=' . $_GET['access_token'];

    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($ch);

    if ($response === false) {
        header('Location: ./');
        exit();
    } else {
        curl_close($ch);

        $data = json_decode($response, true);

        if (json_last_error() !== JSON_ERROR_NONE) {
            // Handle JSON decoding error
            header('Location: ./');
            exit();
        } else {
            if (isset($data['user_username']) && isset($data['user_email'])) {
                $_SESSION['username'] = $data['user_username'];
                $_SESSION['usermail'] = $data['user_email'];
                $_SESSION['logged'] = true;
            } else {
                $_SESSION['logged'] = false;
                header('Location: ./');
                exit();
            }
        }
    }
}

?>

<!DOCTYPE html>
<head>
  <title>kittyauth example</title>
</head>
<body>
  <?php if($_SESSION['logged'] == false) { ?>
    <button><a href="https://kotelek.dev/kittyauth/?login&next=https://your-site.com/"></a>Login</button><br>
    <button><a href="https://kotelek.dev/kittyauth/?register&next=https://your-site.com/"></a>Register</button><br>
  <?php } else { ?>
    Hi, <?php echo $_SESSION['username']; ?>
    Your email is: <?php echo $_SESSION['usermail']; ?>
    <button><a href="https://kotelek.dev/kittyauth/?logmeout&next=https://your-site.com/?logged_out">Logout</a></button>
  <?php } ?>
</body>
</html>
```
So that's it, we made it! Result below.<br>
[![Example Result](https://kotelek.dev/kittyauth/assets/success.png)](https://kotelek.dev/kittyauth/)<br>
What this code basically does is it checks the access token with api and if the token is correct api returns user data that website prints. If not we get login and register buttons back. More information [here](https://github.com/xKotelek/kittyauth/wiki)
