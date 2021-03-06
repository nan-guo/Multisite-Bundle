Symfony Multisite Bundle
======================

The repository contains the Symfony multisite bundle for Symfony 3. 
This bundle allows you to manage multisite in symfony application.


- Each site can have its own database connection or use the same database.
- Each site can have diffirent locales
- Each site can have its own app folder and public folder
- Each site can have its own robots.txt

## Structure public folder

Each sites first looks for assets in its own folder

For example (demo_2): 

The site demo_2 will first look for the image chat.png in

/web/public/demo_2/imgs/chat.png

if this image does not exist, the file /web/imgs/chat.png will be used

## Structure robots.txt

Each site can have its own robots.txt in the folder :

/robots/hostname.txt

if hostname.txt does not exist, the file /web/robots.txt will be used.

## Installation

```

composer require prodigious/symfony-multisite-bundle

```

## Setup

```php
    $bundles = array(
        ...
        new Prodigious\MultisiteBundle\MultisiteBundle(),
        ...
    );
```

Then add these lines to your composer.json of your Symfony project:

```
    "scripts": {
        "post-install-cmd": [
            ...
            "Prodigious\\MultisiteBundle\\Composer\\ScriptHandler::installBundle"
        ],
        "post-update-cmd": [
            ...
            "Prodigious\\MultisiteBundle\\Composer\\ScriptHandler::installBundle"
        ]
    }
```

Afterwards, initialize the bundle using

```

composer install

```

After the installation, the bundle will create some files in your project :

- sites/
- app/MultisiteKernel.php

And you need to modify some files :

##### Add these lines to .htaccess in the folder web:

```
<IfModule mod_rewrite.c>
    RewriteEngine On
    
    # Multisite conditions
    RewriteCond %{DOCUMENT_ROOT}/robots/%{HTTP_HOST}.txt -f
    RewriteRule ^robots\.txt$ robots/%{HTTP_HOST}.txt [L]

    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^public/([^/]+)(?:/(.*)|$) /$2 [QSA,R,L]
    # End
</IfModule>
```

##### Modifiy your app.php and app_dev.php in the folder web:

Replace
```
require __DIR__.'/../vendor/autoload.php
```
To
```
require __DIR__.'/../sites/autoload/sites.php';
```

And Relace

```
    // app.php
    $kernel = new AppKernel('prod', false);
    // app_dev.php
    $kernel = new AppKernel('dev', true);
```

To

```
    // app.php
    $kernel = new MultisiteKernel('prod', false);
    $kernel->setSite($site);
    // app_dev.php
    $kernel = new MultisiteKernel('dev', true);
    $kernel->setSite($site);
```

Like this :

```php
// require __DIR__.'/../vendor/autoload.php';
require __DIR__.'/../sites/autoload/sites.php';

if (PHP_VERSION_ID < 70000) {
    include_once __DIR__.'/../var/bootstrap.php.cache';
}

$kernel = new MultisiteKernel('prod', false);
$kernel->setSite($site);
if (PHP_VERSION_ID < 70000) {
    $kernel->loadClassCache();
}
```

## Instructions

There is a list of commands to manage your sites.


#### Create a new site

```

php bin/console site:create

```
![screenshot](https://github.com/nan-guo/Symfony-Multisite-Bundle/blob/master/Resources/public/imgs/screenshot-1.png)

#### Get the list of sites

```

php bin/console site:list

```
![screenshot](https://github.com/nan-guo/Symfony-Multisite-Bundle/blob/master/Resources/public/imgs/screenshot-2.png)

#### Disable a site

```

php bin/console site:disable --name=demo_1

```
![screenshot](https://github.com/nan-guo/Symfony-Multisite-Bundle/blob/master/Resources/public/imgs/screenshot-3.png)

#### Enable a site

```

php bin/console site:enable --name=demo_1

```
![screenshot](https://github.com/nan-guo/Symfony-Multisite-Bundle/blob/master/Resources/public/imgs/screenshot-4.png)

#### Delete a site

```

php bin/console site:delete --name=demo_1

```
![screenshot](https://github.com/nan-guo/Symfony-Multisite-Bundle/blob/master/Resources/public/imgs/screenshot-5.png)

#### Synchronizer configuration

```

php bin/console site:config:sync

```

## Attributes

```

$currentSite = $request->attributes->get('site'); // 'app' by default

```

```

$currentInstance = $request->attributes->get('instance');

```

```

$currentLocal = $request->getLocale();

```



## Additional info
Author: [Nan GUO](https://github.com/nan-guo/)
 
Company : [Prodigious](http://www.prodigious.com/)
