---
layout: post
title:  "Web Application With Go"
date:   2015-07-01
tags: [Go, Sublime Text, Web Application]
---

Web applications are present everywhere. Take any application you use on a daily basis, and likely it is a web application or has a web application variant (this includes mobile apps). Any language that support software development that interfaces with human beings will inevitably support web application development. 

Go has gained tremendous popularity as a programming language for writing web applications and -as-a-Service systems.

Go is a relatively new programming language, with a thriving and growing community. If you know anything at all about Go, you will know that is very well suited for writing server-side programs that are fast.

It is simple and familiar to most programmers who are used to procedural programming, but also provides features of `functional programming`. It support `concurrency by default`, has a `modern packaging system`, does `garbage collection`, and has an `extensive and powerful set of standard libraries`.

Many companies have already started using Go. As of writing this includes infrastructure companies like Dropbox and Sendgrid, technology-oriented companies such as Square and Hailo as well as more traditional companies such as BBC and New York Times.

Go privides a viable alternative to existing languages and platforms for developing large-scale web applications. Large-scale web applications typically needs to be:

* Scalable
* Modular
* Maintainable

It's the moment to show a simple web application based on Go and know how simple is create it.

We are going to use a computer with Windows 7 64 bits.

### Installing the Go environment

Before to write our first line of Go code, we'll need to set up the environment. Let's start off with installing Go.

Download the [latest version of Go][1]. As of writing is Go 1.4.2, we choose go1.4.2.windows-amd64.msi file. Run it, and after the installation finish Go is installed into "C:\Go".

Verify the GOROOT environment variable is defined: 

![Web Application With Go]({{site.url}}/assets/2015-07-01/01 WebApplicationWithGo.png)

Verify the bin is into the path environment variable:

%GOROOT%/bin

and verify your installation: 

![Web Application With Go]({{site.url}}/assets/2015-07-01/02 WebApplicationWithGo.png)

Now it's time to choose any text editor to write Go coding. I choose [Sublime Text 3][2].

Define the `workspace` creating the directory "D:\code\go". Into this workspace create the following structure:

![Web Application With Go]({{site.url}}/assets/2015-07-01/03 WebApplicationWithGo.png)

Create the file named setVariables.bat with the next content:

{% highlight text%}
set GOPATH=D:\code\go
echo %GOPATH%
set PATH=%PATH%;%GOPATH%\bin
{% endhighlight %}

`It’s very important use the name GOPATH to define our workspace because it is used by Go.`

Into the workspace the bin, pkg and src directories were created. For our first project we create the directory “src/first_webapp” where we are going to create the file server.go with the next code:

{% highlight go%}
package main
import (
    "fmt"
    "net/http"
)

func handler(writer http.ResponseWriter, request *http.Request) {
    fmt.Fprintf(writer, "Hello World, %s!", request.URL.Path[1:])
}

func main() {
    http.HandleFunc("/", handler)
    http.ListenAndServe(":8080", nil)
}
{% endhighlight %}

Open a command window over the workspace and execute the setVariables.bat

![Web Application With Go]({{site.url}}/assets/2015-07-01/04 WebApplicationWithGo.png)

Run the command `go install first_webapp`

![Web Application With Go]({{site.url}}/assets/2015-07-01/05 WebApplicationWithGo.png)

It generates the first_webapp.exe file into the bin directory of our workspace

![Web Application With Go]({{site.url}}/assets/2015-07-01/06 WebApplicationWithGo.png)

Now you can execute your first web application. Execute the first_webapp.exe

![Web Application With Go]({{site.url}}/assets/2015-07-01/07 WebApplicationWithGo.png)

In any browser run http://localhost:8080

![Web Application With Go]({{site.url}}/assets/2015-07-01/07 WebApplicationWithGo.png)

Ready! You has created your first web application with Go!

![Web Application With Go]({{site.url}}/assets/2015-07-01/08 WebApplicationWithGo.png)


Don't worry if you don't understand some of the Go syntax. You can find more details into the book [Go Web Programming][3] or into the [Goland site based on web applications][4].

This simple web application is pretty useless and is nothing more than the equivalent of a Hello World application. In future posts we will build other most sophisticated examples for creating web applications and web services with Go.

[1]: https://golang.org/dl/
[2]: http://www.sublimetext.com/3
[3]: http://www.manning.com/chang/
[4]: https://golang.org/doc/articles/wiki/