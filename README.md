# Smart Attendance System using QR Code

A modern, full-stack Smart Attendance System designed with a beautiful Apple/Notion-inspired glassmorphism UI. Built completely from scratch using Vanilla JS, Express, and MongoDB.

## Features Included 🚀
* **JWT Authentication**: Secure role-based login (Admin/Student).
* **QR Generation & Scanning**: Dynamic generation of short-lived session QR codes, scanned instantly via WebRTC (HTML5 Camera).
* **Live Analytics & State**: Dashboard features top-level analytics (avg attendance, totals) and updates flawlessly in real-time using Socket.io.
* **Security**: Enforces one-time scans, blocks duplicate entries, and validates time expiration on all sessions.
* **Modern UI/UX**: Soft colors, glassmorphism blur effects, toast notifications, micro-animations, and live CSS-based loading spinners.

---

## 🛠️ Tech Stack
* **Frontend**: HTML5, Vanilla CSS3 (Variables + Flex/Grid), Vanilla JavaScript (no frameworks)
* **Backend**: Node.js, Express.js, Socket.io
* **Database**: MongoDB (Mongoose validation & queries)

---

## 💻 Step-by-Step Setup Guide

**1. Clone or Extract the Project**
Make sure you have Node.js installed on your computer. Extract this project into a directory (e.g. `SAS/`).

**2. Backend Setup**
Open your terminal and navigate to the backend folder:
```bash
cd server
npm install
```

**3. Database Configuration**
This project runs a "zero-config" MongoDB setup meaning it will automatically run an in-memory database if you don't configure one! However, to persist your data, ensure your `.env` file in the `server` folder looks like this:
```
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/smart_attendance
JWT_SECRET=supersecretkey_change_me_in_production
```

**4. Run the Backend Server**
```bash
npm run dev
```

**5. Run the Frontend**
Open a new terminal window, navigate to the client folder, and serve the HTML files. You can use standard `npx serve`:
```bash
cd client
npx serve .
```

Navigate to `http://localhost:3000` to view the application!

---

## 🗄️ MongoDB Schemas
The database architecture uses three interconnected models.

1. **User Schema**: Stores `name`, `email` (unique), `password` (hashed via bcrypt), and `role` (Admin or Student).
2. **Session Schema**: Created by Admins. Stores `title`, `durationMinutes`, an encrypted `qrToken`, and dynamically calculates the `expiresAt` Date. 
3. **Attendance Schema**: The bridge. Stores references to the `Session` `_id` and the `Student` `_id` along with a precise `timestamp`. A unique compound index guarantees students cannot mark attendance twice.

---

## 🌐 API Endpoints

### Auth (`/api/auth`)
* `POST /register` - Register a new account. Body: `{ name, email, password, role }`
* `POST /login` - Login to account. Body: `{ email, password }`

### Sessions (`/api/sessions`) *[Protected]*
* `POST /` - (Admin) Create a new QR session. Body: `{ title, durationMinutes }`
* `GET /` - (Admin) Fetch all sessions created by this admin.
* `PUT /:id/stop` - (Admin) Immediately force-expire an active session QR code.

### Attendance (`/api/attendance`) *[Protected]*
* `POST /mark` - (Student) Scan and authenticate attendance. Body: `{ qrToken }`
* `GET /analytics` - Fetches overarching mathematical statistics for both Admin and Student dashboards.
* `GET /history` - (Student) Loads the historical list of sessions the student has successfully attended.
* `GET /session/:id` - (Admin) Grabs all the students who successfully checked into a specific session ID.
