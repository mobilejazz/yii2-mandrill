Mandrill API Extension
======================
Mandrill Api Integration for Yii2  
Version 1.3.0 [![Build Status](https://travis-ci.org/nickcv-ln/yii2-mandrill.svg)](https://travis-ci.org/nickcv-ln/yii2-mandrill)

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist nickcv/yii2-mandrill "*"
```

or add

```
"nickcv/yii2-mandrill": "*"
```

to the require section of your `composer.json` file.


Set Up
------

To use Mandrill you will need to have a [Mandrill Account](https://mandrill.com/signup/). 

Every single account can send up to **12k emails per month for free**.

The [cost for sending emails over that threshold is really low](https://mandrill.com/pricing/).

Once you have an account you will need to create an **API Key**.  
You can create as many API keys as you want, and it's best practice to create one for each website.  
You can also create **test API keys**. Every email submitted using a test API key will not actually be submitted, but you'll be able to check inside the **test dashboard** if the test went thorugh successfully.

Mandrill will keep track of every single email you submit. You can filter the data using tags and you'll also be able to check how many times each email was opened and if the links within it have been clicked.

Usage
-----

Once the extension is installed, change your application config file ```web.php```:

First of all you will need to add an ```application name```.
By default this extension will send every single email using the application name as the sender name and the ```adminEmail``` parameter inside ```params.php``` as the sender email.


```
'id' => 'basic',
'name' => 'Application Name',
```

```
return [
    'adminEmail' => 'admin@example.com',
];
```

You will then need to add the component

```
    'mailer' => [
        'class' => 'nickcv\mandrill\Mailer',
        'apikey' => 'YourApiKey',
    ],
```

From now on you can just use the mandrill mailer just as you used to use the default one.

```
\Yii::$app->mailer
    ->compose('mailViewName', ['model' => $model])
    ->setTo('email@email.com')
    ->send();
```

Mandrill Templates
------------------
You can use Mandrill's own template system if you want to, just set up as true the ```useMandrillTemplates``` attribute in the component configuration

```
    'mailer' => [
        'class' => 'nickcv\mandrill\Mailer',
        'apikey' => 'YourApiKey',
        'useMandrillTemplates' => true,
    ],
```

If you do turn this feature on the component will look for a template within mandrill named after the view argument of the compose method.

Since **version 1.3.0** the component will stop falling back to rendering the internal views.
This change has been made because now the mandrill send-template method will be used, avoiding to make two API calls when templates are enabled.


Additional Methods
------------------

Mandrill lets you set up tags. The method ```\nickcv\mandrill\Message::setTags($tags)``` accept as an argument both a string or an array of strings:

```
\Yii::$app->mailer
    ->compose('mailViewName', ['model' => $model])
    ->setTags(['registration']);
```

Since **version 1.3.0** it's also possible to enable the async mode.
When using async mode mandrill will queue the messages and send em in batches.
If you send a message to more than 10 email addresses async mode will be used
automatically.

```
\Yii::$app->mailer
    ->compose('mailViewName', ['model' => $model])
    ->enableAsync();
```

For more informations check the component documentation.

Unit Testing
------------

All the Classes within the package have been unit tested.  
The tests are included within the package.  

If you wish to run the tests install codeception following the Yii2 documentation.  

The tests use the developer Mandrill Test API key which is only whitelisted for the developer IP.

Logs
----

The component automatically logs every single message sent through mandrill, inside the "mandrill" category.

Messages sent successfully are logged using ```\Yii::info()```, messages rejected or invalid are logged using ```\Yii::warning()```, and all the exceptions thrown by the Mandrill Class are logged using ```\Yii::error()```.

If you are using mandrill templates and the template is not found the error will be logged using ```Yii::info()```.