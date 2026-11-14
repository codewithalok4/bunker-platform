# Bunker

Bunker is a community discussion app built with the MERN stack: MongoDB, Express, React, and Node.js. Members can discover communities, share posts, join discussions, vote, and save posts.

## Features

- Register, log in, and manage a profile
- Explore communities, join or leave them, and create new ones
- Publish text, link, and image posts
- Comment and reply in nested threads
- Upvote and downvote posts and comments
- Save posts and find them in your profile
- Sort feeds by Hot, New, and Top
- Search for posts and communities
- Community moderator tools for editing details, pinning posts, and removing content

## Tech stack

- **Frontend:** React, Vite, React Router
- **Backend:** Node.js, Express
- **Database:** MongoDB with Mongoose
- **Authentication:** JSON Web Tokens and bcrypt password hashing

## Project structure

```text
bunker-platform/
├── client/                 # React app
├── server/                 # Express API and MongoDB models
│   ├── docs/                # Postman API collection
│   └── src/
├── .env.example
├── package.json
└── README.md
```

## Run locally

### Requirements

Install Node.js 20 or newer and have a MongoDB database available, either locally or through MongoDB Atlas.

### 1. Install packages

From the project folder, run:

```bash
npm install
```

### 2. Create environment files

Copy the examples:

```bash
cp server/.env.example server/.env
cp client/.env.example client/.env
```

In Windows PowerShell, use:

```powershell
Copy-Item server/.env.example server/.env
Copy-Item client/.env.example client/.env
```

Edit `server/.env`:

- Set `MONGODB_URI` to your MongoDB connection string.
- Replace `JWT_SECRET` with a long, private value.
- For local development, `PORT` and `CLIENT_URL` can keep their example values.

For local development, `client/.env` can keep its example `VITE_API_URL`.

**Do not commit `.env` files or real secrets.** The `.env.example` files are safe templates.

### 3. Add sample data

With MongoDB running and `server/.env` configured, run:

```bash
npm run seed
```

This resets the Bunker collections in the configured database and adds sample users, communities, posts, comments, and votes. Use it only when you are okay replacing existing Bunker data.

Demo sign-in:

| Username | Email | Password |
| --- | --- | --- |
| `maya_dev` | `maya@example.com` | `bunker-demo-123` |
| `rowan_reads` | `rowan@example.com` | `bunker-demo-123` |
| `sam_codes` | `sam@example.com` | `bunker-demo-123` |

### 4. Start the app

Run both the API and frontend:

```bash
npm run dev
```

Open the frontend at **http://localhost:5173**. The API runs at **http://localhost:5000/api**.

To start only one part:

```bash
npm run dev:server
npm run dev:client
```

To create a production frontend build:

```bash
npm run build
```

## API documentation

Import [`server/docs/Bunker API.postman_collection.json`](server/docs/Bunker%20API.postman_collection.json) into Postman to explore the API. Set the collection’s `baseUrl`, log in, then use the returned JWT as the `token` variable for protected requests.

## Deployment

This repository does not include live deployment links. Configure the frontend, API, and database with your own hosting accounts before adding deployment URLs here.
