# Bunker

Bunker is a full stack community discussion app built with the MERN stack: MongoDB, Express, React, and Node.js. Members can discover and join communities, publish text/link/image posts, comment in threads, vote, save posts, and manage a profile. Community moderators can update community details, pin posts, and remove posts or comments.

This repository contains both sides of the product:

- `client/` — React UI built with Vite and React Router.
- `server/` — Express API, Mongoose models, JWT authentication, and database seed script.
- `server/docs/Bunker API.postman_collection.json` — importable API request collection.

## Requirements

- Node.js 20 or newer and npm.
- A MongoDB server. You can use a local MongoDB Community Server or a MongoDB Atlas database.

## Set up and run locally

Open a terminal in the repository root, then run:

```bash
npm install
```

This installs the root helper and both workspace packages. Copy the environment examples and set your own secret and database URL:

```bash
cp server/.env.example server/.env
cp client/.env.example client/.env
```

On Windows PowerShell, use `Copy-Item server/.env.example server/.env` and `Copy-Item client/.env.example client/.env` instead. Edit `server/.env` and replace `JWT_SECRET` with a long random value. Set `MONGODB_URI` to your local MongoDB URL or Atlas connection string. `client/.env` can stay as-is for local development.

Start both applications from the repository root:

```bash
npm run dev
```

The React app is at `http://localhost:5173`; the API is at `http://localhost:5000/api`. The API health endpoint is `http://localhost:5000/api/health`.

You can start the pieces separately with `npm run dev:server` and `npm run dev:client`. To create a production frontend bundle, run `npm run build`. To start the production API process, run `npm start` (with the server environment variables configured).

## Demo data

After MongoDB is running and `server/.env` is set, run:

```bash
npm run seed
```

The seed script creates 3 sample accounts, 5 communities, 15 posts, and 25 comments, including nested replies. It clears existing Bunker collections first, so use it only when you are happy to reset that database. The sample password is `bunker-demo-123` for each sample account:

| Username | Email |
| --- | --- |
| `maya_dev` | `maya@example.com` |
| `rowan_reads` | `rowan@example.com` |
| `sam_codes` | `sam@example.com` |

The sample data is generic and contains no real personal information.

## Environment variables

The committed `.env.example` files are templates. Copy them to untracked `.env` files; never commit real credentials.

| Variable | Used by | Purpose |
| --- | --- | --- |
| `PORT` | server | API port (defaults to `5000`). |
| `MONGODB_URI` | server | MongoDB connection string. |
| `JWT_SECRET` | server | Secret used to sign session tokens. Use a strong private value. |
| `CLIENT_URL` | server | Allowed frontend origin for CORS; comma-separated origins are accepted. |
| `VITE_API_URL` | client | API base URL, including `/api`. |

The root `.env.example` is a combined reference. The app reads `server/.env` and `client/.env` in development because the two applications run as separate packages.

## Features and behavior

- Public home and popular feeds, community discovery, community pages, post pages, and search.
- Register, log in, edit the current profile, and view user posts, comments, karma, and saved posts.
- Community creation, joining/leaving, unique community names, rules, member counts, and moderator editing.
- Text, link, and image posts; owner editing; owner or moderator removal; moderator pin/unpin.
- Nested comment threads with replies, author editing, removal markers, and community moderator removal.
- Server-owned post and comment vote records. A repeat vote toggles off; an opposite vote switches. Scores and user karma are recalculated by the API.
- Hot, New, and Top feed sorting, feed pagination, combined post/community search, and save/unsave.
- Mobile layouts, loading/error/empty states, inline form feedback, and visitor login gates.
- One API service module in `client/src/api.js` adds the bearer token to authenticated requests. The API returns readable JSON errors with HTTP status codes.

## API overview

Import `server/docs/Bunker API.postman_collection.json` into Postman. Set the collection's `baseUrl`; log in and copy the returned token into the collection's `token` variable. The collection includes requests for the routes below.

