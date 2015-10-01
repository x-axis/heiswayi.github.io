---
layout: post
title: PHP - Encryption, Decryption And Password Hashing
description: Code snippets, examples of usage and the implementations.
keywords: php encryption, php decryption, password hashing using php
---

These days, we're browsing the Web a lot. Some of us may be doing their business at risk when they're using a web application which is weakly built in protecting its user's data or confidential information. It doesn't matter the application is simple or complicated, small or big, when it involves keeping our user's data, we need to take care them well and ensure their data is secure with our application.

I used to develop a lot of PHP-based applications and use MySQL as its database to save the data. Things that I'm going to share here are the PHP code snippets that I used for encrypting and decrypting the data strings. Let's have a look!

### Encryption

This is a PHP function used to encrypt the data string.

{% highlight php startinline %}
function Encrypt($password, $data)
{

    $salt = substr(md5(mt_rand(), true), 8);

    $key = md5($password . $salt, true);
    $iv  = md5($key . $password . $salt, true);

    $ct = mcrypt_encrypt(MCRYPT_RIJNDAEL_128, $key, $data, MCRYPT_MODE_CBC, $iv);

    return base64_encode('Salted__' . $salt . $ct);
}
{% endhighlight %}

#### Example of Usage

{% highlight php startinline %}
echo Encrypt('myPass123', 'Welcome to Flippancy 25');
// Output: U2FsdGVkX19LYv5Y5EDmFbjH8bGMDFwlid30h2x1ybibT1Dwp0vekJ0OT4tb7/j6
{% endhighlight %}

The special feature here is, every time I execute this function `Encrypt()`, by using the **same** password and **same** data, the cnrypted string is always changed.

Here is the example output of encrypted strings after I executed it for three times:-

{% highlight php startinline %}
// Output 1: U2FsdGVkX19LYv5Y5EDmFbjH8bGMDFwlid30h2x1ybibT1Dwp0vekJ0OT4tb7/j6
// Output 2: U2FsdGVkX1/3zxJCcE8p89t67nJNp8blNkezNxTVn4IDFQLM755K2+OSfFHewDLI
// Output 3: U2FsdGVkX18OQ8puUN8BBi+d6vAjEzDTZqM2WaKQD1atOykkYl9MY7NQM1DqI4Kw
{% endhighlight %}

However, even the encrypted strings are changed each time we executed the function for the same input data, but these three encrypted strings are still can be decrypted and output the **same** results. This is the uniqueness of this encryption function.

### Decryption

To get back the original data, we need the decryption function.

{% highlight php startinline %}
function Decrypt($password, $data)
{

    $data = base64_decode($data);
    $salt = substr($data, 8, 8);
    $ct   = substr($data, 16);

    $key = md5($password . $salt, true);
    $iv  = md5($key . $password . $salt, true);

    $pt = mcrypt_decrypt(MCRYPT_RIJNDAEL_128, $key, $ct, MCRYPT_MODE_CBC, $iv);

    return $pt;
}
{% endhighlight %}

#### Example of Usage

{% highlight php startinline %}
echo Decrypt('myPass123', 'U2FsdGVkX19LYv5Y5EDmFbjH8bGMDFwlid30h2x1ybibT1Dwp0vekJ0OT4tb7/j6');
echo Decrypt('myPass123', 'U2FsdGVkX1/3zxJCcE8p89t67nJNp8blNkezNxTVn4IDFQLM755K2+OSfFHewDLI');
echo Decrypt('myPass123', 'U2FsdGVkX18OQ8puUN8BBi+d6vAjEzDTZqM2WaKQD1atOykkYl9MY7NQM1DqI4Kw');
// All of three above will output the same decrypted data: Welcome to Flippancy 25
{% endhighlight %}

For each encryption and decryption, the correct password/passcode is required in order to get back the original data strings.

### Application of Data Encryption and Decryption

These both PHP encryption and decryption functions are useful when we want to securely encrypt user's confidential information such as Social Security number, Phone number, Bank Account number, Credit Card confidentials, or whatever information we think we need to encrypt it, and at some points of time we need it back in plain text or the original version of the data.

> Using these functions for saving passwords are NOT RECOMMENDED as it is reversible (encrypt-decrypt).

### Password Hashing

This is the interesting part and the most important in web security. The best implementation in password hashing is to use **_one-way_** hashing technique. Meaning that it's irreversible. The password is encrypted and hashed for one-time only and it's no longer can be decrypted to a plain text anymore. You may want to check [password_hash](http://php.net/manual/en/function.password-hash.php) or [crypt](http://php.net/manual/en/function.crypt.php) for PHP one-way hashing algorithms.

In my projects, I usually use this hashing function to hash user's password:

{% highlight php startinline %}
function hashPassword($password, $salt) {
    $hash_password = hash("SHA512", base64_encode(str_rot13(hash("SHA512", str_rot13($salt . $password)))));
    return $hash_password;
}
{% endhighlight %}

#### Example of Usage

{% highlight php startinline %}
echo hashPassword('myPa55w0rd', 'Flipp@ncy25');
// Output: 815890bb72e10a75a52087513a931afb6641a5d8d105365fa6f389f038dd81b45290a44cf94bb61e7741e073c6f4d59a16e9896bd197cc320f84f3a4d27cfb50
{% endhighlight %}

-----

For more details about password hashing, I **recommended** you to read [this article](https://crackstation.net/hashing-security.htm). It's a really good article about salted password hashing.
