# Introduction to Web Programming

A web is an **information system enabling documents and other web resources to be accessed over an internet**.
Especially, World Wide Web(WWW) or Web, is a web over the Internet.

## Internet/Web vs. internet/web

* internet = inter network: 네트워크들의 네트워크 (WAN처럼)
* web = info service over internet: internet을 통한 정보(웹페이지 등) 공유 시스템
* (The) Internet = 전 세계적으로 구축된 단 하나의 internet을 일컫는 말
    * 고유명사로서 internet과 구분됨
* Web(World Wide Web, WWW): Internet을 통한 정보 공유 시스템
    * 마찬가지로 고유명사로서 web과 구분됨

## Web Resource

Web resource is any identifiable resource present on or connected to a web.
This includes web pages as well as references to a web page.

### Uniform Resource Identifier (URI)

URI, a.k.a. web address, is a reference to a web resource that specifies the location of a web server which serves that resource in an internet and a mechanism for retrieving it.
It consists of application level protocol(ex. http) to get resource transfered, a location of a web server in an internet and an identifier for web server to identify a resource.

For example, `https://github.com/eugene-crypto-solutions/onboarding` refers to a resource `/eugene-crypto-solutions/onboarding` which is served by a web server identified by a domain name `github.com`, transferred by `https` protocol.

## Web Page

A web page is a structured document available in a web that primarily consists of hypertexts.
An hypertext is a text with hyperlink.
An hyperlink is a reference to other web resources including other web pages or different sections of a same web page.

A web page is generated from a text file written in HyperText Markup Language(HTML) that describes the content of the web page.
Multimedia contents on a web such as image, videos can be directly embedded in a web page to form a compound document.

An HTML document can include Cascading Style Sheets(CSS) documents to describe the presentation of content on a web page.

A web page can also include JavaScript or WebAssembly programs, which are executed by the web browser to add dynamic behavior to the web page.
In this case, a web page is dynamic web page since the contents a web page is dynamic.
More generally, a dynmaic web page is a web page that the contents could be changed, by the time, requestor, etc (dynamic).
On the other hand, static web page is delivered exactly as stored in web server.
It showes exactly same contents all the time.

### Client Side Dynamic Web Page

A dynamic web page is called client side dynamic web page if the dynamic contents are created by the browser.
An example above is client side dynamic web page, since the browser executes the application code to create dynamic contents.

### Server Side Dynamic Web Page

A dynamic web page is called server side dynamic web page if the dynamic contents are created by the web server.
This includes some data from database server.

## Web Site

A website (also written as a web site) is a collection of web pages and related content that is identified by a common domain name and published on at least one web server. Examples of notable websites are Google, Facebook, Amazon, and Wikipedia.

### Home Page

A home page is a main web page of a website.

## Web Browser

A web browser is application software for accessing the web resources.
Web browser make a request for a web resource, specified by its URI.

## Web Server

A web server is application software which serves web resources to makes them available on an internet.
When a client, or web browser requested a web resource, then the request is forwarded to the specified web server.
Then the web server responds to the client with requested resource.

## Web Application / Web Service

A web application (or web app) is application software that runs in a web browser, unlike software programs that run locally and natively of the device.
Web applications are delivered on a web to users with an active network connection, by a web server.

Everything involved to provide an interactive experience to the uesrs, such as dynamic web page, web server, databases, form a web app.
This is why it is hard to define web application.
In my opinion, web application is any application that runs on top of a web,
interacting with user, handles user request on a web and returns a web resources.

For example, [Google](https://www.google.com) is a web application.
At first the user(browser) requests the web page to a web server with domain name `www.google.com`.
This may be a dynamic web page, an HTML document with JavaScript or WebAssembly programs included.
When an HTML document with those are returned from the web server, the browser runs those.

This is a **Frontend** of the web application, which runs on the client side(browser).

## Web Application Server

A web application server(WAS) is application software which serves the requests from the frontend of web application.

Not all part of web application can run on client side.
Some services might have to run on the server side.
For example, it is better to delegate an operation over the database to the server side, to protect data.

This component is called web application server, which contains business logic.
Usually WAS also works as a web server, but for various reason, it is common to seperate web server from was.
In this case web server is only in charge of creating a web page.
If client requested for server-side dynamic content, or server-side dynamic web page, then the web server forwards a request to WAS.
Then using the response from WAS, web server either forward the response back to the client, or use the response to create a web page.

This is **backend** of the web application, which runs on the server side(WAS).

## Web Server vs. Web Application Server

* Web Server = Web resource를 serve하는 server (Apache, NGINX 등)
* Web Application = Web 위에서 동작하는 application (= client + server)
    * Web Application Client = Application code/script that runs on browser
    * Web Application Server = Business Logic을 수행하며 사용자의 요청을 serve (Framework를 통한 개발)
