# Getting started with persistend connections

## 1. Install nuget package for the middleware:
**> dotnet add package SocketCore.Server.AspNetCore**

Create connection class:
```csharp
using System.Threading.Tasks;
using SocketCore.Server.AspNetCore;

namespace YourAppNamesapce
{
    public class SimpleConnection : Connection
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

## 2. Install bower package for the js client:
**> bower install socketcore-client-javascript**

Add script reference to your html page:
```html
<script src="/lib/socketcore-client-javascript/SocketCore.js"></script>
```

Create connection object:
```js
var conn  = new socketCore.connection("/realtime");

conn.stateChanged(function(state){
    console.log("state changed: " + state.current);
});

conn.opening(function(){
    console.log("opening: " + this.connectionId);
});

conn.opened(function(){
    console.log("opened: " + conn.connectionId);
});

conn.reopening(function(){
    console.log("reopening: " + conn.connectionId);
});

conn.closed(function(){
    console.log("closed: " + conn.connectionId);
});

conn.recieved(function(data){
    console.log("recieved (" + conn.connectionId + "): " + data);
});

conn.sent(function(data){
    console.log("sent (" + conn.connectionId + "): " + data);
});

conn.error(function(evt){
    console.log(evt);
});

conn.opened(function(){
    conn.send("Hello!!!");
});

conn.open();
```