# LifeFlow — Blood Bank Donor & Request Management

LifeFlow is a simple MERN project for registering donors, searching donors by blood group and city, and managing blood requests. It follows **PS9: Blood Bank Donor & Request Management System** from Assignment 6.

## Features

### Donor

- Register and log in with an email address and password.
- Search donors by blood group and city.
- Create a blood request with patient name, required blood group, units, hospital, city and contact number.
- View personal blood requests and their current status.
- Log out.

### Admin

- View all blood requests.
- Filter requests by blood group, city and status.
- Update a request to Pending, Accepted, Completed or Cancelled.
- View total, pending and completed counts for the currently loaded requests.

Profile viewing and editing are available through the backend API. The current frontend does not include a profile editing page.

## Technology

| Layer | Tools |
| --- | --- |
| Frontend | React, React Router, Axios, Vite, CSS |
| Backend | Node.js, Express |
| Database | MongoDB with Mongoose |
| Authentication | JSON Web Tokens and bcryptjs |

## Project structure

```text
Bloodbank-main/
├── backend/
│   ├── Config/db.js
│   ├── Controllers/
│   ├── Middleware/
│   ├── Models/
│   ├── Routes/
│   ├── package.json
│   └── server.js
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── App.jsx
│   │   ├── App.css
│   │   └── main.jsx
│   └── package.json
└── README.md
```

## Run locally

Use a current Node.js LTS release compatible with the dependencies, npm, and a MongoDB database. MongoDB Atlas can be used for the database.

### 1. Install dependencies

From the project root:

```bash
cd backend
npm ci
```

In another terminal, from the project root:

```bash
cd frontend
npm ci
```

### 2. Configure the backend

Create a `.env` file inside `backend/`:

```dotenv
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_long_random_secret
JWT_EXPIRES_IN=1d
PORT=5002
```

Replace the example values with your own configuration. Keep the real `.env` file private.

### 3. Select the API server

The current frontend uses different API addresses:

| File | Current API URL |
| --- | --- |
| `frontend/src/components/LoginRegister.jsx` | `https://bloodbank-4yqt.onrender.com/api/auth` |
| `frontend/src/pages/UserDashboard.jsx` | `https://bloodbank-4yqt.onrender.com/api` |
| `frontend/src/pages/AdminDashboard.jsx` | `http://localhost:5002/api/blood-requests` |

For a fully local run, set the first two `API_URL` constants to `http://localhost:5002/api/auth` and `http://localhost:5002/api`. Keep the admin URL as shown. For a hosted run, point all three constants at the same hosted backend, retaining their respective paths. The frontend currently has no environment variable for this setting.

These are setup instructions; this README does not change the source URLs. The hosted address comes from the source code, and its availability has not been verified.

### 4. Start the backend

From `backend/`:

```bash
npm start
```

The default backend address is `http://localhost:5002`. Opening `/` returns an API status message.

### 5. Start the frontend

From `frontend/`:

```bash
npm run dev
```

Open the local URL printed by Vite.

## Admin access

Registration creates a user with the `Donor` role. There is no admin registration page or admin seed script.

For your own development database, register an account and change that account's `role` field to `Admin` in MongoDB. Log out and log in again to receive a token with the updated role. Use the exact values `Admin` and `Donor`.

## Database models

- **User:** name, unique email, hashed password, phone, blood group, date of birth, city, role and timestamps.
- **BloodRequest:** requester reference to User, patient name, blood group, units, hospital name, city, contact number, status and timestamps.

One user can create multiple blood requests. Each request stores the creating user's ID.

## API routes

Protected routes require `Authorization: Bearer <token>`.

| Method | Route | Access | Purpose |
| --- | --- | --- | --- |
| GET | `/` | Public | API status |
| POST | `/api/auth/register` | Public | Register a donor |
| POST | `/api/auth/login` | Public | Log in and receive a token |
| GET | `/api/auth/me` | Signed-in user | Get current profile |
| PUT | `/api/auth/profile` | Signed-in user | Update allowed profile fields |
| GET | `/api/donors/search` | Public | Search donors by `bloodGroup` and `city` |
| POST | `/api/blood-requests` | Signed-in user | Create a blood request |
| GET | `/api/blood-requests/my` | Signed-in user | Get own blood requests |
| GET | `/api/blood-requests` | Admin | List requests, with optional `bloodGroup`, `city` and `status` filters |
| PATCH | `/api/blood-requests/:id/status` | Admin | Update request status |

Registration requires `name`, `email`, `password`, `phone`, `bloodGroup`, `dateOfBirth` and `city`. Login requires `email` and `password`.

Creating a request requires `patientName`, `bloodGroup`, `units`, `hospitalName`, `city` and `contactNumber`. A status update accepts a `status` field.

## Rules and status workflow

- Passwords must contain at least eight characters and are stored as bcrypt hashes.
- Email addresses are normalized to lowercase, and duplicate registration is rejected.
- Blood groups use the eight values A+, A-, B+, B-, AB+, AB-, O+ and O-.
- Requested units must be a positive whole number.
- New requests start as `Pending`.
- Only admins can list all requests and update their status.
- The backend accepts any of the four status values on an admin update. It does not enforce a fixed transition order.
- Donor search matches stored blood group and city. It does not calculate medical compatibility or donation eligibility.

## Scope kept simple

The project does not include maps or location tracking; live notifications, chat, SMS or email delivery; Google login; charts; file uploads; PDF export; AI features; Redux; Next.js; animation libraries; blood inventory; medical compatibility calculations; eligibility or donation cooldown checks; payments; or record deletion workflows.

The email field is used for login. City is a text field. Requested units describe a request, not available blood stock. Cancelling a request changes its status and does not delete it.

## Checks and build

From `frontend/`:

```bash
npm run lint
npm run build
npm run preview
```

The backend currently has no automated test suite. Its `npm test` script is the default placeholder and exits with an error.

## Deployment notes

Configure backend environment variables on the host and use the same database and API server for both roles. Build the frontend with `npm run build` and deploy `frontend/dist`. The frontend host must serve `index.html` for client routes such as `/user` and `/admin`.

## References

- [Assignment 6](https://groovy-harbor-371.notion.site/Assignment-6-MERN-Project-3f8aad0730f1810d8733d20dc9a6e35f)
- [React documentation](https://react.dev/learn)
- [Express documentation](https://expressjs.com/)
- [Mongoose documentation](https://mongoosejs.com/docs/guide.html)
- [MongoDB Atlas documentation](https://www.mongodb.com/docs/atlas/)
