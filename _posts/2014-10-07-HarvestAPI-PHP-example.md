---
layout: post
title: PHP snippet example call to HarvestAPI 
---

![_config.yml]({{ site.baseurl }}/images/harvest_logo.png)

I modified some PHP code I found on [StackOverflow](https://stackoverflow.com/questions/9802788/call-a-rest-api-in-php) to make a call to [HarvestAPI](http://harvestapi.io). I did this in hopes that it will make things easier for developers to see how to make calls to this rest API that provides important Jamaican agriculture information to a variety of important stakeholders along the agriculture value chain. 

Using HarvestAPI one could for instance find the latest prices for a particular crop such as Okra.

I created this [gist on github](https://gist.github.com/varunity/bc041214007fe825c467) so hopefully others can learn from it. Please see further notes below.

{% gist bc041214007fe825c467 %}

OK, so the CallAPI function can be included in your own code. Persons will need to register for a HarvestAPI account otherwise an authentication error will be returned:
{% highlight ruby %}
{"detail": "Invalid username/password"}
{% endhighlight %}

To register for an API key, please [signup](http://harvestdata.herokuapp.com/user/register) and make a note of your username and password. You will have to modify the code with this information.

{% highlight ruby %}
//search for farmers named 'johnson' and order by first name
$farmers = CallAPI('GET', 'harvestdata.herokuapp.com/farmers/', array('ordering'=>'first_name', 'search'=>'johnson'));  
{% endhighlight %}

Another note is I had to install curl before the code would run properly:

{% highlight ruby %}
//had to install php curl
sudo apt-get install php5-curl
sudo service apache2 restart
//It's possible you'll need to install more:
sudo apt-get install curl libcurl3 libcurl3-dev;
{% endhighlight %}


