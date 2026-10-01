# PROMPT.MD — SYSTEM SPECIFICATION & OPERATIONAL GUIDELINES

## Core Directive
Build, maintain, and operate the **APEX Dragon Tiger P2P Casino Platform** under strict zero-bot, zero-bonus, maximum mobile optimization, global build number visibility, and complete transparency principles.

---

## 1. Operating Rules & Constraints

### 1. NO BOTS ALLOWED (`no bot allow`)
- **Zero Bot Policy**: Absolutely zero automated bots, simulated opponents, virtual high-frequency bot bettors, or AI auto-acceptors are permitted anywhere in the codebase.
- **100% Real Player Matchmaking**: All wagering and room challenges in `OneOnOneArena.tsx` and `P2PLobby.tsx` must take place between authentic, authenticated player accounts.
- **Server Room Queuing**: When a player starts matchmaking on `/api/rooms/create`, the room enters an authentic waiting queue awaiting a real peer; all mock bot auto-accept mocks (`ai_bot_opponent`, `TigerLord_Bot`, `DragonMaster_AI`) are strictly prohibited and eliminated.
- **Anti-Bot Defense**: IP-based frequency limiters and anti-bot spam tracking (`recentRoomAttempts`) detect and throttle automated scripts and malicious bot flooding.

### 2. NO BONUSES ALLOWED (`no bonus allow`)
- **Zero Synthetic Balance**: Zero deposit match bonuses, welcome signup bonuses, promo code balances, or artificial multiplier inflations.
- **1:1 Verifiable Accounting**: Real balance strictly reflects 1:1 verified player deposits and peer-to-peer winnings less the fixed 5% platform settlement rake.
- **Strict Demo Separation**: Demo credits are segregated from real funds with zero bleed-through.
- **Earned Gameplay Cosmetics**: Frames, card skins, ELO titles, and badges are 100% free achievement unlocks earned purely through verified gameplay hands, never sold via bonus mechanics.

### 3. BUILD NUMBER VISIBILITY EVERYWHERE
- The active build identifier (`BUILD_NUMBER`) sourced from `/src/config/version.ts` must be visibly displayed across all footer surfaces:
  - **Login Page Footer** (`LoginScreen.tsx`): Prominent bottom status bar with pulsating status indicator and build badge.
  - **Logged-In Pages Footer** (`RegulatoryFooter.tsx`): Official footer rendered across all views (Arena, 1v1 Duels, Elite Leaderboard).
  - **Mobile Bottom Navigation Bar** (`MobileBottomNav.tsx`): Micro-badge anchored above mobile tabs for continuous mobile viewport presence.
  - **Live Table HUD Console** (`GameTable.tsx`): Integrated telemetry line showing date, time, and active build.
  - **1v1 Arena Footer** (`OneOnOneArena.tsx`): Dedicated bottom status footer.
  - **P2P Multiplayer Lobby Footer** (`P2PLobby.tsx`): Global lobby footer.
  - **Leaderboard Footer** (`Leaderboard.tsx`): Elite rankings footer.
  - **Side Navigation Drawer** (`SideNavDrawer.tsx`): Dedicated "SYSTEM VERSION" badge.
  - **Admin Login & Admin Dashboard** (`AdminLogin.tsx` & `AdminDashboard.tsx`): Administrative console build stamps.

### 4. PWA INSTALLATION & OPEN MECHANICS ("Open" & "Install App")
- **Root Cause of "Open doesn't launch app"**:
  - Web browser security standards strictly prohibit web pages from force-launching installed external PWA applications via standard Javascript (`window.location.href`). Doing `window.location.href = '/'` simply reloads the existing web page inside the browser tab.
  - Furthermore, storing persistent flags in `localStorage` previously led the UI to falsely label the button as "Open" inside normal browser tabs where direct programmatic opening is impossible.
- **Enterprise Solution**:
  1. **Accurate State Detection**: `isStandalone` detects if the user is currently inside the standalone installed window (`(display-mode: standalone)`). In standalone mode, the app is already open!
  2. **1-Tap Direct Browser Install**: When the browser has `deferredPrompt` available, clicking "Install App" triggers `deferredPrompt.prompt()`.
  3. **Interactive Launch & Install Guide (`PWAInstallModal`)**: When `deferredPrompt` is not yet available, or on iOS Safari, or if the user taps to open/install:
     - Automatically detects the user's OS (Android, iOS Safari, Desktop PC).
     - Provides crystal-clear step-by-step instructions in Bengali and English.
     - **For Android Chrome**: (Tap ⋮ menu ➔ Tap "Install app" / "Add to Home Screen" ➔ Launch from Home Screen).
     - **For iPhone Safari**: (Tap Share 📤 ➔ Tap "Add to Home Screen" ➕ ➔ Launch from Home Screen).
     - **How to Open**: Explicitly instructs users who already installed the app to tap the **APEX Casino** gold icon directly from their phone's **Home Screen** or **App Drawer** for 100% full-screen native performance.
     - Provides a 1-tap "Copy App Link" button for users browsing inside restricted social webviews (Telegram, Facebook, WhatsApp).
  4. **Active Service Worker**: Service worker is registered immediately on mount (`registerSW({ immediate: true })` from `virtual:pwa-register`) to satisfy all PWA install criteria.

### 5. DEDICATED CASHIER (ZERO-SCROLL WALLET)
- Dedicated endpoints: `POST /api/wallet/deposit` and `POST /api/wallet/withdraw` alongside `POST /api/wallet/transfer`.
- Multi-rail cashier supporting bKash, Nagad, Rocket, UPI, Cards, and USDT TRC20/ERC20.
- Zero-scroll cashier modal layout with quick chip selectors (`+500`, `+1,000`, `+2,500`, `+5,000`), tactile audio feedback (`sound.playCoinsClinking()`), and real-time ledger auditing.

### 6. FULL-SITE MOBILE OPTIMIZATION (`100dvh` ZERO SCROLL)
- Dynamic Viewport: Container roots enforce `h-[100dvh]` to eliminate mobile URL bar bouncing and bottom bar clipping.
- Dynamic Element Scaling: `ResizeObserver` card auto-scaling bounded in `[0.55, 1.15]` ensures cards never overlap on small screens.
- Compact Touch Targets: Header controls and Iconic21 toolbar enforce minimum 28–44px touch ergonomics.
- Gesture Navigation: Touch-drag horizontal swiping between tabs with `@use-gesture/react` and Framer Motion spring physics.

---

## 2. Technical Stack & Architecture
- **Frontend**: React 19, TypeScript, Tailwind CSS, Lucide Icons, Framer Motion, `@use-gesture/react`, `vite-plugin-pwa`.
- **Backend**: Node.js, Express, `ws` (WebSockets), Cryptographic HMAC-SHA512.
- **RNG**: Cryptographic Provably Fair algorithm with 2^32 modulo bias rejection.
- **Port**: 3000.
