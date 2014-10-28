---
layout: post
title: Make Your Drupal Website Communicate with a Native Andriod App
---

![_config.yml]({{ site.baseurl }}/images/why_need_services.png)

I will demonstrate how to set up the [Drupal Services](https://www.drupal.org/project/services) module to quickly turn your Drupal 7 website into a [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) server. I also include a sample native Android app to demonstrate how to login to Drupal via an HTTP request to the server and post new content by using this web service to interact with the Drupal site from a completely seperate application.

##Why?

###What are web services good for anyway? 

A [Web Service](https://en.wikipedia.org/wiki/Web_service) is a method of communication between two electronic devices over a network. In my case, I have a website called patwa.org which I am in the process of updating from Drupal 6 to Drupal 7. [Patwa.org](http://patwa.org) is an audio dictionary for the Jamaican patois language. By exposing my patwa content on the internet as a web service, I can build a mobile app that allows users to easily add new patwa words to the dictionary. The advantage of having this as a native app instead of say just a mobile friendly website is that going native allows me to access the hardware of the cell phone to allow users to record their own use of patwa as audio files. Although free and open source frameworks such as [PhoneGap](http://docs.phonegap.com/en/1.9.0/cordova_media_capture_capture.md.html) allow developers to gain access to the audio, image, and video capture capabilities of the device, I have chosen to just [fork](https://github.com/varunity/DrupalAndroidApp) an existing Android application written in java.

##How?
To demonstrate how to set up and interact with the services module, I am sharing all the steps I took to setup drupal on my (local)development environment and install, enable, and configure the relevant modules. I am a big fan of using the command line so I strongly reccomend this tool called [drush](https://github.com/drush-ops/drush) which saves me mucho time.

> Drush is a command-line shell and scripting interface for Drupal, a veritable Swiss Army knife designed to make life easier for those who spend their working hours hacking away at the command prompt.

###Install drupal with drush
N.B. you can also [download drupal 7](https://www.drupal.org/download) the old fashioned way.
{% highlight bash %}
~/work/jadrug$ drush dl drupal --drupal-project-rename=drupalservices
{% endhighlight %}

If using an existing Drupal 7 site, be sure to backup and upgrade to Drupal 7.32 or to whatever the latest security release is at the time when you are reading this.

###Create a symbolic link to the drupal installation on your web root (/var/www)
{% highlight bash %}
/var/www$ sudo ln -s ~/work/jadrug/drupalservices
{% endhighlight %}

###Create mysql database and user

For simplicity, I just use a bash script below called 'createdb.sh', but you can follow the steps [outlined here](https://www.drupal.org/documentation/install/create-database) for creating a mysql database and user.
{% highlight bash %}
#script createdb.sh creates mysql database and user
~/scripts$ . createdb.sh services services_user L0M88OE1k
{% endhighlight %}

###Copy default.settings.php file and edit settings.php
{% highlight bash %}
cp sites/default/default.settings.php  sites/default/settings.php 
vim sites/default/settings.php 
{% endhighlight %}

Set database details in settings.php
{% highlight ruby %}
   $databases['default']['default'] = array(
     'driver' => 'mysql',
     'database' => 'services',
     'username' => 'services_user',
     'password' => 'L0M88OE1k',
     'host' => 'localhost',
     'prefix' => '',
   );
{% endhighlight %}

###Create files folder
{% highlight bash %}
$ cd sites/default/
$ mkdir files
$ sudo chmod 775 -R files/
$ sudo chgrp www-data -R files/
{% endhighlight %}

Ensure your webserver has write access to the files folder and all subdirectories

###Visit website in browser and do standard drupal install

```
eg. go to http://localhost/drupalservices/install.php
```

![_config.yml]({{ site.baseurl }}/images/DrupalInstalling.png)

###Drupal site should be installed now

![_config.yml]({{ site.baseurl }}/images/DrupalHome.png)

###Install modules

You can also download them from their respective project pages if you are not using drush.

{% highlight bash %}
#views and services_views are optional
$ drush dl views, services, services_views, ctools, libraries
$ drush en views, veiws_ui, services, services_views, libraries, rest_server
{% endhighlight %}

###Enable my favourite module "administration menu" and disable the default drupal toolbar

{% highlight bash %}
$ drush dl admin_menu
$ drush dis toolbar
$ drush en admin_menu
{% endhighlight %}

###Go to admin/structure/services

You will be presented with an empty list because no endpoint/resources have been defined yet

###Add an an endpoint

Make sure that the rest_server module is installed. Lets be boring and call our endpoint "api".

![_config.yml]({{ site.baseurl }}/images/AddAnEndpoint.png)

###View list of services again (Go to admin/structure/services)
![_config.yml]({{ site.baseurl }}/images/ServicesList.png)

###Click on "Edit Resources"

This is to define the software function provided by this endpoint eg. once the user methods are enabled, sending HTTP requests to http://example.com/my_endpoint/user/login allows a user to login via the web service and http://example.com/my_endpoint/user/register would allow a new user to register.

###Enable node and user resources on your endpoint

![_config.yml]({{ site.baseurl }}/images/EditResource.png)

```
Now you can go to http://localhost/drupalservices/api/node to see a list of nodes in xml
```

###Test your setup
Download [Poster](https://addons.mozilla.org/en-us/firefox/addon/poster/) or some other "REST Client" type of tool(eg. "Postman" for google chrome). Poster is a Firefox plugin that is decribed as

> a developer tool for interacting with web services and other web resources that lets you make HTTP requests, set the entity body, and content type. This allows you to interact with web services and inspect the results...

Use this tool to efficiently test the api/services you set up above by making specific HTTP requests (GET, POST, etc.) to the resources/endpoints you defined earlier.

After we test retrieving data (GET), adding data(POST), and updating data(PUT) over HTTP, we can create a simple Android app that will use the web service we created earlier to interact with and post data to our drupal site. 

N.B. user permissions in Drupal work with services so be sure to test your URLs and users. Ensure that everything is configured properly at
```
admin/people/permissions
```
I usually start testing by allowing access to anonymous users before turning on session authentication.

![_config.yml]({{ site.baseurl }}/images/RestAuthentication.png)

Here is [more information](https://www.drupal.org/node/1699354) on how to test services.

Once you are happy with the web services you have set up locally, you can push your code to a live server on the internet. This way your web service can be used and accessed from anywhere on the internet.

##The Android Application

![_config.yml]({{ site.baseurl }}/images/MobileApp.png)

I created an android application which has been configured to login and add new patwa words to patwa.org from the application. The demo code which opens in [Android Studio](https://developer.android.com/sdk/installing/studio.html) is [here](https://github.com/varunity/DrupalAndroidApp).

The important folder to look at in this code is: 

```
DrupalAndroidApp/app/src/main/java/com/example/DrupalAppDemo/ 
```

The relevant files to look at and modify are [LoginActivity.java](https://github.com/varunity/DrupalAndroidApp/blob/master/app/src/main/java/com/example/DrupalAppDemo/LoginActivity.java), [ListActivity.java](https://github.com/varunity/DrupalAndroidApp/blob/master/app/src/main/java/com/example/DrupalAppDemo/ListActivity.java), and [AddArticle.java](https://github.com/varunity/DrupalAndroidApp/blob/master/app/src/main/java/com/example/DrupalAppDemo/AddArticle.java).

##FIN
I hope this code and demonstration helps persons build better mobile apps and web services. Please be sure to share any feedback or comments.
