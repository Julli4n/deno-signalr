a Deno port of [node-signalr](https://github.com/alex8088/node-signalr)
# deno-signalr
 A SignalR client for Deno
* ✅ Supports ASP.net
* ✅ Written in TypeScript
* ✅ Supports custom queries and headers (headers only for HTTP and HTTPS)
* ✅ Lots of JSDoc integration
* ✅ Asynchronous


## Credits
 * [node-signalr](https://github.com/alex8088/node-signalr) - (Original) by [alex.wei](https://github.com/alex8088)
 * [Julli4n](https://github.com/Julli4n?tab=repositories) - Ported the original


## License
[MIT](./LICENSE)


## Dependencies
* [Evt](https://deno.land/x/evt)


# Documentation
**NOTiCE: This documentation is as of v0.1.1, usage may change drastically as it reaches v1**


## Importing
As like any other 3rd-party Deno module, it can be imported with the `import` statement to the mod.ts URL.
```typescript
import { SignalR, SignalRError, to } from "https://deno.land/x/deno_signalr/mod.ts";
```


## Creating a client
Assuming that it has already been imported:
```typescript
const MyClient = new SignalR("https://localhost:8080/signalr", [ "MyTestHub" ], { 
    "MyOptionalQuery": "MyOptionalQueryValue"
}, {
    "MyOptionalHeader": "MyOptionalHeaderValue"
});
```
This will create a new SignalR client instance with the parameters given.

`
new SignalR(string endpoint, Array<string> hubs[, Record<string, unknown> query][, Record<string, string> headers])
`


## Configuring a client
After creating the client, it can be configured:
```typescript
// Custom headers
MyClient.headers["MyOptionalHeader"] = "nyOptionalVHeaderValue";
// Custom query
MyClient.query["MyOptionalQuery"] = "MyOptionalQueryValue";
// Timeout for sending messages
MyClient.callTimeout = 10000; // 10s, default: 5000ms (5s)
// Delay time for reconnecting
MyClient.reconnectDelayTime = 2000; // 2s, default: 5000ms (5s)
// Timeout for connecting
MyClient.requestTimeout = 6000; // 6s, default: 5000ms (5s)
```


## Binding client events
Deno-signalr takes advantage of the [Evt](https://deno.land/x/evt) module, which `SignalR` extends:
```typescript
// Attach until dettached
// MyClient.$attach(to(eventName), callback);
// Attach and then dettach after one time
// MyClient.$attachOnce(to(eventName), callback);
MhClient.$attach(to("connected", () => {
    console.log("SignalR in Deno Example: Connected");
}));
MhClient.$attach(to("reconnecting", (connectionCount: number) => {
    console.log(`SignalR in Deno Example: Connecting... ${connectionCount} tries`);
}));
MhClient.$attach(to("disconnected", (reason: string) => {
    console.log(`SignalR in Deno Example: Disconnected, reason "${reason}"`);
}));
MhClient.$attach(to("error", (error: SignalRError) => {
    console.log(`SignalR in Deno Example: Error, code: ${error.code}, message: ${typeof(error.message) === "string" ? error.message : "none"}`);
}));
```


## Binding a hub method

### Bind callback to receive messages
```typescript
MyClient.connection.hub.on("MyHub", "MyMethod", (message: unknown) => {
    console.log(`SignalR in Deno Example: New message from MyHub/MyMethod: ${message.toString()}`);
});
```

`
void SignalRHub.on(string hub, string method, function callback)
`


### Call the method and return the values asynchronously
```typescript
MyClient.connection.hub.call("MyHub", "MyMethod", "hi from SignalR in Deno!").then((result: boolean) => {
    console.log(`SignalR in Deno Example: Sent message to MyHub/MyMethod success: ${result.toString()}`);
});
```

`
Promise<unknown> SignalRHub.call(string hub, string method, unknown message)
`


### Invoke the method without return values
```typescript
MyClient.connection.hub.invoke("MyHub", "MyMethod", "hi from SignalR in Deno!");
```

`
void SignalRHub.invoke(string hub, string method, unknown message)
`


## Starting the connection
When ready to start the connection to the Hubs, start the client:
```typescript
MyClient.start();
```
This will negotiate, start and connect with the hubs.

`
void SignalR.start(numbeer protocol = 1.5)
`


## Ending the connection
When no longer needed, end the connection (if it exists):
```typescript
MyClient.end()
```

`
void SignalR.end()
`