# SocketCore

SocketCore is a library for easy developing scalable realtime functionality in your .NET Core applications.
Conceptually very similar to SignalR.

It uses **only web sockets** as communication technology without support for any other technologies like long polling, forever frame, etc.
This gives a solid and future foundations for developing thruly persistend full-duplex communication in modern application.

Another core conncept is **scalability by default**.
Which means that every application by default will be able to use multiple instances to feed connected clients.
Internally it is realized by leveraging messaging infrastructure like Redis, but there is an option to switch to single process in-memory communication.

## Both persisted full-duplex communication and messaging architecture gives application developers amazying capabilities:
* send data to 'ConnectionID' (GUID or named connection like 'john_firefox_1')
* mananging connection's groups to support realtime collaboration features
* reacting on events like client connected/disconnedted

Any application component/instance will be able to communicate with any connected client just by knowing its connection id without the need of being physically connected. This is huge opportunity to use microservices architecture and make applications very scalable.

## Current features
* Persisted connections
* Naming connections
* Connection's groups
* Workflows (client-server messaging layer on the top of connections.. like Hubs in SignalR or Controllers in MVC)
* Redis and in-memory connection providers
* TypeScript client

## Future
* More connection providers like RabbitMQ or Azure Event Hubs
* Port client and server to other languages (.NET, Node.js, Python, etc)
* Big vision is to have unified dev experience across different tech stacks


## Learn more
* [Getting started with workflows](https://github.com/janusznoszczynski/SocketCore/blob/master/GettingStartedWithWorkflows.md)
* [Getting started with persistent connections](https://github.com/janusznoszczynski/SocketCore/blob/master/GettingStartedWithConnections.md)


## Related repos
* [SocketCore client for TypeScript](https://github.com/janusznoszczynski/SocketCore.Client.TypeScript)
* [SocketCore client for JavaScript](https://github.com/janusznoszczynski/SocketCore.Client.JavaScript)
* [SocketCore server for ASP.NET Core](https://github.com/janusznoszczynski/SocketCore.Server.AspNetCore)

