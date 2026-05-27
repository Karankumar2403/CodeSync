# ⚡ CodeSync

A lightweight, high-performance real-time collaborative code editor designed to enable seamless, low-latency code sharing and synchronous pair programming directly in the browser. CodeSync leverages React, Express, Socket.IO, and CodeMirror to synchronize text edits and track participant presence in collaborative rooms.

---

## 🏗️ Architectural Design

CodeSync uses a client-server architecture powered by WebSockets to achieve near-instantaneous code synchronization without the overhead of database transactions. All session metadata and active client states are managed in-memory on the server.

```mermaid
graph TB
    subgraph Client-Side (React App)
        CRA[React App - Create React App]
        CodeMirror[CodeMirror 5 Editor]
        SocketClient[Socket.IO Client]
        Roster[Client Presence List]
        
        CRA --> CodeMirror
        CRA --> SocketClient
        CRA --> Roster
    end

    subgraph Backend Services (Node.js & Express)
        Server[Express App Server]
        SocketServer[Socket.IO Server Engine]
        Registry[In-Memory User Roster]
        
        SocketClient <-->|WebSocket Protocol| SocketServer
        CRA <-->|Static Build Asset Requests| Server
        SocketServer --> Registry
    end

    classDef client fill:#1e293b,stroke:#475569,stroke-width:2px,color:#fff;
    classDef server fill:#0f172a,stroke:#334155,stroke-width:2px,color:#fff;
    
    class CRA,CodeMirror,SocketClient,Roster client;
    class Server,SocketServer,Registry server;
```

### 💻 Client-Side Architecture
*   **React Front-End**: Bootstrapped using Create React App (CRA) to handle layout, view state, and routing.
*   **CodeMirror Editor**: Integrates CodeMirror 5 (with Dracula theme, JavaScript styling, auto-close tags, and auto-close brackets) to provide a rich text editing interface.
*   **Socket.IO Client**: Establishes a WebSocket connection to dispatch editor state changes (`code-change`) and capture updates (`code-update`) from other collaborators.
*   **Roster Component**: Tracks the names of active users currently sharing the workspace using virtual avatar tags.

### ⚙️ Server-Side Architecture
*   **Express Web Server**: Handles static assets and serves the pre-compiled production build files (`build/` folder) for client loading.
*   **Socket.IO Server Engine**: Manages connection events, maintains an in-memory client roster mapping socket IDs to usernames, coordinates room joins/leaves, and broadcasts code changes.
*   **In-Memory State Manager**: Maps socket mappings on the fly: `const userSocketMap = {}`. No external database is needed, ensuring high speed and zero persistent overhead.

---

## ✨ Features

*   **Instant Collaborative Editing**: Code changes are synchronized across all connected client browsers with near-zero latency.
*   **Live Roster Presence**: Displays real-time lists of connected pairing partners using customized client initials and avatars.
*   **CodeMirror Integration**: Dracula-themed workspace equipped with syntax highlighting, automatic closing of brackets, and tag closing.
*   **Automated Room Generation**: Instantly create a clean, private pairing session with a unique UUID-based Room ID.
*   **Real-time Notifications**: In-room system notifications (powered by `react-hot-toast`) inform users when new partners join or leave the room.

---

## 🛠️ Technology Stack

*   **Frontend**: React.js, CodeMirror 5 (`codemirror`), Socket.io-client, React Router Dom, React Hot Toast, React Avatar.
*   **Backend**: Node.js, Express.js, Socket.IO, Nodemon.
*   **Roster Generation**: UUID v4.

---

## 📁 Repository Directory Structure

```text
codesync/
├── public/                 # Static public assets (Favicon, logos, images)
│   ├── code-sync.png       # CodeSync homepage and brand logo
│   └── index.html          # Shell template page
├── src/                    # Frontend source codebase
│   ├── components/
│   │   ├── Client.js       # Roster presence user card component
│   │   └── Editor.js       # CodeMirror 5 integration and event listener
│   ├── pages/
│   │   ├── EditorPage.js   # Sidebar dashboard layout and sockets connection hooks
│   │   └── Home.js         # Room login, joining and new session creator Page
│   ├── Actions.js          # Shared socket event constant declarations
│   ├── App.js              # Application router configuration
│   ├── App.css             # Main visual elements and style directives
│   ├── index.css           # Typography configurations
│   ├── index.js            # React app mount engine
│   └── socket.js           # Client-side Socket.IO engine instantiator
├── .gitignore              # Dependency exclusion rules
├── package.json            # NPM dependencies, metadata, and runtime scripts
├── package-lock.json       # Exact module dependency lockfile
└── server.js               # Node.js/Express and Socket.IO server startup file
```

---

## 🚀 Setup & Installation Guide

Run CodeSync locally on your machine with these simple steps:

### 1. Prerequisites
Ensure you have [Node.js](https://nodejs.org/) (v16.x or higher) and [NPM](https://www.npmjs.com/) installed.

### 2. Clone the Repository
```bash
git clone https://github.com/Karankumar2403/CodeSync.git
cd CodeSync
```

### 3. Environment Configuration
Create a `.env` file in the root of the project to declare your environment variables:
```env
REACT_APP_BACKEND_URL=http://localhost:5000
```

### 4. Install Dependencies
Install all required Node modules for both client-side and server-side components:
```bash
npm install
```

### 5. Running the Application

#### Development Mode
1. Run the Express backend server:
    ```bash
    npm run server:dev
    ```
2. Open a separate terminal window and run the React frontend:
    ```bash
    npm run start:front
    ```
3. Open `http://localhost:3000` to start pair-programming.

#### Production Build & Run
To run the server and client as a single unified web service:
```bash
npm run start
```
This builds your React project and boots up the production Express server on port `5000`. Navigate to `http://localhost:5000` to run the app.

---

## 💬 Sockets Communication Protocol

The synchronization between the editor workspace and the server relies on key Socket.IO message channels defined in `src/Actions.js`:

| Event Constant | Direction | Payload Example | Action Description |
| :--- | :--- | :--- | :--- |
| `join` | `Client -> Server` | `{ "roomId": "xyz-123", "username": "karan" }` | Sent by client to enter a collaborative room |
| `joined` | `Server -> Client` | `{ "clients": [...], "username": "karan", "socketId": "..." }` | Broadcast to all room users when a client joins |
| `code-change` | `Client -> Server` | `{ "roomId": "xyz-123", "code": "const a = 1;" }` | Dispatched on every text keystroke change |
| `code-change` | `Server -> Client` | `{ "code": "const a = 1;" }` | Broadcast to room members to update their editors |
| `sync-code` | `Client -> Server` | `{ "socketId": "...", "code": "const a = 1;" }` | Sent to initialize code state for a newly joined member |
| `disconnected`| `Server -> Client` | `{ "socketId": "...", "username": "karan" }` | Broadcast to room members when a collaborator leaves |

---

## 📄 License
This project is released under the terms of the MIT License. See [LICENSE](LICENSE) for details.
