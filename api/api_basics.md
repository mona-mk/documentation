# API and Web Service Introduction


**Application Programming Interface (API)**

Interface = abstract away the implementation
interfaces are all around us, like an interface of a radio i.e. the buttons etc. Interface provides developers with means of controlling the interactions while abstracting away how that actually works implementation-wise.  A developer is also working with interfaces that abstract away some of the code for her. For instance, she implements the code that happens when the button is clicked, but the button itself is an interface and she didn’t write how the button makes a sound when clicked or changes appearance when clicked. A button is part of the API, that is available from the application framework that was used to create the app. For playing a song, again the developer just knows that she needs to call the “play” method of the media player API that again abstracts away how to send audio data to hardware. So, there is APIs down to 1s and 0s providing abstractions at many levels. All interfaces have one thing in common: they all define ways for us to interact or communicate with an object (physical or software) and as a user of that interface we don’t need to know the implementation, we only need to know what we are allowed to change or see. While UI is an interface for a user, the API is an interface for an application programmer to use and extend in their application. An API is a contract which defines how it is expected to be used and what you can expect by using it. It is a set of tools designed for software developers. These days the term API is almost always used for “Web based APIs”. However, there are many other APIs. For instance to upper case an string in any language you will call an API to do so (python: string.uppper()). If you don’t want to do this, then you have to do some low-level bit math on each character. It exists because it is a common problem that developers run into. Framework provides an API that you can extend what is provided, to make it your own. This is again another API. We use APIs to avoid recreating the wheels.

* Application: software that does a task: What software has the program that is to be run?
* Programming: Program (P) that does the task in Application (A): What does the program do?
* Interface: Place (I) to tell the program (P) to run. From where are you telling the program to run?

Three things happen in each API transaction:
1. a request is made for something to be done
2. a program is run to complete that request
3. the program sends back the response

In case of say google search for `tuna`, the program is located at the url (www.google.com), this is the google computer. And where exactly the program is located on the google computer? In the search directory (`/search`), and you add the search term as a parameter (`?q=tuna` parameter symbol and q for query) => `wwww.google.com/search?q=tuna`

To checkout some API out there: [www.programmableweb.com](www.programmableweb.com), ebay developers program, 
---
**API mashup**

* mashup: a mixture or fusion of disparate elements
* API Mashup: API built from other APIs (like skyscanner)

---
**Web Service**

Web Service is an API that uses the internet. Not all APIs use the web, but those that do are web service. Web services typically use XML or JSON to format data and send data over the internet. You can't just use any format, you have to use certain format. Also, when sending data, you have to use some kind of protocols. These protocols are: REST, SOAP, XML/RPC.

---
**HTTP**

When an API is a web service, we call the computer where the application is running using HTTP (HyperText Transfer Protocol) and the computer responds with HTTP. HTTP functions as a request–response protocol in the client–server model. 
* Hypertext: www.google.com is a text, hypertext makes a text to go hyper, go somewhere else. Hyper text is a text that can be linked to other text.
* basic parts of a url: https (scheme) + (s stands for extra security)://www.google.com (server) / search (path)


Request & reponse both have four parts:

1. start line:

| | request | response |
| ----------- | ----------- | ----------- |
| Name | start line, request line | start line, response line, status line |
| HTTP version | HTTP/1.1 | HTTP/1.1 |
| Method | GET, POST, PUT, DELETE, etc | No |
| API program folder location | yes(example: `/search`) | No |
| Parameters | yes(example: `?q=tuna`) | No |
| Status code | No | Yes (example: 200 OK) |
| format | Method(space)API program folder location+Parameters(space)HTTP version | HTTP version+status code |
| example | GET /search?q=tuna HTTP/1.1 | HTTP/1.1 200 OK |

NP. what is idempotence? A request method is idempotent if multiple identical requests with that method have the same effect as a single such request.
| method | idempotent |
| ----------- | ----------- |
| GET | Yes |
| POST | No |
| PUT | Yes |
| DELETE | yes |


2. headers

You can have as many or as few header lines as you want. Header fields are colon-separated key-value pairs in clear-text string format, terminated by a carriage return (CR) and line feed (LF) character sequence. Example: `Content-Type: application/json` or `Server: Apache 2.4.1 (Unix)`
Refer to [list of header lines from wikipedia](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields) to see available headers for request and response.

