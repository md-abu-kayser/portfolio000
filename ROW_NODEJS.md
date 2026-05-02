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
});

2.  Working with Environment-Based Configuration
    import dotenv from "dotenv";
    dotenv.config();

const PORT = process.env.PORT || 3000;

console.log("Running on port:", PORT);

3. API Routes & the Difference Between GET and POST
   import http from "http";

const server = http.createServer((req, res) => {
if (req.method === "GET" && req.url === "/users") {
res.end("Get all users");
}

if (req.method === "POST" && req.url === "/users") {
res.end("Create user");
}
});

server.listen(3000);

4. Creating a Custom Router
   type Handler = (req: any, res: any) => void;

const routes: Record<string, Handler> = {};

export const addRoute = (method: string, path: string, handler: Handler) => {
routes[`${method}:${path}`] = handler;
};

export const handleRoute = (req: any, res: any) => {
const key = `${req.method}:${req.url}`;
const handler = routes[key];

if (handler) {
handler(req, res);
} else {
res.statusCode = 404;
res.end("Not Found");
}
};

5. Using Our Custom Route Handler

import http from "http";
import { addRoute, handleRoute } from "./router";

addRoute("GET", "/users", (req, res) => {
res.end("All users");
});

addRoute("POST", "/users", (req, res) => {
res.end("User created");
});

const server = http.createServer(handleRoute);

server.listen(3000);

6.  Implementing sendJson & Cleaning Up Server.ts
    export const sendJson = (res: any, data: any, status = 200) => {
    res.writeHead(status, { "Content-Type": "application/json" });
    res.end(JSON.stringify(data));
    };

7.  Creating parseBody & Building a Clean POST API
    export const parseBody = (req: any): Promise<any> => {
    return new Promise((resolve, reject) => {
    let body = "";

        req.on("data", (chunk: any) => {
          body += chunk;
        });

        req.on("end", () => {
          try {
            resolve(JSON.parse(body));
          } catch {
            resolve({});
          }
        });

    });
    };

8.  Setting Up a Fake JSON Database
    {
    "users": []
    }
    import fs from "fs";

const DB_FILE = "./db.json";

export const readDB = () => {
const data = fs.readFileSync(DB_FILE, "utf-8");
return JSON.parse(data);
};

export const writeDB = (data: any) => {
fs.writeFileSync(DB_FILE, JSON.stringify(data, null, 2));
};

9. Handling Dynamic Routes
   const matchRoute = (url: string) => {
   const match = url.match(/^\/users\/(\d+)$/);
   return match ? match[1] : null;
   };

const userId = matchRoute(req.url);

if (req.method === "GET" && userId) {
const db = readDB();
const user = db.users.find((u: any) => u.id == userId);

sendJson(res, user || { message: "User not found" });
}

10. Dynamic Route Handling for PUT Requests
    if (req.method === "PUT" && userId) {
    const body = await parseBody(req);
    const db = readDB();

const index = db.users.findIndex((u: any) => u.id == userId);

if (index === -1) {
return sendJson(res, { message: "User not found" }, 404);
}

db.users[index] = { ...db.users[index], ...body };

writeDB(db);

sendJson(res, db.users[index]);
}
