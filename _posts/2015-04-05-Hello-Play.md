---
layout: post
title:  "Hello Play!"
date:   2015-04-05
tags: [Java, Play Framework]
---
"Play isn't really a Java web framework. `Java` is involved, but that isn't the whole story. Play provides a `fresh alternative` to excesive enterprise architectures. Play is not based on Java Enterprise Edition APIs (JEE) and made for Java developers; Play is for `web` developers."

"Play wasn't written for web developers, it was written by web developers, and they brought high productivity web development from modern frameworks like Ruby on Rails and Django to the JVM. Play is for productive web developers."

"Play 2 was written in Scala, but that doesn't mean you have to write your web application in Scala. This is because Play 2 comes with a complete Java API, giving you the option to pick the language that suits you best..."

"Play is fun. Play makes you more productive. Play is a web framework whose HTTP interface is `simple, convenient, flexible, and powerful`. Most importantly, Play `improves` on the most popular non-Java web development languages and frameworks --PHP and Ruby on Rails-- by introducing the advantages of the Java Virtual Machine (JVM)."

These parapraphs are extracts from [Play for Java][1] book. This book is an excelent introduction for Play.

Now we continue showing a small example creating a simple web application with Play. I recommend you define Firefox or Chrome like the principal browser.

Our first application
---------------------

* From <https://typesafe.com/get-started> you are going to download the Typesafe Activator (`typesafe-activator-1.3.2.zip` is the current version at the moment of this publication).
* Unzip the file over your favorite path (in our case I do it over /opt/apps. I am working in an Ubuntu machine).
* Add Activator into your PATH environment variable
    {% highlight sh %}
export PATH="$PATH:/opt/apps/activator-1.3.2"
    {% endhighlight %}
* From shell, execute the next command
   {% highlight bash %} albeip$ activator ui{% endhighlight %}
Next screen is showed: ![Activator](/images/2015-04-05/activator01.png) 

* Select a template (Computer Database Java)
In my case I chose the next path: /home/albeip/code/activator/activator-1.3.2/wsHelloWorld/computer-database-java. Push the red button named "Create app"

Next screen is showed: ![Activator](/images/2015-04-05/activator02.png) 

Next window is showed. The applications was created.

![Activator](/images/2015-04-05/activator03.png)

* Choose "Run and inspect the app" option, after push the "Run" button:

![Activator](/images/2015-04-05/activator04.png)

By default, the application is deployed to the next address: 

![Activator](/images/2015-04-05/activator05.png)

* Open the browser using the next URL: `http://localhost:9000`.

> Due to this is the first time we open this web application, a button named `"Apply this script now"` is showed. Push the button.
![Activator](/images/2015-04-05/activator06.png)

__The application is ready! You can see a list of computers created.__

![Activator](/images/2015-04-05/activator07.png)

Using an IDE
------------

Returning to the activator, it's possible view the code:
![Activator](/images/2015-04-05/activator08.png)

You can edit your code directly into the activator. Although I suggest to use an IDE such as `Eclipse`.

* Select "Code", push the `"..."` button located in front of Browse Code and choose `"Create Eclipse project"`

![Activator](/images/2015-04-05/activator09.png)

Be patient. You must wait the status changes:

![Activator](/images/2015-04-05/activator10.png)

![Activator](/images/2015-04-05/activator11.png)

* Open Eclipse using the respective workspace (`/home/albeip/code/activator/activator-1.3.2/wsHelloWorld`):

![Activator](/images/2015-04-05/activator12.png)

* Import the project:

![Activator](/images/2015-04-05/activator13.png)

![Activator](/images/2015-04-05/activator14.png)

Now you can see your project's code:

![Activator](/images/2015-04-05/activator15.png)

Until now you can make modification in your code and see the changes immediately.

We chose this template so that we can see how to implement the use of database. But if you are looking for a small web application, for example a web service, you could choose any other template such as `"Play Java Intro" template`.

Meanwhile we continue working with the current application to create a simple REST web service.

Creating a REST Web Service
---------------------------

* Create a new class named RestController. It will extend the `play.mvc.Controller`:

![Activator]({{site.baseurl}}//images/2015-04-05/activator16.png)

* Create the static method named `getData()`

{% highlight java %}
package controllers;

import play.mvc.Controller;
import play.mvc.Result;

public class RESTController extends Controller {
	
	public static Result getData() {
		return TODO;
	}
}
{% endhighlight %}

* Locate the `routes` file and open it

![Activator](/images/2015-04-05/activator17.png)

* At the end of the route  file we add the next line and save all:

{% highlight text %}
GET /myApplication/data  controllers.RESTController.getData()
{% endhighlight %}

* In the browser put the URL `http://localhost:9000/myApplication/data`

![Activator](/images/2015-04-05/activator18.png)

__Our web service is working!__

Now we are going to sofisticate it returning data.

* Create the next class:

{% highlight java %}
package models;

public class Dummy {
	private String message;
	private int value;
	
	public String getMessage() {
		return message;
	}

	public int getValue() {
		return value;
	}

	public Dummy() {
		message = "Hello Everybody!";
		value = 10;
	}
}

{% endhighlight %}


* Modify the controller with the next code:
{% highlight java %}
package controllers;

import models.Dummy;
import play.libs.Json;
import play.mvc.Controller;
import play.mvc.Result;

public class RESTController extends Controller {
	
	public static Result getData() {
		Dummy dummy = new Dummy(); 
		return ok(Json.toJson(dummy));
	}
}
{% endhighlight %}

* Finally, refresh `http://localhost:9000/myApplication/data`

![Activator](/images/2015-04-05/activator19.png)


__Ready!__


We reach the en of our introduction to Play.

The next steps now is start playing with Play and you are going to find a lot of interesting things with it.

__At the end, you will find that Play is very funny!__

##Interesting Links

[Introducing Play 2.0][3]

[Node.js vs Play Framework][4]

[Play at Linked in][5]


[1]: http://www.amazon.com/gp/product/1617290904/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1617290904&linkCode=as2&tag=wwwalbeviacom-20&linkId=OVKL7ZGQCY5VPVCO
[2]: https://typesafe.com/get-started
[3]: https://www.playframework.com/documentation/2.0/Philosophy
[4]: http://es.slideshare.net/brikis98/nodejs-vs-play-framework
[5]: http://es.slideshare.net/brikis98/the-play-framework-at-linkedin?related=1
[jekyll]:          http://jekyllrb.com
[jekyll-gh]:       https://github.com/jekyll/jekyll
[jekyll-help]:     https://github.com/jekyll/jekyll-help

