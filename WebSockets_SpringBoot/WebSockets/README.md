### Using WebSocket to build an interactive web application

This guide walks you through the process of creating a "Hello, world" application that sends messages back and forth between a browser and a server. WebSocket is a thin, lightweight layer above TCP. This makes it suitable for using "subprotocols" to embed messages. In this guide, we use `STOMP` messaging with Spring to create an interactive web application. STOMP is a subprotocol operating on top of the lower-level WebSocket.

### What you will build
You will build a server that accepts a message that carries a user's name. In response, the server will push a greeting into a queue to which the client is subscribed. 

### Create a Resource Representation Class
Now that you have set up the project and build system, you can create your STOMP message service. 

Begin the process by thinking about service interactions. 

The service will accept messages that contain a name in a STOMP whose body is a JSON object. 

To model the message that carries the name, you can create a plain old Java object with a `name` property and a corresponding `getName()` method, as the following listing(from `src/main/java/com/example/messagingstompwebsocket/HelloMessage.java`) shows:

Upon receiving the message and extracting the name, the service will process it by creating a greeting and publishing that greeting on a separate queue to which the client is subscribed. The greeting will also be a JSON object, which as the following listing shows:

To model greeting representation, add another plain old Java object with a `content` property and a corresponding `getContent()` method, as the following listing (from `src/main/java/com/example/messagingstompwebsocket/Greeting.java`) shows:

Spring will use the `Jackson JSON` library to automatically marshal instances of type `Greeting` into JSON. 
Next, you will create a controller to receive the hello message and send a greeting message. 

### Create a Message-handling Controller
In Spring's approach to working with STOMP messaging, STOMP messages can be routed to `@Controller` classes. For example, the `GreetingController` from `src/main/java/com/example/messagingstompwebsocket/GreetingController.java` is mapped to handle messages to the `/hello` destination, as the following listing shows:

This controller is concise and simple, but plenty is going on. We break it down step by step.

The `@MessageMapping` annotation ensures that, if a message is sent to the `/hello` destination, the `greeting()` method is called. 

The payload of the message is bound to a `HelloMessage` object, which is passed into `greeting()`.

Internally, the implementation of the method simulates a processing delay by causing the thread to sleep for one second. This is to demonstrate that, after the client sends a message, the server can take as long as it needs to asynchronously process the message. The client can continue with whatever work it needs to do without waiting waiting for the response. 

After the one-second delay, the `greeting()` method creates a `Greeting` object and returns it. The return value is broadcast to all subscribers of `/topic/greetings`, as specified in the `@SendTo` annotation. Note that the name from the input message is sanitized, since in this case, it will be echoed back and re-rendered in the browser DOM on the client side. 

### Configure Spring for STOMP messaging
Now that the essential components of the service are created, you can configure Spring to enable WebSocket and STOMP messaging. 

Create a Java class named `WebSocketConfig` that resembles the following listing(from `src/main/java/com/example/messagingstompwebsocket/WebSocketConfig.java`):

`WebSocketConfig` is annotated with `@Configuration` to indicate that it is a Spring configuration class. It is also annotated with `@EnableWebSocketMessageBroker`. As its name suggests, `@EnableWebSocketMessageBroker` enables WebSocket message handling, backed by a message broker. 

The `configureMessageBroker()` method implements the default method in `WebSocketMessageBrokerConfigurer` to configure the message broker. It starts by calling `enableSimpleBroker()` to enable a simple memory-based message broker to carry the greeting messages back to the client on destinations prefixed with `/topic`. It also designates the `/app` prefix for messages that are bound for methods annotated with `@MessageMapping`. This prefix will be used to define all the message mappings. For example, `/app/hello` is the endpoint that the `GreetingController.greeting()` method is mapped to handle. 

The `registerStompEndpoints()` method registers the `gs-guide-websocket` endpoint for websocket connections. 

### Create a Browser Client
With the server-side pieces in place, you can turn your attention to the JavaScript client that will send messages to and receive messages from the server side. 

Create an `index.html` file similar to the following listing (from `src/main/resources/static/index.html`):

This HTML file imports the `StompJS` javascript library that will be used to communicate with out server through STOMP over websocket. We also import `app.js`, which contains the logic of our client application. The following listing (from `src/main/resources/static/app.js`) shows that file:

The main pieces of this JavaScript file to understand are the `stompClient.onConnect` and sendName functions.

`stompClient` is initialized with `brokerURL` referring to path `/gs-guide-websocket`, which is where our websockets server waits for connections. Upon a successful connection, the client subsribes to the `topic/greetings` destination, where the server will publish greeting messages. When a greeting is received on that destination, it will append a paragraph element to the DOM to display the greeting message. 

The `sendName()` function retrieves the name entered by the user and uses the STOMP client to send it to the `/app/hello` desitination(where `GreetingController.greeting()` will receive it).

The `main.css` can be omitted if you like, or you can create an empty one, just so the `<link>` can be resolved. 

### Make the Application Executable
Spring Boot creates an application class for you. In this case, in needs no further modification. You can use it to run this application. The following listing (from `src/main/java/com/example/messagingstompwebsocket/MessagingStompWebsocketApplication.java`) shows the application class:

`@SpringBootApplication` is a convenience annotation that adds all of the following:

- `@Configuration` : Tags the class as a source of bean definitions for the application context.
- `@EnableAutoConfiguration` : Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, fi `spring-webmvc` is on the class path, this annotation flags the application as a web application and activates key behaviors, such as setting up a `DispatcherServlet`. 
- `@ComponentScan` : Tells Spring to look for other components, configurations, and services in the `com/example` package, letting it find the controllers. 

The `main()` method uses Spring Boot's `SpringApplication.run()` method to launch an application. Did you notice that there was not a single line of XML? There is no `web.xml` file, either. This web application is 100% pure Java and you did not have to deal with configuring any plumbing or infrastructure. 

