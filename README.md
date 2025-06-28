# 💬 Real-Time Chat App

A full-stack real-time chat application built with:

- 🖥 **Frontend:** React + Zustand + TailwindCSS
- ⚙️ **Backend:** Node.js, Express, MongoDB
- 🔥 **Real-time:** Socket.IO
- 🖼 **Images:** Cloudinary uploads

---

## 🚀 Features

✅ User signup & login with JWT + cookies  
✅ Persistent auth with `/auth/check` route  
✅ Profile picture updates  
✅ Real-time messaging with Socket.IO  
✅ Photo sharing in chat (uploaded to Cloudinary)  
✅ Shows online users + optional toggle to filter online only  
✅ Responsive UI: Desktop & Mobile

---

## 🔥 Screenshots

📌 **Login / Signup**  
![image](https://github.com/user-attachments/assets/e6b33f76-800a-4987-b152-aa6d887aef46)


📌 **Chat with online sidebar & typing**  
![image](https://github.com/user-attachments/assets/dea84eb0-6223-4652-80d8-0528b6c8c50e)


📌 **Send photo**  
![image](https://github.com/user-attachments/assets/6cae573b-d4e0-41c0-aff7-b231c9dcf777)


---

## ⚙️ Installation & Running Locally

Clone the repository & install dependencies for both frontend and backend:

    ```bash
    git clone https://github.com/your-username/chat-app.git
    cd chat-app

###📦 Backend (Node / Express)

    cd backend
    npm install
    npm run dev
  Runs on: http://localhost:5001

Configure .env in the backend folder:

    MONGO_URI=your_mongodb_uri
    JWT_SECRET=your_jwt_secret
    CLOUDINARY_CLOUD_NAME=xxx
    CLOUDINARY_API_KEY=xxx
    CLOUDINARY_API_SECRET=xxx
    PORT=5001
    NODE_ENV=development

#### 🖥 Frontend (React + Vite)

    cd frontend
    npm install
    npm run dev
Runs on: http://localhost:5173

### 🔍 How the app works
🚀 Flow of the project
✅ Authentication
Auth is handled via JWT tokens stored in HTTP-only cookies.

backend/routes/auth.route.js exposes:

    POST /api/auth/signup

    POST /api/auth/login

    POST /api/auth/logout

    GET /api/auth/check → checks for valid cookie and returns user.

Frontend uses a global Zustand store useAuthStore which:

    Saves the authUser object on signup/login.

    Calls connectSocket() immediately after login/signup or on refresh (via checkAuth).


### 🔌 Real-time messaging (Socket.IO)
Each user connects to Socket.IO on login:

    const socket = io(BASE_URL, {
      query: { userId: authUser._id },
    });
    Server (lib/socket.js) maps userId -> socketId in userSocketMap.

When a message is sent, the backend checks if the receiver is online:

    const receiverSocketId = getReceiverSocketId(receiverId);
    if (receiverSocketId) {
      io.to(receiverSocketId).emit("newMessage", newMessage);
    }
    
Online users are broadcasted to all via:

    io.emit("getOnlineUsers", Object.keys(userSocketMap));

###💬 How messages are shared
Messages are stored in MongoDB via the Message model:
    
    {
      senderId,
      receiverId,
      text,
      image,  // optional URL if photo is sent
      createdAt
    }

On the frontend:

Zustand useChatStore manages selected user + messages.

When you click a user in the sidebar, it loads all previous messages via:

    axiosInstance.get(`/message/${selectedUser._id}`)
Also subscribes to "newMessage" event so chats are real-time.

####🖼 How send photo functionality works
  In the message input, we allow attaching a photo (converted to Base64 on client).

  Backend uses cloudinary.uploader.upload(image) to upload & get a URL.

  This URL is saved in the Message schema.

  Frontend renders it inside <img> tag under the chat bubble.

### 🎨 How we designed the UI
Built with:

TailwindCSS for quick styling + dark/light mode.

Lucide icons for consistent icons.

Responsive layouts using flex, grid, overflow-y-auto.

Sidebar toggle to show only online users.

Zustand stores keep global auth & chat state so all components react instantly.

Reusable components like <ChatHeader>, <MessageInput>, <Sidebar>.

Scroll-to-bottom with useRef + scrollIntoView for new messages.

### 🛠 Tech Stack
    Frontend: React, Zustand, TailwindCSS, React Router, React Hot Toast
    
    Backend: Node.js, Express, Mongoose, JWT, Cookie Parser
    
    Real-time: Socket.IO
    
    Uploads: Cloudinary
    
    Deployment Ready: Can run on separate servers or merge via Express static.

###📂 Folder Structure\.

        ├── backend
        │   ├── routes/
        │   ├── controllers/
        │   ├── lib/ (db.js, socket.js, cloudinary.js)
        │   ├── models/
        │   └── index.js
        ├── frontend
        │   ├── src/components/
        │   ├── src/store/ (zustand)
        │   ├── src/pages/
        │   └── main.jsx
        ├── package.json (root)
        ├── README.md
        └── .gitignore
