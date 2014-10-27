---
layout: post
title: Make Your Drupal Website Communicate with a Native Andriod App
---
I will demonstrate how to set up the Drupal Services module to quickly turn your Drupal 7 website into a [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) server. I also include a sample native Android app to demonstrate how to login to Drupal using Services module and post new content using REST services to interact with the Drupal site..

##Why?

###What are web services good for anyway? 

A [Web Service](https://en.wikipedia.org/wiki/Web_service) is a method of communication between two electronic devices over a network. In my case, I have a website called patwa.org which I am in the process of updating from Drupal 6 to Drupal 7. [Patwa.org](http://patwa.org) is an audio dictionary for the Jamaican patois language. By exposing my patwa content on the internet as a web service, I can build a mobile app that allows users to easily add new patwa words to the dictionary. The advantage of having this as a native app instead of say just a mobile friendly website is that going native allows me to access the hardware of the cell phone to allow users to record their own use of patwa as audio files. Although free and open source frameworks such as [PhoneGap](http://docs.phonegap.com/en/1.9.0/cordova_media_capture_capture.md.html) allow developers to gain access to the audio, image, and video capture capabilities of the device, I have chosen to just fork an existing Android application written in java.

##How?
To demonstrate how to set up and interact with the services module, I am sharing all the steps I took to setup drupal on my development environment local machine and install and enable the relevant modules. I am a big fan of the command line so I strongly reccomend this tool called drush which saves me mucho time.

###Install drupal with drush
{% highlight bash %}
~/work/jadrug$ drush dl drupal --drupal-project-rename=drupalservices
{% endhighlight %}

If using an existing Drupal 7 site, be sure to upgrade to Drupal 7.32 or to whatever the latest security release is at the time when you are reading this.

###Create a symbolic link to the drupal installation on your web root (/var/www)
{% highlight bash %}
/var/www$ sudo ln -s ~/work/jadrug/drupalservices
{% endhighlight %}

###Create mysql database and user

Here I just use a bash script, but you can follow the steps [outlined here](https://www.drupal.org/documentation/install/create-database).
{% highlight bash %}
#script createdb.sh creates mysql database and user
~/scripts$ . createdb.sh services services_user L0M88OE1k
{% endhighlight %}

###Copy default.settings.php file and edit settings.php with DB credentials.
{% highlight bash %}
cp sites/default/default.settings.php  sites/default/settings.php 
vim sites/default/settings.php 
{% endhighlight %}

Set database details in settings.php
![_config.yml]({{ site.baseurl }}/images/DB_settings.png)

###Visit website in browser and do standard drupal install

```
eg. go to http://localhost/drupalservices/install.php
```

###Create files folder
{% highlight bash %}
$ cd sites/default/
$ mkdir files
$ sudo chmod 775 -R files/
$ sudo chgrp www-data -R files/
{% endhighlight %}

###Drupal site should be installed now(standard)


###Install modules

{% highlight bash %}
$ drush dl views, services, services_views, ctoolsi, libraries
$ drush en views, veiws_ui, services, services_views, libraries, rest_server
{% endhighlight %}

###Enable my favourite module "administration menu" and disable the default drupal toolbar

{% highlight bash %}
$ drush dl admin_menu
$ drush dis toolbar
$ drush en admin_menu
{% endhighlight %}

###Go to admin/structure/services

You will be presented with an empty list

###Add an an endpoint

Make sure that the rest_server module is installed. Lets be boring and call our endpoint "api"

![_config.yml]({{ site.baseurl }}/images/AddAnEndpoint.png)

###View list of services
![_config.yml]({{ site.baseurl }}/images/ServicesList.png)

###Click on "edit resource"

###Enable node and user resources on your endpoint

![_config.yml]({{ site.baseurl }}/images/EditResource.png)

```
Go to http://localhost/drupalservices/api/node
```

Download Poster or some other tool(eg. "Postman" for google chrome). [Poster](https://addons.mozilla.org/en-us/firefox/addon/poster/) is a firefox plugin which is

> a developer tool for interacting with web services and other web resources that lets you make HTTP requests, set the entity body, and content type. This allows you to interact with web services and inspect the results...

Use this tool to efficiently test the api/services you set up above by making specific HTTP requests (GET, POST, etc.).

After we test retrieving data (GET), adding data(POST), and updating data(PUT) over HTTP, we can create a simple 

N.B. user permissions in Drupal are respected with services so be sure to test your URLs and users and that everything is configured properly at
```
admin/people/permissions
```

##The Android Application

![_config.yml]({{ site.baseurl }}/images/MobileApp.png)

I created an android application which has been configured to login and add new patwa words to patwa.org from the application. The demo code which opens in [Android Studio](https://developer.android.com/sdk/installing/studio.html) is [here](https://github.com/varunity/DrupalAndroidApp).

The important folder to look in is: DrupalAndroidApp/app/src/main/java/com/example/DrupalAppDemo/ and the important code/files to look at and modify are in LoginActivity.java, ListActivity.java, and AddArticle.java.

##FIN
I hope this code and demonstration helps persons build better mobile apps and web services. Please share any feedback or comments.
