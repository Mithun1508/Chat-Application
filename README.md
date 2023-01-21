# Let's Build a Real-Time Chat App using Node.js and WebSocket

#Final Chat App Looks like this :

Modern web applications often require some form of real-time communication, where data can continuous flow from client to server and vice-versa with minimal delay.

To fulfil this requirement, the HTML5 WebSocket Protocol was created. So, we can exchange messages, information in real-time.

In this post, we will focus on learning how to build a very basic real-time web application using WebSocket.

# Implementing the Server
Let us get started with the server code. First, open a new terminal and create a folder named chat-app in the root directory. Dive to this folder and initialize the project by entering npm init or yarn init command in the terminal. Now that we initialized our app, let's know a bit more about WebSocket.

The WebSocket specification defines an API establishing “socket” connections between a web browser and a server. In plain words: With this API, you can send messages to a server and receive event-driven responses without having to poll the server for a reply.

We will use WebSocket API to implement the real-time capabilities of our chat application. We will rely on its library called ws which is a pure WebSocket implementation for Node.js.

Ws is pretty simple to use, blazing fast and the code we are going to write will confirm this assumption. So, let’s create the server-side of our chat application in a file called index.js

chat-app/index.js
import { createServer } from 'http';
import staticHandler from 'serve-handler';
import ws, { WebSocketServer } from 'ws';
//serve static folder
const server = createServer((req, res) => {   // (1)
    return staticHandler(req, res, { public: 'public' })
});
const wss = new WebSocketServer({ server }) // (2)
wss.on('connection', (client) => {
    console.log('Client connected !')
    client.on('message', (msg) => {    // (3)
        console.log(`Message:${msg}`);
        broadcast(msg)
    })
})
function broadcast(msg) {       // (4)
    for (const client of wss.clients) {
        if (client.readyState === ws.OPEN) {
            client.send(msg)
        }
    }
}
server.listen(process.argv[2] || 8080, () => {
    console.log(`server listening...`);
})
That’s it! That’s all we need to implement the server-side component of our chat application. This is how it works:

We first create an HTTP server and forward every request to a special handler which will take care to serve all the static files from the public directory. This is needed to access the client-side resources of our application (for example, HTML, JavaScript, and CSS files).

We then create a new instance of the WebSocket server, and we attach it to our existing HTTP server. Next, we start listening for incoming WebSocket client connections by attaching an event listener for the connection event.

Each time a new client connects to our server, we start listening for incoming messages. When a new message arrives, we broadcast it to all the connected clients.

he broadcast() function is a simple iteration over all the known clients, where the send() function is invoked on each connected client. This is the magic of Node.js! Of course, the server that we just implemented is very minimal and basic, but as we will see, it does its job.

Implementing the Client
Next, it’s time to implement the client-side of our chat application. This can be done with another compact and simple fragment of code, essentially a minimal HTML page with some basic JavaScript code. Let’s create this page in a file named public/index.html as follows:

chat-app/public/index.html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="index.css">
    <title>Minimlist chat App</title>
</head>

<body>
    <div class="container">
        <p class="msg">Messages:</p>
        <div id="messages" class="messages"></div>
        <form id="msgForm" class="msgForm">
            <input type="text" placeholder="Send message" class="input" id="inputBox" />
            <input type="submit" class="btn" value="Send">
        </form>
    </div>
    <script type="text/javascript">
        const ws = new WebSocket(`ws://${window.document.location.host}`);
        ws.binaryType = "blob";
        // Log socket opening and closing
        ws.addEventListener("open", event => {
            console.log("Websocket connection opened");
        });
        ws.addEventListener("close", event => {
            console.log("Websocket connection closed");
        });
        ws.onmessage = function (message) {
            const msgDiv = document.createElement('div');
            msgDiv.classList.add('msgCtn');
            if (message.data instanceof Blob) {
                reader = new FileReader();
                reader.onload = () => {
                    msgDiv.innerHTML = reader.result;
                    document.getElementById('messages').appendChild(msgDiv);
                };
                reader.readAsText(message.data);
            } else {
                console.log("Result2: " + message.data);
                msgDiv.innerHTML = message.data;
                document.getElementById('messages').appendChild(msgDiv);
            }
        }
        const form = document.getElementById('msgForm');
        form.addEventListener('submit', (event) => {
            event.preventDefault();
            const message = document.getElementById('inputBox').value;
            ws.send(message);
            document.getElementById('inputBox').value = ''
        })
    </script>
</body>

</html>
Let’s add some styles to a file called index.css in the public directory to make our app look better.

chat-app/pulic/index.css
html{
font-size: 62.5%;
}
body{
margin:0;
padding: 0;
display:flex;
justify-content:center;
}
.container{
margin-top: 5rem;
display:flex;
flex-direction:column;
width: 30%;
height: 70vh;
}
.msg{
color:blueviolet;
font-size: 2rem;
}
.msgCtn{
margin: 1rem 0;
color: white;
padding: 1rem 1rem;
background-color: blueviolet;
border-radius: 6px;
font-size: 2rem
}
.msgForm{
display:flex;
flex-direction:row;
}
.input{
height: 100%;
flex: 4;
margin-right: 2rem;
border:none;
border-radius: .5rem;
padding: 2px 1rem;
font-size: 1.5rem;
background-color:aliceblue;
}
.input:focus{
background-color:white;
}
.btn{
height:5rem;
flex: 1;
display:flex;
justify-content:center;
align-items:center;
border-radius: .5rem;
background-color:blueviolet;
color: whitesmoke;
border:none;
font-size: 1.6rem;
}
The HTML page we just created doesn’t really need many comments, it’s just a piece of straightforward web development.

We use the native WebSocket object to initialize a connection to our Node.js server, and then start listening for messages from the server, displaying them in new div elements as they arrive.

The messages received from the server is binary data, so we use JavaScript Object called FileReader to read the contents of this data (or raw data buffers).

For sending messages, instead, we use a simple textbox and a button within a form.

Running the Application
We can try to run our application immediately. Just launch the server with the following command:
node index.js 8080
Then, open a couple of browser tabs or even two different browsers, point them at http://localhost:8080, and start chatting:

Image chat app

Now, we want to see what happens when we try to scale our application by launching multiple instances. Let’s try to do that. Let’s start another server on another port:
node index.js 8081
The desired outcome should be that two different clients, connected to two different servers, should be able to exchange chat messages. Unfortunately, this is not what happens with our current implementation. We can test this by opening another browser tab to http://localhost:8081.

When sending a chat message on one instance, we only broadcast the message locally, distributing it only to the clients connected to that particular server. In practice, the two servers are not talking to each other. We need to integrate them. To do that we need to use a message broker like RabbitMQ or Redis.
