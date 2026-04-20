# ⚔️ CodeWars Live — Backend

Real-time competitive coding platform. Built by Omar Ahmed.

---

## Stack

| Layer      | Tech                          |
|------------|-------------------------------|
| Server     | Node.js + Express             |
| Real-time  | Socket.io (WebSockets)        |
| Database   | MongoDB + Mongoose            |
| Matchmaking| In-memory queue engine        |
| Deploy     | Railway / Render / Fly.io     |

---

## Setup (Local)

```bash
# 1 — Install dependencies
cd codewars-backend
npm install

# 2 — Create .env
cp .env.example .env
# Edit MONGO_URI with your MongoDB Atlas connection string

# 3 — Run
npm run dev       # development (nodemon)
npm start         # production
```

Server starts at **http://localhost:3001**

---

## Socket.io Events

### Client → Server

| Event          | Payload                              | Description                  |
|----------------|--------------------------------------|------------------------------|
| `player:join`  | `{ username, points }`               | Register player on connect   |
| `queue:join`   | `{ difficulty, language }`           | Enter matchmaking queue      |
| `queue:leave`  | —                                    | Leave queue                  |
| `code:update`  | `{ code, progress }`                 | Relay live code to opponent  |
| `code:submit`  | `{ code }`                           | Submit final solution        |
| `chat:message` | `{ text }`                           | In-battle chat               |

### Server → Client

| Event               | Payload                                    | Description                     |
|---------------------|--------------------------------------------|---------------------------------|
| `player:joined`     | `{ username, points, leaderboard, stats }` | Confirmation + initial data     |
| `queue:joined`      | `{ position, queueSize, ... }`             | Queue position                  |
| `queue:left`        | —                                          | Removed from queue              |
| `battle:start`      | `{ battleId, problem, opponent, ... }`     | Match found — battle begins     |
| `battle:tick`       | `{ remaining }`                            | Server-authoritative timer      |
| `opponent:update`   | `{ code, progress }`                       | Live opponent code relay        |
| `submit:result`     | `{ passed, total, errors }`                | Test results                    |
| `submit:failed`     | `{ passed, total, errors }`                | Partial pass                    |
| `battle:end`        | `{ winner, reason, duration, points }`     | Battle finished                 |
| `leaderboard:update`| `[{ rank, username, points }]`             | Fresh leaderboard               |
| `stats:update`      | `{ onlineCount, activeRooms }`             | Live platform stats             |
| `chat:message`      | `{ from, text, ts }`                       | Incoming chat message           |

---

## REST API

| Method | Endpoint                   | Description           |
|--------|----------------------------|-----------------------|
| GET    | `/health`                  | Server health check   |
| GET    | `/api/leaderboard`         | Top 20 players        |
| GET    | `/api/stats`               | Platform stats        |
| GET    | `/api/problems/:difficulty`| Problems list         |
| GET    | `/api/battles/active`      | Live battles          |

---

## Matchmaking Logic

```
player emits queue:join(difficulty, language)
  → added to queue[difficulty][language]
  → if queue.length >= 2:
      → dequeue 2 players
      → assign random problem from PROBLEMS[difficulty]
      → emit battle:start to both with opponent info
      → start server-side countdown timer
      → create Battle record in MongoDB
```

**Forfeit rule:** if a player disconnects mid-battle, opponent wins automatically.

---

## Deploy to Railway (Free)

```bash
# 1 — Push to GitHub
git init && git add . && git commit -m "CodeWars Live backend"
git remote add origin https://github.com/omarahameds2010-lab/codewars-live
git push -u origin main

# 2 — Go to railway.app → New Project → Deploy from GitHub
# 3 — Add environment variable: MONGO_URI=<your atlas URI>
# 4 — Done. Railway gives you a live URL like:
#     https://codewars-live-production.up.railway.app
```

Then in **client-connector.html** change:
```js
const SERVER_URL = 'https://codewars-live-production.up.railway.app';
```

---

## Built by Omar Ahmed
- GitHub: [omarahameds2010-lab](https://github.com/omarahameds2010-lab)
- LinkedIn: [omar-ahmed](https://linkedin.com/in/omar-ahmed-7b97613b1)
