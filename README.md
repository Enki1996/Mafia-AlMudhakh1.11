# مافيا المذخر — Complete Online Game Replacement (V1)

This is a **new standalone Node.js + Express + Socket.IO replacement project**, assembled from the latest approved designs and supplied artwork. **It is NOT a verified patch against your original `Enki1996/mafia-nights` repository**, because the original source was not accessible in this session. Back up the old repository and test this build on a separate Railway service before replacing a live deployment.

## Included screens

- Home: latest Iraqi Arabic copy, responsive cinematic background MP4, official illustrated logo and characters, create/join forms, rules.
- Narrator waiting room: static warehouse backdrop, real room code/invite, live player list, Mafia count, optional Doctor/Detective, and conditional Silencer count.
- Player waiting room: static backdrop, official logo, hourglass, rules popup, real invitation and participants.
- Night player: envelope/role-card reveal, all five supplied double-headed cards, alternating 0°/180° orientation on successive reveals, own role description, teammate list (Mafia only), private action selector.
- Night narrator: live action-progress overview, player actions and target names, resolve-night command.
- Day: face-to-face accusations and voting; narrator records an elimination or no elimination. Silenced players get a warning, and detective privately gets investigation results.
- Game end/rematch: win condition and new game in the same room.

## Mechanics and privacy

- Room codes, session tokens, role assignments and game state are generated **server-side**.
- Player names are **actual connected lobby members**, not hardcoded accounts.
- **Mafia** has two variants: `mafia_killer` and `mafia_silencer`. Both are counted within total Mafia.
- Each living killing-Mafia member submits a target. **All active killers must agree** to perform the night kill. Killers cannot target any Mafia teammate.
- Silencers target an enemy player who cannot speak in the subsequent in-person discussion. Silencers cannot target Mafia teammates.
- If **no active killing Mafia remains**, the first living Silencer is promoted to **Killing Mafia** on the server. Their role card, description, target action and teammate projection update automatically. If other Silencers remain, the process can happen again after the promoted killer dies.
- Doctor protects one player, including themself. Detective privately learns the alignment of one target.
- Mafia players receive the names, role subtypes and alive statuses **only of other Mafia players**. Non-Mafia players never receive other players' secret roles or night targets. The narrator receives privileged information.
- Only the server validates and resolves night actions. The player browser cannot choose or overwrite its assigned role.
- One narrator-recorded elimination per day; no phone-based voting (the group votes physically).
- Players can refresh their page in the **same browser tab** and resume using a session token stored in `sessionStorage`. Do not share the narrator's device/session.

## Install and run

Requires **Node.js 20+**.

```bash
npm install
npm start
```

Open `http://localhost:3000` for local development. You can set `PORT` in the environment.

Tests (do not require an internet connection once Node is installed):

```bash
npm test
```

## Deploy to Railway from GitHub

1. **Back up** your existing `Enki1996/mafia-nights` repository before replacing anything. Prefer a separate branch and separate Railway service for this build.
2. Extract the ZIP; upload its **contents** (`server.js`, `game-logic.js`, `package.json`, `railway.json`, `public/`, and `test/`) to the repository **root**. Do not upload only the ZIP or keep the outer folder as an extra level.
3. If the existing repo still has an **old `package-lock.json`**, remove it or regenerate it locally with `npm install` so it matches this new `package.json`. Railway should use Node/Nixpacks and run `npm start` (already set in `package.json` and `railway.json`). Keep the Railway public URL for inviting players.
4. For room persistence, attach a Railway Volume at `/data` and set `MAFIA_DATA_DIR=/data`. The app writes `almudhakhar-rooms-v1.json`, deliberately avoiding collision with an older game's room file. With no volume, rooms exist only in memory and disappear on server restart/redeploy.
5. Test with several devices, including narrator reconnection, player reconnection, Mafia teammate secrecy, all five card displays, the Silencer promotion, win conditions, and role assignment before migrating the production URL.

## Deployment notes

- Socket.IO requires a working long-running Node service (Railway is suitable). **GitHub Pages alone cannot run the backend.**
- This ZIP does not include a `node_modules` folder or credentials; Railway runs `npm install` during build.
- The `public/assets/background.mp4` is used on the home screen only. Other pages use the supplied **static** backgrounds.
- This is a replacement architecture, not a database migration from the old game. Existing live rooms cannot be guaranteed to survive switching codebases.
- The user-provided game cards' Arabic role names are baked into the images; if the UI is switched to English, the additional translated role label appears under the card.
- **Automated tests cover the pure game-logic module.** Static/mobile visual checks ran in an offline browser with mocked Socket.IO. This environment did **not** have npm dependencies or a live Railway deployment available for full end-to-end network testing.

## Key files

- `server.js`: authorized Socket.IO events, persistence, room management.
- `game-logic.js`: secure room/role/action/round and Silencer promotion rules.
- `public/index.html`, `public/styles.css`, `public/app.js`: screens and interactive UI.
- `public/assets/`: original logo, backgrounds, video, five cards, classified envelope, hourglass, characters.
- `test/game-logic.test.js`: automated role privacy and game-rule tests.
