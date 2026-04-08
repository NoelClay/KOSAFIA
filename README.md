# KOSAFIA — Real-Time Mafia Game

**Team project** · Spring Boot + React + WebSocket · 2024

A browser-based multiplayer mafia game with real-time role assignment, turn-based voting, and role-specific chat channels.

---

## My Contributions

**146 of 328 commits (44%) — most in the team**

I owned the real-time communication layer end-to-end: STOMP WebSocket server on Spring Boot, and the matching React context/hook client.

### Backend — Spring Boot

| Area | What I built |
|------|-------------|
| **STOMP config** | WebSocket endpoint `/wstomp`, message broker (`/topic`, `/queue`), SockJS fallback |
| **Room socket controller** | Player join/leave events, player list broadcast, room chat over `/topic/room.*` |
| **Game socket controller** | Game chat routing — normal vs mafia-only channel split by `gameStatus: NIGHT` |
| **Vote system** | Real-time vote collection and broadcast; execution vote vs night-kill vote |
| **Game state machine** | `GameStatus` transitions (WAIT → DAY → NIGHT → END), role assignment, police night-action channel |
| **Role-based chat isolation** | Mafia channel `/topic/game.chat.mafia.{roomKey}` visible to MAFIA role only at night |

Key files:
- `controllers/socket/game/GameSocketController.java` — game chat + role filtering
- `controllers/socket/room/RoomSocketController.java` — room lifecycle events
- `config/socket/WebSocketStompConfig.java` — STOMP broker setup
- `models/gameroom/` — Room, Player, GameStatus, Role domain model

### Frontend — React

| Area | What I built |
|------|-------------|
| **Socket context architecture** | `BaseSocketContext` → `MafiaSocketContext` / `RoomContext` / `GameSocketContext` provider hierarchy |
| **Custom hooks** | `useRoom`, `useUserList`, `useLobbyChat` — subscribe/unsubscribe, message dispatch |
| **Chat components** | `BaseChat`, `LobbyChat`, `ChatBox` — reused across lobby and game room |
| **User list** | `UserList` component + `useBaseList` hook, live-updated via socket |
| **Timer** | `Timer` component + `TimeControlUtils`, `TimerUtils` — synchronized countdown |
| **Game room page** | `GameRoomKNY.js`, `TestPlayRoom` — wired socket context to UI |

Key files:
- `contexts/kny/MafiaSocketContext.js` — top-level socket provider
- `contexts/socket/BaseSocketContext.js` — STOMP client lifecycle management
- `hooks/socket/room/useRoom.js`, `hooks/socket/list/useUserList.js`
- `components/socket/game/GameSocketComponent.js`

### Integration Role

I also served as the **primary PR integrator** for the team — reviewed, resolved conflicts on, and merged the majority of feature branches into `merging`.

---

## Architecture

```
Browser (React)
  └─ STOMP over SockJS  ──────────────────────────────────┐
                                                            ▼
Spring Boot (WebSocket Broker)
  ├─ /wstomp (SockJS endpoint)
  ├─ /fromapp/** (client → server)
  └─ /topic/**  (server → subscribed clients)
       ├─ /topic/room.players.{roomKey}   ← player list
       ├─ /topic/room.chat.{roomKey}      ← room chat
       ├─ /topic/game.chat.{roomKey}      ← public game chat
       └─ /topic/game.chat.mafia.{roomKey}← mafia-only (night)

Redis (session cache)  ←─ RoomRepository / GameStateService
MySQL  ←─ User, Room schema
Docker Compose  ←─ Redis + MySQL containers
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Java 17, Spring Boot 3, Spring WebSocket (STOMP) |
| Frontend | React 18, SockJS-client, @stomp/stompjs |
| Realtime | STOMP over WebSocket with SockJS fallback |
| Cache | Redis (game room state) |
| DB | MySQL (user/room persistence) |
| Infra | Docker Compose |

---

## Team

4-person project. Members owned separate domains (user/auth, DB schema, UI design, realtime).  
My domain: **realtime communication** (WebSocket backend + React client).

Original organization repo: [KOSAFIA/KOSAFIA](https://github.com/KOSAFIA/KOSAFIA)
