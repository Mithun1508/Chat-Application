# Build a Real-Time Simple Functional Chat Application using Node.js and web socket 

# Curious to Know What we are Building?
The WebSocket specification defines an API establishing “socket” connections between a web browser and a server. 
In plain words: With this API, you can send messages to a server and receive event-driven responses without having to poll the server for a reply.

We will use WebSocket API to implement the real-time capabilities of our chat application. We will rely on its library called "ws" which is a pure WebSocket implementation for Node.js.

Ws is pretty simple to use, blazing fast and the code we are going to write will confirm this assumption. So, let’s create the server-side of our chat application in a file called index.js

# How it Works?

1) First create an HTTP server and forward every request to a special handler which will take care to serve all the static files from the public directory. This is needed to access the client-side resources of our application (for example, HTML, JavaScript, and CSS files).

2) We then create a new instance of the WebSocket server, and we attach it to our existing HTTP server. Next, we start listening for incoming WebSocket client connections by attaching an event listener for the connection event.

3)Each time a new client connects to our server, we start listening for incoming messages. When a new message arrives, we broadcast it to all the connected clients.

4) The broadcast() function is a simple iteration over all the known clients, where the send() function is invoked on each connected client. 

# About HTML page 

1) We use the native WebSocket object to initialize a connection to our Node.js server, and then start listening for messages from the server, displaying them in new div elements as they arrive.

2)The messages received from the server is binary data, so we use JavaScript Object called FileReader to read the contents of this data (or raw data buffers).

3)For sending messages, instead, we use a simple textbox and a button within a form.

# Steps for Running the Application 

  1) clone the repo by locally using: "git clone https://github.com/Mithun1508/Chat-Application.git"
  
  2)  Run the cmd: "node index.js"
  
  3)  Then, open a couple of browser tabs or even two different browsers, point them at http://localhost:8080, and start chatting:
  
  4) Now, we want to see what happens when we try to scale our application by launching multiple instances. Let’s try to do that. Let’s start another server on another port:  node index.js 8081
  
  Final Chat Application looks this :
  
 ![Screenshot (60)](https://user-images.githubusercontent.com/93249038/213846882-0f0d8705-83a5-4668-a1f4-d84c174f4a2e.png)
 
 Deployed Final Chat Application On vercel. Do check out the below link :
 
# Site is Live : https://csb-xdk2b4-or3lv2m78-mithun1508.vercel.app/