3. blank line

Without the blank line you wouldn't be able to tell when headers finished and body started.

4. body

Body contains the contents. It could be an image, video, HTML page, etc. The header `Content-Type: type/sub-type` defines what type of information the body holds. Typically for sending and receiving data (i.e. text information) we use either JSON or XML.

---
** HTTP Infrastructure (Hardware and Software)**

| | No HTTP (e.g. telephone from 80s) | HTTP(e.g. Amazon) |
| ----------- | ----------- | ----------- |
| Application | Bell | Amazon |
| Client(Requester) | Person calling | Browser, Program |
| Initial receiver | Telephone | Load Balancer |
| Server | Person answering | Application server |
| Client-Server Connection | Copper wires | Cabels or satellite |
| Memory | Brain of person | Database |

Stateless infrustructure benefits:
* Scalability (no memory needs to be kept)
* Reslience (Crashability)
* Less memory needed

Web:

Before we understand REST, since REST uses web, we need to understand web. The browser is a web client and you use it to connect to a web server. You put a location in the address bar: Universal Resource Locator (URL) or its superset term URI (identifier). The URL has a scheme portion, and it may happen to be http (HyperText Transfer Protocol). Protocol is similar to an API but lower level, and is more like a contract. It defines how to communicate. HTTP is the protocol that is used for the World Wide Web. Whenever your browser requests a page, it does it by contacting a web server and making the request with HTTP. The server then responds by sending the page, again with HTTP. So the browser creates an HTTP request for you along with a URL and a particular HTTP verb such as GET which mean it will only receive data and won’t change any data. The server receives that request that was TRANSFERed from the client. It processes the request and sends back a response to my browser. This response contains a body that for a web page it contains an HTML (Hyper Text Markup Language). Hyper text is a text that can be linked to other text. With this response the browser renders the page. HTTP, as was originally designed, was a stateless protocol. Once the request received a response it is done. If you want to keep state, the client has to manage it itself, and send that state in each new HTTP request. There are many more HTTP request methods besides GET. There are more information in both the request and the response. For instance parameters are passed after `?` mark, or key/value pairs that are called headers. Both requests and responses have headers, they are further used to communicate what is wanted. Some of the popular headers are e.g. Current Language or Content Type. You can also ask only if it has changed which allows for caching. You can add authentication information in each request. The response header for instance typically has status code which shows what has happened on the server side. [List of URI schemes aside from HTTP scheme](https://en.wikipedia.org/wiki/List_of_URI_schemes)

---
**server**

Services running on your computer are from the servers running on your computer. From the view of a computer, a server(software) is just a set of services which is available to clients on the network. A server offers one or more services. An example of Server as software: Apache HTTP server.

---
**Cookies**

NB. HTTP is stateless by default. State+less mean the request is unknown. REST and SOAP both use HTTP, therefore, both are by default stateless. Example of stateful protocol is FTP (File Transfer Protocol) where you have two computers and you want to transfer files between them. This is where you know before the request is made, where you want to send it and to what computer. But in case of HTTP which is stateless, how Amazon remembers you added something to your card? With Cookies. The session id (browser sends it to Amazon, and the applications asks the browser to Set-Cookies) will be in the cookies, then the session between application and client can be continued. Cookies are set using header lines.
* Cookies are not executable, they are just text.
* Cookied don't store passwords (e.g. can use tokens)
* Applications store data with session_id

---
**XML**

eXtensible Markup Language. It is a Content-Type in HTTP header (application/xml). XLM uses tags `<>` just like HTML uses tags. The difference between XML and HTML is this though: terms between tags don't mean anything in XML, but they have specific meanings in HTML. (ex: `<button>Click me</button>` in XML button has just the value of click me, while in HTML it will create a button). XML and HTML where both created by W3C organization. "eXtensible" in XML means you can extend it and customise it, while in HTML tags mean something and can't be extended.

---
**JSON**

JavaScript Object Notation. In your browser, you can install an extension to read JSON in a collabsable colorful fashion.

---
**Remote APIs , REST and RESTful**

A tv control is a remote interface to remotely control an object, and a drone in remotely controlled by an app on your phone. It is not just physical objects that can benefit from remote API. Often we don’t have space on our local machine for all the data that is available. For instance using Shazam on your phone, you use a remote API that compares your pieces of music with all the music there is in the world. Another problem that remote APIs solve is the computational power where many processing will happen on a remote machine. It took sometime to come up with a common style for remote APIs, but then a new architectural style appeared that was clear and allowed developers to interact with specific resources over the web, and everyone started using and providing this: REST (Representational STATE TRANSFER) which had the abstraction developers needed. When APIs conform to the style and constrains of REST, they are called RESTful APIs.

---
**SOAP**

Simple Object Access Protocol. Every API/web services that is on the internet uses WSDL(Web Service Description Language). WSDL is an XML and it describes the service. SOAP was created by W3C. The 4 parts of HTTP:
1. Start line: POST, WSDL location, HTTP version. In SOAP, we only use POST, it is a placeholder, because the method is not used. 
2. Header line: Content-Type: text/xml
3. Blank line
4. Body XML envelope formed using the WSDL

---
**SOAP vs REST**

History of API:

| Time | API method  |
| ----------- | ----------- |
| 1980s | SUN RPC (Remote Procedure Call) |
| 1988 | XML-RPC |
| 1999 | SOAP |
| 2000 | REST |

They both ways to create web services, i.e. ways to form HTTP response and request. The difference is that in REST the METHOD is used in HTTP start line(GET, POST, etc) while in SOAP it is always POST. REST is designed to be cacheable whereas SOAP is just NOT designed to be cachable. REST uses GET - which is easy to cache. Note that Cache keeps the data where you are located (where the client is located, say browser).

---
**REST (Representational State Transfer)**

The 4 parts of HTTP:
1. Start line: All METHODS (GET, POST, etc) 
2. Header line: All
3. Blank line
4. Body Any (JSON, XML, images, html, etc)

If an API conforms to all of REST constrains, it is called a REST API:
* client-server architecture
* statelessness
* layered system
* cacheability
* uniform design
* code on demand

---
**Authentication and Authorization**

* Authentication: proving who you are
* Authorization: limited access

| Name | Authentication | Authorization | Examples |
| ----------- | ----------- | ----------- | ----------- |
| No auth(enticayion) | No | No | google search |
| Basic Auth(entication) | Yes | No | Email account |
| Bearer Token | No | Yes | Not many because anybody can get a bearer token to get access to the service (you don't know who has access)|
| OAuth(orization) | Yes | Yes | many axample. e.g. Wayz app (You get another entity like an app authorization to get the information) |
| Two factor authentication | Yes | No | High security like bank account |

---
**operating system**

Between you and the hardware is the operating system. The most common operating systems are:
* Windows
* Mac OS X
* Linux (made to do big computations behind the scene)
* Android for cellphones
* iOs for cellphones

Each operating system has API on top of it that apps can access. Example of APIs on cellphones: vibrating, microphone, camera, speaker, touchscreen and keyboard which are low-level APIs. In addition to these hardware APIs, there are high level APIs, such as calendar, push notifications, browser, email, contacts.

---
**App**

Three types of cellphone app:
* Native app: run on an operating system. The native app calls the APIs on the operating system. Therefore, the native app has to be specific to the operating system. Twitter, google maps, google translate.
* Web app: does not run directly on operating system, it runs on a browser. The browser itself is an API. Therefore, it is limited to APIs that the browser can call. These look like web pages, because they are built with HTML, CSS,.. Examples are: google, youtube, wikipedia.
* Hybrid app: it has aspects of both. Example: facebook. There are two ways that hybrid apps work.

|  | Native | Hybrid | Web |
| ----------- | ----------- | ----------- | ----------- |
| Quality | X |  |  |
| Speed | X |  |  |
| Cost |  |  | X |
| App store/google platform | X | X |  |
| Device access | XX | X |  |

---
**OAuth2.0**

[Go to RFC 6749 OAuth2.0 specification](https://www.ietf.org/rfc/rfc6749.txt) for all the details.

If you have a website myphotos.com where you have private and public image folders, for you to authenticate to the website, you use your username and password, and you are the resource owner. If you want to authorize access to somebody else to your public photos (a client).

Abstract:
* OAuth allows a 3rd party access
* Limited (Authorized) access
* To a web service (HTTP)
* OAuth1.0 obsolete

Introduction:
* Problem giving your credentials to 3rd party
* OAuth introduces authorization layer
* Instead of credentials, 3rd party gets access tokens
* Must use HTTP

Roles:
* Resource owner
* Resource server
* Client/Application
* Authorization server

The resource server and authorization server can be on different servers, can be two different servers or can be in a single bigger server. What this means is that first the client calls the authorization service and gets the access token, and then with access token calls the resource server.

Protocol flow:
1. Client sends a request to resource owner called an authorization request
2. Resource owner then grants the access to client called authorization grant
3. Client sends the authorization grant to Authorization server
4. Authorization server sends back an access token
5. Client sends the access token to the resource server
6. Resource server sends protected resource to the client

Authorization grant types:
* Authorization code (here the client asks for authorization through authorization server and doesn't ask directly the resource owner. The resource owner then has to log into the authorization server and say yes. This method is the most used method among the these 4 types. For more info check Reference 2.
* implicit: the request is made by code itself (AJAX)
* resource owner: is when the owner of resource itself asks for the resource
* client credentials: if the resource is already public or some arrangements where made beforehand

Refresh token and access token:
* Access tokens usually expire fast. With refresh token you can get a new access token. Therefore, refresh token normally live longer than access tokens.

Open ID Connect:
* It is a way for the client to get information about the resource owner, such as name, location, phone, address, likes, dislikes.
* In OAuth2.0 the client does not get information about the resource owner.
* Open ID Connect is the way to get that information from the resource owner.
* Concern is that Personal Identifiable Information (PII) is given to the client.
* In Open ID Connect, the client in addition to access token, gets an ID token. Then Client sends this two tokens to the resource server.
* Open ID Connect is not part of RFC 6749 organization but is on top of it by another organization.
* ID tokens that are sent back to the client are call JSON WEB TOKENS (JWT). It is in base64, and contains header, resource owner information and third part is signature.

---
**Basic authentication**

A simple way of proving your identity. Access an API using username and password. Not very secure even though you are sending it with HTTPS, since the more you send your username and password, the more your information is exposed. This is why OAuth is way more secure because you use a token that expires.

---
**Digest authentication**

Digest means something that can be converted into something useful. A way of proving your identity using a digest, or accesing an API using a digest. This is an alternative to basic authentication and unlike basic authentication (HTTPS) can use HTTP, because if someone has the digest, they can not know what it means unless the have the secret as well. This method is not that often used. There is a concept of Nonce (number used once), which is a number the server gives back to client that can only be used once. So, if someone else has the digest, it no longer can be used.

---
**Bearer token**

A bearer is a holder of something. Token is used for accessing something. In API world, there are APIs that if you just send then a Bearer token, they don't care who you are and give you access. This method has originated from OAuth2.0 specification. An access token is a brearer token, but access token has a expiration to it. You must protect token. You must use HTTPS because HTTPS encrypts the transmission of a bearer token.

---
**Postman**

Postman is a browser. With postman you can:
* call an API without the need for an interface
* we can see what happens underneath the hood

---
**Webhooks == reverse API == reverse web service**

Web: web service, hook: connected to an event (for instance a payment has occured, logged in, delivery made, etc). So, webhook is when a web service is called when an event happens that has a hook. For instance, you normally send a request to your bank as you being the client. However, when bank sees an unusual log in i.e. an event, the bank sends you a request like a text message and you respond (bank uses the IP of your cellphone). This is a reverse API. 

---
**microservices vs Monolith**

These are multiple APIs rather than small APIs. This is compared to Monolith (mono: one, lith: stone) which means one API. So, this means instead of having one API we have many APIs. There are variety of ways to have microservices. For instance each API can have their own database or all APIs can share a database. 

Some advantages of microservices:
* scalability (you can scale part of the system where there is more load, say scale only the payment service)
* code language independent
* smaller and speciliased team

Some disadvantage of microservices:
* lack of consistently (separate teams, some team may perform poor. You have to train teams on multiple language. You are also looking it as parts rather than one whole)

---
References:
1. [How to use an API (full course / tutorial)](https://www.youtube.com/watch?v=GZvSYJDk-us)
2. [API and Web Service Introduction](https://www.udemy.com/share/101Yrw3@rcUR9xgfJJdm7lYg712fP6qAB1aAxRncUhbdh9WKJQe7GBmmD93Grsb4cTYLxiXBRw==/)
		
