# APEX CASINO — CODING_LOGIC & DATA FLOW BLUEPRINT

## 1. High-Frequency Game Loop & WebSocket Flow
```text
Client Application                                Server (server.ts)
      |                                                   |
      | ----------> [WebSocket Connection] -------------> |
      |                                                   |
      | <---------- TIMER_TICK (Every 1s) <-------------- |
      |             (Seconds remaining, Pool volumes)     |
      |                                                   |
      | ----------> Place Bet (POST /api/game/bet) -----> |
      |             { userId, side, amount, type }        |
      |                                                   |
      | <---------- NEW_BET broadcast <------------------ |
      |                                                   |
      | <---------- ROUND_PHASE: MATCHING <-------------- |
      |             (Calculate matched vs unmatched)      |
      |                                                   |
      | <---------- ROUND_DEALING <---------------------- |
      |             (Dragon Card, Tiger Card, Result)     |
      |                                                   |
      | <---------- ROUND_SETTLED <---------------------- |
      |             (Winners paid, Unmatched refunded)    |
      |                                                   |
      | <---------- NEW_ROUND <-------------------------- |
```

---

## 2. Dedicated Cashier & Wallet Logic (`WalletModal.tsx` & `server.ts`)
```text
Client Modal (WalletModal.tsx)                     Server (server.ts)
      |                                                   |
      | --- (1) User Selects Method & Amount -----------> |
      |     (bKash / Nagad / Rocket / UPI / Card / USDT)  |
      |     Quick Chips: +500, +1,000, +2,500, +5,000     |
      |                                                   |
      | --- (2) POST /api/wallet/deposit ---------------> |
      |     { userId, username, amount, method }          |
      |                                                   |
      | <-- (3) 200 OK + Updated User Balance <---------- |
      |                                                   |
      | --- (4) Client Actions: ------------------------> |
      |     ├── sound.playCoinsClinking()                 |
      |     ├── onUpdateWallet(user)                      |
      |     ├── fetchGlobalLedger()                       |
      |     └── Set Zero-Scroll Success State             |
```

---

## 3. PWA Installation & Launch Logic
```text
User Clicks "Install App" / Mobile Nav
      │
      ├── Case A: App Running in Standalone Window (isStandalone = true)
      │     └── Install trigger is suppressed; app is already running natively.
      │
      └── Case B: User Browsing in Web Browser (isStandalone = false)
            │
            ├── 1. Check if browser fired `beforeinstallprompt` (hasPrompt = true)
            │      └── YES: Call `deferredPrompt.prompt()` directly!
            │               ├── User accepts: App installs, sets standalone mode!
            │               └── User dismisses or cancels: Proceed to Step 2.
            │
            └── 2. If prompt unavailable or dismissed (hasPrompt = false)
                   └── Open `PWAInstallModal.tsx`:
                       ├── Detect Device OS (Android, iOS Safari, Desktop PC)
                       ├── Render exact step-by-step visual instructions in BN/EN
                       ├── Provide "How to Open" guidance: Tap APEX Casino icon on phone's Home Screen
                       └── Provide "Copy App Link" for social in-app webviews (WhatsApp, Telegram)
```

---

## 4. Strict Real-Player P2P Matchmaking Logic (Zero Bots)
```text
User Initiates Matchmaking (OneOnOneArena.tsx)
      │
      ├── 1. Query Existing Rooms (GET /api/rooms)
      │      └── If open room from real peer exists → Accept and Start Duel
      │
      └── 2. If No Open Room Exists:
             ├── POST /api/rooms/create (Creates authentic server room)
             ├── Anti-Bot Spam Check: Rate limits creation frequency
             ├── Real Room Enters Waiting State (Status: "open")
             ├── Real Peer Browsing Lobby Joins via POST /api/rooms/join
             └── WebSocket Broadcasts MATCH_FOUND to both players!
```

---

## 5. Mobile Layout & Auto-Scaling Pipeline
```text
GameTable Mounts / Window Resizes
      │
      ├── ResizeObserver observes container entry (.contentRect)
      │
      ├── Calculate scale = min(width / 750, height / 580)
      │
      ├── Clamp scale strictly within [0.55, 1.15]
      │
      └── Apply transform: scale(clampedScale) to card felt container:
            ├── Prevents card overlap on narrow screens (< 380px)
            ├── Preserves crisp text without clipping
            └── Keeps chips and action buttons comfortably in thumb reach!
```
