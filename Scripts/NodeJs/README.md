# NodeJS

- [Big pictures](#big-pictures)
- [Node.js: File System, Streams, Async IO, Uploads, and Pipes](./NodeJS_IO.md)
- [Modern NPM script](./ModernNPMScript.md)
- [Node.js in the Terminal](./NodeJS_InTheTerminal.md)

## Big pictures

- Node.js is the runtime environment for javascript (i.e. replace the browser as the running container) --> allow javascript to run on desktop, interact with OS, etc.
- It has an extensive libraries of components, updated continuously
- Characteristics: light-weight, single threads but is event-driven ==> can handle concurrent tasks in pseudo-parallel
- `npm`: package management system (i.e. registry, commandline tools to install & manage packages), run node
  - `npm audit`: check for security problems with installed packages
  - `npm update`: update installed package (normally will keep the same major version)
  - `npm audit fix`: specifically fix security problems by pulling patched version

## Project ideas

### Beginner Projects

These focus on core Node.js concepts like the file system, modules, and basic HTTP servers.

1. Task Tracker CLI: Build a command-line interface (CLI) to manage a to-do list. You'll learn to handle user input and read/write data to local JSON files.
2. Simple Web Server: Create a "Hello World" server using the built-in http module without any external frameworks. This helps you understand how requests and responses work at a fundamental level.
3. Weather App: Fetch live data from a public API (like OpenWeatherMap) and display it based on user input. This is great for learning how to use the fetch API or libraries like axios.

### Intermediate Projects

These introduce frameworks like Express.js and databases like MongoDB.

1. Blogging Platform API: Develop a RESTful API where users can create, read, update, and delete (CRUD) blog posts. You'll use Express.js for routing and a database to persist data.
2. Real-Time Chat App: Use Socket.io to enable instant messaging between multiple users. This project teaches you about WebSockets and event-driven architecture.
3. Secure Login System: Build an authentication system using Passport.js and JWT (JSON Web Tokens). You'll learn about password hashing (with bcrypt) and session management.

### Advanced & Portfolio Projects

1. Job Search Application: Create a platform that fetches job listings from external APIs (like Adzuna), includes user accounts, and lets users save listings.
2. Full-Stack MERN App: Build a social media or "memories" app using the MERN stack (MongoDB, Express, React, Node). This demonstrates your ability to connect a modern frontend with a Node.js backend.
3. E-commerce API with GraphQL: Move beyond REST by building an API using GraphQL. This is highly relevant for modern enterprise development.

### Recommended Learning Resources

- [Node.js Official Introduction](https://nodejs.org/learn/getting-started/introduction-to-nodejs): The best place to start for absolute beginners.
- [Roadmap.sh Node.js Projects](https://nodejs.org/learn/getting-started/introduction-to-nodejs): A curated path from beginner to advanced ideas.
