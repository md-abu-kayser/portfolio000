# Building Server with Raw Nodejs & Typescript

1.  Building a Server with Node.js
    const http = require("http");

// create server
const server = http.createServer((req, res) => {
// request received
console.log("Request received:", req.url);

// response setup
res.writeHead(200, { "Content-Type": "text/plain" });

// send response
res.end("Hello, this is my first Node.js server!");
});

// run server
const PORT = 3000;

server.listen(PORT, () => {
console.log(`Server is running on http://localhost:${PORT}`);
}); 2. Working with Environment-Based Configuration 3. API Routes & the Difference Between GET and POST 4. Creating a Custom Router 5. Using Our Custom Route Handler 6. Implementing sendJson & Cleaning Up Server.ts 7. Creating parseBody & Building a Clean POST API 8. Setting Up a Fake JSON Database 9. Handling Dynamic Routes 10. Dynamic Route Handling for PUT Requests