| Method and route | Access | Purpose |
| --- | --- | --- |
| `POST /api/auth/register` | Public | Create account and return token. |
| `POST /api/auth/login` | Public | Verify password and return token. |
| `GET /api/auth/me` | Logged in | Current profile. |
| `PUT /api/auth/me` | Logged in | Update bio/avatar URL. |
| `GET /api/communities?search=` | Public | List/search communities. |
| `GET /api/communities/:name` | Public | Community details and membership. |
| `POST /api/communities` | Logged in | Create community; creator becomes moderator and first member. |
| `PUT /api/communities/:name` | Moderator | Edit details and rules. |
| `POST /api/communities/:name/join` | Logged in | Join idempotently. |
| `POST /api/communities/:name/leave` | Logged in | Leave idempotently. |
| `GET /api/posts?sort=hot\|new\|top&community=&search=&page=` | Public | Paginated feed; logged-in default feed is limited to joined communities. `popular=1` requests the site-wide feed. |
| `GET /api/posts/:id` | Public | Post details. |
| `POST /api/posts` | Logged in | Create text/link/image post. |
| `PUT /api/posts/:id` | Owner | Edit own post. |
| `DELETE /api/posts/:id` | Owner, moderator, or admin | Soft-remove post. |
| `POST /api/posts/:id/pin` | Community moderator or admin | Toggle pin. |
| `GET /api/posts/:id/comments` | Public | Nested comment thread. |
| `POST /api/posts/:id/comments` | Logged in | Add comment or reply (`parentId`). |
| `PUT /api/comments/:id` | Owner | Edit own comment. |
| `DELETE /api/comments/:id` | Owner, community moderator, or admin | Remove comment while preserving replies. |
| `POST /api/posts/:id/vote` | Logged in | Vote with `{ "value": 1 }`, `-1`, or `0`. |
| `POST /api/comments/:id/vote` | Logged in | Vote with the same one-vote rule. |
| `POST /api/posts/:id/save` | Logged in | Toggle saved state. |
| `GET /api/users/:username` | Public | Profile and public posts/comments; saved posts only for the owner. |
| `GET /api/search?q=` | Public | Search posts and communities together. |

Successful responses contain their resource (`post`, `community`, `user`, etc.). Errors use `{ "message": "..." }`. Authentication uses `Authorization: Bearer <token>`.

## Deployment

The source is prepared for separate frontend and API hosting, with MongoDB Atlas as the database option. No hosting account or credentials were provided, so this repository does not claim a live deployment. Before submitting the Full Stack task, deploy the client and server, configure the environment variables on each host, then add the real app and API URLs here:

- App: **add your deployed frontend URL**
- API: **add your deployed API URL**

For a deployed client, set `VITE_API_URL` to the full deployed API base (for example, `https://your-api.example/api`) before building. Set the server's `MONGODB_URI`, `JWT_SECRET`, `CLIENT_URL`, and `PORT` in the hosting provider's environment settings. Recheck registration, community join/leave, post/comment/reply, voting, saving, profile tabs, and Hot/New/Top on both phone and desktop after deployment.

## Suggested learning path through the code

1. Read `server/src/models.js` to see how the five MongoDB collections relate to one another.
2. Follow registration and login in `server/src/index.js`, then see how `server/src/middleware.js` checks JWTs and moderator access.
3. Trace one feature from UI to API: `client/src/App.jsx` calls the single service in `client/src/api.js`.
4. Read `server/src/seed.js` to see a small repeatable dataset; change sample content and rerun the seed when you want a clean database.
5. Use the Postman collection to explore one endpoint at a time and inspect both successful responses and validation errors.

## Scope notes

Direct messages, live chat, video posts, awards, advertisements, and real-time notifications are outside the assignment scope. Community deletion and global admin management are not included because admin work is only required when assigned by the mentor.
