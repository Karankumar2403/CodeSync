# ⚡ CodeSync

<p align="center">
  <img src="https://img.shields.io/badge/CodeSync-Realtime%20Collaborative%20Editor-blueviolet?style=for-the-badge&logo=visual-studio-code" alt="CodeSync Badge" />
  <img src="https://img.shields.io/badge/MERN-Stack-green?style=for-the-badge&logo=mongodb" alt="MERN Stack Badge" />
  <img src="https://img.shields.io/badge/Socket.IO-v4.7-black?style=for-the-badge&logo=socketdotio" alt="Socket.io Badge" />
  <img src="https://img.shields.io/badge/License-MIT-blue?style=for-the-badge" alt="License Badge" />
</p>

CodeSync is a production-grade, real-time collaborative code editor designed to enable developers to code together in real-time, share syntax-highlighted sandboxes, and run code instantly. Modeled after professional IDE sharing solutions, CodeSync leverages the power of WebSockets, JWT session management, Monaco Editor core, and isolated compilation logic to deliver a seamless remote pair-programming experience.

---

## 🏗️ System Architecture

```mermaid
graph TB
    subgraph Client-Side (React App)
        Vite[Vite React App]
        Monaco[Monaco Editor Component]
        SocketClient[Socket.IO Client Engine]
        Context[React Context & State Manager]
        
        Vite --> Monaco
        Vite --> SocketClient
        Vite --> Context
    end

    subgraph Backend Services (Node.js & Express)
        Server[Express Server App]
        SocketServer[Socket.IO Server Engine]
        JWTAuth[JWT & Cryptography Service]
        
        SocketClient <-->|WebSocket Protocol| SocketServer
        Vite <-->|REST HTTP Requests| Server
        Server --> JWTAuth
    end

    subgraph Database & Persistence
        DB[(MongoDB Cloud Cluster)]
        Server <-->|Mongoose ODM| DB
    end

    classDef client fill:#5b21b6,stroke:#7c3aed,stroke-width:2px,color:#fff;
    classDef server fill:#1e3a8a,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef database fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#fff;
    
    class Vite,Monaco,SocketClient,Context client;
    class Server,SocketServer,JWTAuth server;
    class DB database;
```

---

## ✨ Features

*   **Real-time Collaborative Editing**: Multiple users can connect to the same room, editing code simultaneously with near-zero latency.
*   **Live User Presence & Cursor Tracking**: See who is currently in the workspace with interactive user avatars, activity badges, and distinct cursor highlights.
*   **VS Code (Monaco) Editor Integration**: Powered by Microsoft's Monaco Editor, supporting autocomplete, auto-closing brackets, syntax error reporting, minimap, and automatic formatting.
*   **Multi-Language Sandbox Support**: Swap code environments on-the-fly between Javascript, Python, C++, Java, and HTML/CSS with fully persistent room configurations.
*   **Robust JWT Authentication & Guest Access**: Secure registration and login using encrypted sessions, as well as a quick guest mode to join a shared room instantly.
*   **Session History & Dashboard**: Logged-in users can access a personal dashboard containing all created rooms, saved snippets, and shareable read-only links.
*   **Live In-Room Chat**: Integrated real-time messaging panel to communicate with collaborators directly beside your code canvas.
*   **Elegant Glassmorphism UI**: Built with dynamic HSL-based themes, responsive styling, dark mode support, and micro-interactive animations.

---

## 🛠️ Tech Stack

*   **Frontend**: React (Vite.js), Tailwind CSS, Monaco Editor (`@monaco-editor/react`), Socket.io-client, React Router Dom, React Hot Toast.
*   **Backend**: Node.js, Express.js, Socket.IO, JSON Web Tokens (JWT), Bcrypt.js, Mongoose.
*   **Database**: MongoDB (Atlas Cloud or Local Instance).

---

## 📁 Repository Structure

```text
codesync/
├── backend/
│   ├── config/             # DB and third-party API configurations
│   ├── controllers/        # Express request handling controllers
│   ├── middleware/         # JWT authorization and validation middlewares
│   ├── models/             # Mongoose database schemas
│   ├── routes/             # REST API endpoint definitions
│   ├── socket/             # Socket.IO event handler functions
│   ├── .env.example        # Reference environment configuration for backend
│   ├── package.json        # Backend NPM package manifest
│   └── server.js           # Server application entrypoint
└── frontend/
    ├── public/             # Static public assets
    ├── src/
    │   ├── assets/         # App icons, backgrounds, fonts
    │   ├── components/     # Reusable UI elements (Buttons, Inputs, Modals)
    │   ├── context/        # Session and theme React context providers
    │   ├── hooks/          # Custom hooks (Sockets handler, Auth helper)
    │   ├── pages/          # Layout pages (Home, Auth, Dashboard, EditorWorkspace)
    │   ├── App.jsx         # App router and layout wrapper
    │   ├── index.css       # Core styling & custom HSL scrollbars
    │   └── main.jsx        # Frontend application entrypoint
    ├── .env.example        # Reference environment configuration for frontend
    ├── package.json        # Frontend NPM package manifest
    └── vite.config.js      # Vite project bundler settings
```

---

## 🚀 Setup & Installation Guide

Follow these steps to set up and run CodeSync locally:

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

### 3. Backend Setup
1. Navigate to the backend directory:
    ```bash
    cd backend
    ```
2. Install the necessary dependencies:
    ```bash
    npm install
    ```
3. Create a `.env` file from the example:
    ```bash
    cp .env.example .env
    ```
4. Update the `.env` variables with your specific database connection and JWT credentials:
    ```env
    PORT=5000
    MONGO_URI=mongodb+srv://<username>:<password>@cluster0.example.mongodb.net/codesync?retryWrites=true&w=majority
    JWT_SECRET=your_super_secure_jwt_secret_key_here
    CLIENT_URL=http://localhost:5173
    ```
5. Start the backend dev server:
    ```bash
    npm run dev
    ```

### 4. Frontend Setup
1. Open a new terminal session and navigate to the frontend directory:
    ```bash
    cd ../frontend
    ```
2. Install the dependencies:
    ```bash
    npm install
    ```
3. Create a `.env` file from the example:
    ```bash
    cp .env.example .env
    ```
4. Define your backend URL:
    ```env
    VITE_BACKEND_URL=http://localhost:5000
    ```
5. Start the frontend application in development mode:
    ```bash
    npm run dev
    ```
6. Open `http://localhost:5173` in your browser to start pair-programming!

---

## 🔌 API & WebSocket Reference

### 🌐 REST API Endpoints

| Endpoint | Method | Authentication | Description |
| :--- | :--- | :--- | :--- |
| `/api/auth/register` | POST | None | Registers a new user account |
| `/api/auth/login` | POST | None | Authenticates user credentials and sets token cookie |
| `/api/auth/logout` | POST | Required | Clears user auth cookie / invalidates session token |
| `/api/auth/me` | GET | Required | Fetches the authenticated user profile |
| `/api/rooms/create` | POST | Guest/User | Creates a new active workspace session |
| `/api/rooms/validate/:roomId`| GET | Guest/User | Verifies the validity/existence of a Room ID |
| `/api/snippets` | GET | Required | Returns user's saved coding workspaces |
| `/api/snippets/save` | POST | Required | Saves the active room editor state to User history |

### 💬 WebSockets Communication Events

The live collaboration module works over real-time events. Below are the core event schemas:

#### Client-to-Server (`Client -> Server`)
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

#### Server-to-Client (`Server -> Client`)
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
This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
