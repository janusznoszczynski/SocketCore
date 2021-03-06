# Getting started with workflows

Workflows is a layer build on the top of [connections](https://github.com/janusznoszczynski/SocketCore/blob/master/GettingStartedWithConnections.md). 
This is actually recommended way of using SockerCore framework instead of low level connections.
Workflows provide you with messaging framework incorporating connections (web sockets) to transfer messages between server and client.
As every messaging system it has two fundamental concepts: messages and channels.

Message is base structure for data exchanged between server and client. 
It contains useful properties that are used across many features of workflows subsystem:
* MessageId: string - unique message identifier (if not set defaults to new GUID)
* ConnectionId: string - unique persisted connection identifier (set automatically when client connects)
* SessionId: string - unique session identifier (if not set defaults to new GUID)
* SenderId: string - unique identifier for the client (if not set defaults to "WebClient")
* ReplyToMessageId: string - unique "request" message identifier (correlates request-reply messages)

To better understand single communication cycle let's do quick example:
1. Web browser requests page containing SockerCore

2. script on the page creates a workflow's client (SocketCore.Client.JavaScript library) and initiates session
```js
var client = new socketCore.workflowClient("/realtime", { senderId: "MyWebApp" });
```
SessionId and SenderId properties are initialized.

3. script on the page opens messaging realtime communication with the server:
```js
client.run(function () { ... });
```

4. workflow middleware recieves new connection and sends connection id back to the client

ClientId property is initialized.

5. Now we have established connection and we can exchange messages between client and server.


Message has also some properties desired for application specyfic tasks:
* Namespace: string - scope for related messages (eg defined by single component/module/subsystem)
* Type: string - identifies different message types inside namespace (eg: SaveUser, GenerateReport, RenderTasksList)
* Data: object - arbitary payload related to the message type (JSON serializable)

Here are a few examples of messages...

Message for representing command sent to the server for saving user data:
```json
{
    "Namespace": "http://my-app.io/module1/component2",
    "Type": "SaveUser",
    "Data": {
        "Id": 123,
        "FirstName": "Janusz",
        "SecondName": "Noszczyński"
    }
}
```

Message for representing command sent to the server for generating report:
```json
{
    "Namespace": "Ecommerce.Reporting",
    "Type": "GenerateLatestOrdersReport",
    "Data": {
        "TimeFrame": "4h",
        "TotalAmount": ">1M"
    }
}
```

Message for representing command sent to the client for displaying tasks from database:
```json
{
    "Namespace": "TaskApp/TaskList",
    "Type": "Render",
    "Data": [
        { "Title": "Task 1", "DueDate": "6th of May 2020" },
        { "Title": "Task 2", "DueDate": "12th of December 2018" }
    ]
}
```

Message contains also generic Headers property which is an array of name-value objects ({ "Name": "mykey", "Value": "myvalue" }).


# How to set it up?

## 1. Install nuget package for the middleware:
**> dotnet add package SocketCore.Server.AspNetCore**

Create connection class:
```csharp
using System.Threading.Tasks;
using SocketCore.Server.AspNetCore;

namespace YourAppNamesapce
{
    [SubscribeChannel("MyChannel1")]
    public class SimpleWorkflow : WorkflowBase
    {
        protected override Task OnReceived(string connectionId, object data)
        {
            return SendToConnectionsAsync($"Reply to: {data}", connectionId);
        }
    }
}
```

Register services in your Startup class:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    //other stuff...
    
    //RedisConnectionManager is recommended for the production apps
    services.AddSingleton<IConnectionManager, RedisConnectionManager>(provider => new RedisConnectionManager("localhost"));

    //but for development or small apps you can use InProcConnectionManager (no Redis server required):
    //services.AddSingleton<IConnectionManager, InProcConnectionManager>(provider => new InProcConnectionManager());
}
```

Configure middleware in your Startup class:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory, IApplicationLifetime applicationLifetime)
{
    //other stuff

    //below code will require: using SocketCore.Server.AspNetCore;
    app.UseSocketCore("/realtime", new Conn1());
}
```

## 2. Install NPM package for the TypeScript client:
**> npm install --save socketcore-client-typescript**

Import TypeScript module:
```js
import * as socketCore from "socketcore-client-typescript";
```

Create workflow connection object:
```js
let conn  = new socketCore.WorkflowClient("/realtime", {
    senderId: "MyWebApp"
});
```

Subscribe to recieve messages:
```js
this.props.client.subscribe((message: socketCore.Message) => {
    if (message.namespace == "..." && message.type == "...") {
        // process
    }
});
```