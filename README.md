# Comoxy OG

## Overview

Comoxy OG is a launcher and a backend tooling suite designed to let you relive classic Fortnite experiences on your own terms. The project provides:
- A downloadable launcher for OG Fortnite
- Guides and walkthroughs for setup
- Scripts and commands for game customization
- A backend server for running essential game functions

> **Disclaimer:** Comoxy is a fan-made, community-led project and is **not affiliated with Epic Games**.

---

## What Can I Do With Comoxy OG?

- **Play older Fortnite builds** (not included; see guides)
- Use prebuilt launchers to simplify your setup—no coding needed
- Access cheats, mods, and commands from a dedicated extras repo
- Watch tutorials and walkthrough videos for each step

---

## How Does the Backend Work?

The backend is a custom server built with Node.js and Express that recreates endpoints and services expected by OG Fortnite clients.

**Key Features:**
- Handles API endpoints that the real Fortnite backend would provide
- Lets you host local sessions using the launcher & your own builds
- Manages player profiles (including skins, items, stats)
- Custom endpoint for news, lobby backgrounds, and dynamic content
- Extensible command system for cheats and server-side controls

**Backend Technical Notes:**
- Express routes serve as proxies and logic handlers for the game client
- The player profile system (`athena`) is loaded from disk and used as a template for each new local account
- Cheat commands and configuration changes can be added without restarting everything
- News content and lobby backgrounds are served via specific API endpoints matching what the game expects

**Critical Fixes / Gotchas:**  
The backend's most important fixes (already present or needed) are all documented in the lower part of this README, covering profile loading, Express route mounting, lobby backgrounds, and more. Make sure to keep these in sync if you modify the backend.

---

## Installation

1. **Download** the latest Comoxy launcher from Releases.
2. **Extract** and run the launcher executable.
3. **Follow docs/tutorials** to acquire OG Fortnite build files (not included here).
4. **Configure** connection endpoints (which point to the local backend server).
5. (Optional) **Install extra commands or cheats** from the extras repo.

---

## Quick Start

- Run the launcher and select your setup option
- Start the local server backend (`node index.js` usually, see docs)
- Use the launcher to point your game client at your local server
- Use documented commands (like `open 127.0.0.1`) to create sessions
- Customize gameplay using cheats or config files if desired

---

## Project Structure

- `comoxyogfn` — Main downloadable launcher (binaries/releases)
- `FortniteComoxyCommands-Cheats` — Scripts, cheats, and OG commands
- `How-to-Make-an-old-Fortnite-project` — Documentation and step-by-step guides

---

## Community & Support

- YouTube tutorials: Video walkthroughs for setup, customization, and gameplay ([see channel link in releases or docs])
- Discord: Community support and troubleshooting

---

## Disclaimer

Comoxy is a fan-made, community-maintained project. **Not affiliated with, endorsed by, or supported by Epic Games.**  
Use for educational and nostalgic purposes only.

---

## Backend Developer Notes & Critical Fixes

Comoxy OG backend replicates core Fortnite API endpoints and is designed for _local, non-commercial use only_. If you're modifying the backend JS, make sure you've addressed the following:

1. **Profiles System:** Ensure the `athenaProfile` (from disk) is loaded and used as a template for every new player profile—this makes skins and items available.
2. **API Routing:** All routers like `contentPagesRouter` **must be mounted after** your main routes in `index.js`, or news/lobby will break.
3. **Lobby Background/News:** Remove or comment out any generic wildcard route (like `app.get(/^\\/content\\/api\\/pages\\/fortnite-game(\\/.*)?$/, ...)` which blocks your actual endpoint logic.
4. See the full "CRITICAL FIXES NEEDED FOR YOUR INDEX.JS" section below for exact code changes if you encounter bugs with player profiles, news, or lobby visuals.

---

## CRITICAL FIXES NEEDED FOR YOUR INDEX.JS

### PROBLEM 1: SKINS DON'T LOAD 
... (full step-by-step as in current README)

### PROBLEM 2: NEWS DOESN'T WORK
... (full fix and explanation as in current README)

### PROBLEM 3: LOBBY BACKGROUNDS DON'T WORK
... (full fix and explanation as in current README)

(See below for line-by-line details.)

---

**For full technical details, guidance, and troubleshooting, see the code and guides in this repo and in `FortniteComoxyCommands-Cheats`.**
