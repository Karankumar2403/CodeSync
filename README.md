# ⚡ CodeSync

A modern, high-performance real-time collaborative code editing platform designed for developers, educators, and remote pairs. Built on top of the **MERN** stack and powered by **Socket.IO**, CodeSync facilitates seamless, low-latency code sharing and synchronous pair programming directly in the browser.

---

## 🌟 Key Highlights

*   **Lag-Free Collaborative Editing**: Experience zero-latency code syncing across multiple clients in a shared environment.
*   **Presence Indicators & Cursor Telemetry**: Monitor connected teammates with customizable avatar cards and live cursor positions highlighted in real time.
*   **Monaco-Powered Canvas**: Integrates Microsoft's Monaco Editor engine, supplying professional autocomplete, syntax highlighting, bracket auto-matching, and multi-language support.
*   **Persistent Workspaces & JWT Security**: Offers both guest access for rapid sessions and robust user accounts using JSON Web Tokens (JWT) for saving work history.
*   **In-App Peer Chat**: A minimalist, high-speed chat sidebar allows team members to communicate without switching tabs.
*   **Sleek Glassmorphism Interface**: Styled with curated dark-mode HSL gradients, clean typography, and subtle micro-animations.

---

## 🏗️ Architecture Design

```mermaid
graph TB
    subgraph Client-Side (React Application)
        Vite[Vite Bundler]
        Monaco[Monaco Editor Component]
        SocketClient[Socket.IO Client Engine]
        State[React Context / State]
        
        Vite --> Monaco
        Vite --> SocketClient
        Vite --> State
    end

    subgraph Backend Services (Node.js & Express)
        Server[Express App Server]
        SocketServer[Socket.IO Web Server]
        Auth[JWT Cryptography Middleware]
        
        SocketClient <-->|WebSockets (WS)| SocketServer
        Vite <-->|HTTP REST API| Server
        Server --> Auth
    end

    subgraph Database Layer
        DB[(MongoDB Cloud Database)]
        Server <-->|Mongoose ODM| DB
    end

    classDef client fill:#4c1d95,stroke:#8b5cf6,stroke-width:2px,color:#fff;
    classDef server fill:#1e3a8a,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef database fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#fff;
    
    class Vite,Monaco,SocketClient,State client;
    class Server,SocketServer,Auth server;
    class DB database;
```

---

## 🛠️ Technology Stack

*   **Frontend**: React (Vite), Tailwind CSS, Monaco Editor (`@monaco-editor/react`), Socket.io-client, React Router Dom, React Hot Toast.
*   **Backend**: Node.js, Express.js, Socket.IO, Bcrypt.js, Mongoose.
*   **Database**: MongoDB.

---

## 📁 Repository Directory Structure

```text
codesync/
├── backend/
│   ├── config/             # Database and external integrations config
│   ├── controllers/        # Express handlers for incoming requests
│   ├── middleware/         # Token validation and security checks
│   ├── models/             # Mongoose MongoDB schemas
│   ├── routes/             # REST endpoint routing definitions
│   ├── socket/             # Socket.IO connection event channels
│   ├── .env.example        # Environment setup template for the server
│   ├── package.json        # Server configuration and dependencies manifest
│   └── server.js           # Express app bootstrapper
└── frontend/
    ├── public/             # Static public assets
    ├── src/
    │   ├── assets/         # CSS style sheets, fonts, and assets
    │   ├── components/     # Reusable React buttons, overlays, inputs
    │   ├── context/        # Session and theme state contexts
    │   ├── hooks/          # Sockets initialization and authentication hooks
    │   ├── pages/          # App views (Landing, login, dashboards, workspace)
    │   ├── App.jsx         # App routes manager
    │   ├── index.css       # Core Tailwind CSS directives
    │   └── main.jsx        # React entrypoint
    ├── .env.example        # Environment setup template for the client
    ├── package.json        # Client configuration and dependencies manifest
    └── vite.config.js      # Vite compilation settings
```

---

## 🚀 Setup & Installation Guide

Get CodeSync up and running locally with these steps:

