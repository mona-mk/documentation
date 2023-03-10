**Polling and Streaming**
In systems, clients and servers communicate together in which client requests and server responds. But at what frequency? Polling is defined as the process when a client requests a particular piece of data at regular intervals (e.g. every x seconds) and the server responds. Example of Polling application: getting temperature updates 30sec/1min of regular intervals. However, there are applications like chat app where you have many clients that are meant to communicate with each other in real-time (messages, chat). But Polling is not suitable here, because the client feels the delay. Streaming is done through sockets.  In Layman terms, sockets are a file that your computer can write/read from in a long width connection with another computer, an open connection till one machine turns it off. Here you may think of reducing the set intervals to 1sec/0.5sec and use Polling instead of Streaming. However, within 10 seconds you are requesting up to 20 requests for a single client, and for millions of clients, this would create an issue for server to handle these requests at the same time.

---
**Duck typing**

Duck typing in computer programming is an application of the duck test—"If it walks like a duck and it quacks like a duck, then it must be a duck"—to determine whether an object can be used for a particular purpose. With nominative typing, an object is of a given type if it is declared to be (or if a type's association with the object is inferred through mechanisms such as object inheritance). In duck typing, an object is of a given type if it has all methods and properties required by that type.

---
**serialisation**

Serialisation is the process of translating a data structure or object state into a format that can be stored or transmitted and reconstructed later, possibly in a different computer environment. A serialisation language means that applications written in different languages which have different data structures, can transfer data between each other using a common agreed on standard format (like JSON, XML, YAML)

---