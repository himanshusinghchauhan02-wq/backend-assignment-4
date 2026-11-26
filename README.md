# Lab 4 – Secure Connect and Communication Platform

Course: Web Dev III  
Unit: 4  
Marks: 2.5

## Requirements covered

- Node.js + Express backend
- Signup and login
- bcrypt password hashing
- JWT authentication
- Protected routes
- Role-based access control (RBAC)
- Secure Notes CRUD API
- dotenv environment variables
- Socket.IO real-time chat
- Node.js EventEmitter event-driven logging
- Postman collection for API testing
- JSON-file persistence using Node's built-in `fs` module

The assignment PDF allows MongoDB or another preferred persistence approach. This implementation uses JSON files so that no unlisted database package is required.

## 1. Installation

```bash
cd Secure-Connect-Communication-Platform
npm install
```

Copy `.env.example` to `.env` and set a strong JWT secret.

Windows PowerShell:
```powershell
Copy-Item .env.example .env
```

Then edit `.env`.

## 2. Start server

```bash
npm start
```

Expected:
```text
[server] listening on http://localhost:5000
```

## 3. API endpoints

| Method | Endpoint | Auth |
|---|---|---|
| GET | / | No |
| GET | /api/health | No |
| POST | /api/auth/signup | No |
| POST | /api/auth/login | No |
| GET | /api/protected/profile | JWT |
| GET | /api/admin/users | JWT + admin |
| GET | /api/notes | JWT |
| POST | /api/notes | JWT |
| GET | /api/notes/:id | JWT |
| PUT | /api/notes/:id | JWT |
| DELETE | /api/notes/:id | JWT |

## 4. Postman testing

Import:
`postman/Secure-Connect-Lab4.postman_collection.json`

Run in this order:

1. Health Check
2. API Root
3. Signup
4. Login
5. Profile - Protected
6. Get Notes - Protected
7. Create Note
8. Get Note By ID
9. Update Note
10. Delete Note
11. Notes Without Token - expected 401
12. Admin Users - expected 403 for a normal user

The Login test script automatically saves the JWT into `{{token}}`.
The Create Note test script automatically saves the note ID into `{{noteId}}`.

## 5. Real-time Socket.IO test

Install the Socket.IO client only if you want a separate client test. The server itself implements:

- `joinRoom`
- `chatMessage`
- `systemMessage`
- disconnect logging

Example client logic:

```js
const socket = io("http://localhost:5000");

socket.emit("joinRoom", "general");

socket.on("systemMessage", console.log);

socket.emit("chatMessage", {
  room: "general",
  username: "Sahid",
  message: "Hello from Socket.IO!"
});

socket.on("chatMessage", console.log);
```

## 6. Security notes

- Passwords are never stored as plain text.
- JWT is required for protected endpoints.
- Notes are filtered by authenticated owner.
- Admin endpoint checks the user's JWT role.
- JWT secret is read from `.env`.
- Request body size is limited to 50 KB.
- Login uses a generic invalid-credentials response.

## 7. Viva / explanation points

### Authentication
Signup hashes a password with bcrypt. Login verifies the hash and issues a JWT.

### Authorization
`authRequired` verifies the JWT. `roleRequired("admin")` implements RBAC.

### Notes
Each note contains an owner ID. CRUD operations only access notes owned by the authenticated user.

### EventEmitter
The application emits events for signup, login audit, note creation, note update/delete, Socket.IO room joins, chat messages, and disconnects.

### Socket.IO
Clients join a room and exchange `chatMessage` events in real time.

## 8. Expected status codes

- 200: successful GET/login/update
- 201: successful signup/create
- 400: validation error
- 401: missing/invalid/expired JWT or invalid login
- 403: valid token but insufficient role
- 404: resource not found
- 409: duplicate email
- 500: unexpected server error

<img width="1917" height="1026" alt="Screenshot 2026-11-25 230606" src="https://github.com/user-attachments/assets/8d43da28-cdf0-4552-9a7b-4f2dd331ab3a" />
<img width="1917" height="1032" alt="Screenshot 2026-11-25 230622" src="https://github.com/user-attachments/assets/b29d42bf-4244-45d7-9124-94732fe80074" />
<img width="1917" height="1027" alt="Screenshot 2026-11-25 230642" src="https://github.com/user-attachments/assets/40798b90-f842-4576-9b12-d6b974149cfa" />
<img width="1917" height="1028" alt="Screenshot 2026-11-25 230724" src="https://github.com/user-attachments/assets/70fbbb72-d33d-4946-99f6-c721e7227617" />