### 1. Prerequisites
Ensure you have the following installed on your machine:
*   [Node.js](https://nodejs.org/) (v16.x or higher)
*   [MongoDB](https://www.mongodb.com/) (Atlas URL or a running local instance)
*   [NPM](https://www.npmjs.com/) (usually bundled with Node.js)

### 2. Clone the Repository
```bash
git clone https://github.com/Karankumar2403/CodeSync.git
cd CodeSync
```

### 3. Backend Configuration
1. Navigate to the backend folder:
    ```bash
    cd backend
    ```
2. Install npm dependencies:
    ```bash
    npm install
    ```
3. Initialize the environment configuration:
    ```bash
    cp .env.example .env
    ```
4. Define your MongoDB Atlas URI, Port, and a secure signature key inside `.env`:
    ```env
    PORT=5000
    MONGO_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/codesync?retryWrites=true&w=majority
    JWT_SECRET=your_super_secure_jwt_secret_key_here
    CLIENT_URL=http://localhost:5173
    ```
5. Launch the backend server in development mode:
    ```bash
    npm run dev
    ```

### 4. Frontend Configuration
1. Open a new terminal session and navigate to the frontend directory:
    ```bash
    cd ../frontend
    ```
2. Install dependencies:
    ```bash
    npm install
    ```
3. Copy the environment configuration:
    ```bash
    cp .env.example .env
    ```
4. Link the API URL to the backend port inside `.env`:
    ```env
    VITE_BACKEND_URL=http://localhost:5000
    ```
5. Launch the client:
    ```bash
    npm run dev
    ```
6. Visit `http://localhost:5173` in your browser to begin pair-programming!

---

## 🔌 API & Event Documentation

### 🌐 REST API Router

| Route | HTTP Verb | Security | Purpose |
| :--- | :--- | :--- | :--- |
| `/api/auth/register` | POST | None | Sign up a new user account |
| `/api/auth/login` | POST | None | Authenticate credentials and set session cookie |
| `/api/auth/logout` | POST | Required | Clear credentials cookie and kill active session |
| `/api/auth/me` | GET | Required | Retrieve current user profile details |
| `/api/rooms/create` | POST | Guest/User | Initialize a new active pairing room session |
| `/api/rooms/validate/:roomId` | GET | Guest/User | Checks if a given Room ID is active and joinable |
| `/api/snippets` | GET | Required | Fetch saved snippets history for the user |
| `/api/snippets/save` | POST | Required | Commit current room editor code state to snippets database |

### 💬 Socket.IO Event Schema

CodeSync uses bidirectional event exchanges to sync rooms. Below are the key payload specifications:

#### Outbound Client Events (`Client -> Server`)
*   `join-room`: Sent when a client enters an active collaborative session room.
    ```json
    {
      "roomId": "room-uuid-string",
      "username": "karan_dev",
      "userId": "mongodb-user-id-or-guest-id"
    }
    ```
*   `code-change`: Dispatched on every keystroke in the Monaco editor.
    ```json
    {
      "roomId": "room-uuid-string",
      "code": "const compile = () => console.log('Syncing...');"
    }
    ```
*   `language-change`: Fired when a room manager updates the project programming language.
    ```json
    {
      "roomId": "room-uuid-string",
      "language": "python"
    }
    ```
*   `cursor-move`: Syncs cursor coordinates for in-room tracking.
    ```json
    {
      "roomId": "room-uuid-string",
      "position": { "lineNumber": 12, "column": 5 }
    }
    ```

#### Inbound Server Events (`Server -> Client`)
*   `user-joined`: Broadcast to active room members when a new collaborator joins.
    ```json
    {
      "socketId": "x7SdhF...9",
      "username": "karan_dev",
      "users": ["karan_dev", "alice_pair", "john_coder"]
    }
    ```
*   `code-update`: Updates the code canvas of all users in the matching room.
    ```json
    "const compile = () => console.log('Syncing...');"
    ```
*   `language-update`: Synchronizes syntax highlights on the Monaco shell.
    ```json
    "python"
    ```
*   `user-left`: Dispatched when a participant disconnects or closes their tab.
    ```json
    {
      "username": "john_coder",
      "users": ["karan_dev", "alice_pair"]
    }
    ```

---

## 📄 License
This project is released under the terms of the MIT License. Refer to [LICENSE](LICENSE) for further details.
