# Comoxy OG

## Overview
Comoxy OG is a community-driven launcher and tooling project that helps you play older versions of Fortnite through curated commands, a launcher, and setup guides. Downloadable releases are published on the Comoxy GitHub so you can get started without digging through source code.

## What Comoxy is (and isn’t)
Comoxy itself is not a “Fortnite builds archive.” It’s the launcher, commands, and setup resources used to run OG experiences. Builds referenced by the community may be external; Comoxy focuses on the tooling and instructions required to launch them.

## Features
- **Launcher releases:** Prebuilt downloads to quickly set up and run Comoxy OG without compiling.
- **Commands and cheats:** A dedicated commands/cheats repository to configure behavior in OG sessions.
- **Guides:** Community-written docs on making and running old Fortnite projects.
- **Walkthroughs:** Short videos showing how to use Comoxy and common commands (e.g., “open 127.0.0.1”).

## Installation
1. **Get the latest Comoxy release:** Download from the Comoxy GitHub releases page for a ready-to-run build.
2. **Extract and open the launcher:** Unzip the downloaded archive and run the launcher executable.
3. **Configure basics:** Follow the release notes and guide prompts; many tutorials reference simple commands like `open 127.0.0.1` to start local sessions.
4. **Optional extras:** If you need specific commands or cheats, grab them from the Comoxy Commands/Cheats repository.

## Quick start
- **Run the launcher** and select your setup option.
- (run the game server) and run new fortnite.
- **Use the documented commands** (`open 127.0.0.1`) to initialize a local session as shown in Comoxy’s tutorial videos.
- **Adjust configs** using the commands/cheats repo for fine-tuning behavior.

## Project structure
- **comoxyogfn (releases):** Primary distribution with downloadable builds of the Comoxy launcher.
- **FortniteComoxyCommands-Cheats:** Commands and cheats tailored for Comoxy sessions.
- **How-to-Make-an-old-Fortnite-project:** Guides and documentation for setup and experimentation.

## Community and support
- **YouTube tutorials:** Short videos demonstrate setup and usage, including common commands and Discord info for community support.

## Disclaimer
Comoxy is a fan-made, community-maintained project and is not affiliated with, endorsed by, or supported by Epic Games. Use for educational and nostalgic purposes only.

---
> Sources: 


# 🔧 CRITICAL FIXES NEEDED FOR YOUR INDEX.JS

## ❌ PROBLEM #1: SKINS DON'T LOAD
**Location:** Lines 5-17 and 76-89

### The Issue:
Your `athenaProfile` is loaded from disk (line 76-89) but **NEVER** merged into the profiles system. The `initProfile` function (lines 5-17) creates empty profiles with `items: {}`, so skins from athena.json are never used.

### The Fix:
**Add these lines after line 89:**

```javascript
// ✅ FIX: Store athenaProfile as template for new profiles
if (athenaProfile && Object.keys(athenaProfile).length > 0) {
  profiles._template = { athena: athenaProfile };
  console.log("✅ Athena profile stored as template");
}
```

**Then modify `initProfile` function (lines 5-17) to:**

```javascript
function initProfile(accountId) {
  if (!profiles[accountId]) {
    // ✅ FIX: Use loaded athena profile as template
    const athenaTemplate = profiles._template?.athena || {
      revision: 0,
      created: new Date().toISOString(),
      wipeNumber: 1,
      items: {},
      stats: { attributes: {} }
    };
    
    profiles[accountId] = {
      athena: JSON.parse(JSON.stringify(athenaTemplate)) // Deep clone
    };
  }
  return profiles[accountId];
}
```

---

## ❌ PROBLEM #2: NEWS DOESN'T WORK
**Location:** Line 23

### The Issue:
You import `contentPagesRouter` on line 23:
```javascript
import contentPagesRouter from "./routes/contentPagesRouter.js";
```

But you **NEVER MOUNT IT** to the Express app! It's imported but never used.

### The Fix:
**Find line 98** which says:
```javascript
app.use("/", mainRoutes);
```

**Add this line RIGHT AFTER IT:**
```javascript
app.use("/", mainRoutes);
app.use("/", contentPagesRouter);  // ✅ FIX: Mount the news router!
```

---

## ❌ PROBLEM #3: LOBBY BACKGROUNDS DON'T WORK
**Location:** Line 390 (inside the `/content/api/pages/fortnite-game` endpoint)

### The Issue:
Your `dynamicbackgrounds` section exists (lines 390-407) but it's being **BLOCKED** by the regex route on line 812:

```javascript
app.get(/^\/content\/api\/pages\/fortnite-game(\/.*)?$/, (_, r) => no(r));
```

This returns a 204 (No Content) response which overwrites your actual content pages endpoint!

### The Fix:
**REMOVE OR COMMENT OUT line 812:**

```javascript
// ❌ REMOVE THIS LINE - it blocks your news and backgrounds:
// app.get(/^\/content\/api\/pages\/fortnite-game(\/.*)?$/, (_, r) => no(r));
```

The proper endpoint at line 330-430 will then work correctly.

---

## 📋 SUMMARY OF ALL CHANGES

### Change #1: After line 89, ADD:
```javascript
// ✅ Merge athenaProfile into profiles template
if (athenaProfile && Object.keys(athenaProfile).length > 0) {
  profiles._template = { athena: athenaProfile };
  console.log("✅ Athena profile stored as template");
}
```

### Change #2: Replace lines 5-17 with:
```javascript
function initProfile(accountId) {
  if (!profiles[accountId]) {
    const athenaTemplate = profiles._template?.athena || {
      revision: 0,
      created: new Date().toISOString(),
      wipeNumber: 1,
      items: {},
      stats: { attributes: {} }
    };
    
    profiles[accountId] = {
      athena: JSON.parse(JSON.stringify(athenaTemplate))
    };
  }
  return profiles[accountId];
}
```

### Change #3: After line 98, ADD:
```javascript
app.use("/", contentPagesRouter);
```

### Change #4: DELETE line 812:
```javascript
// DELETE THIS LINE:
app.get(/^\/content\/api\/pages\/fortnite-game(\/.*)?$/, (_, r) => no(r));
```

---

## ✅ WHAT THESE FIXES DO

1. **Skins Fix:** Loads all skins from athena.json into new player profiles
2. **News Fix:** Actually mounts the contentPagesRouter so news displays  
3. **Backgrounds Fix:** Removes the blocking route that was preventing backgrounds from loading

---

## 🚀 TESTING AFTER FIXES

1. Restart your server
2. Check console for: `"✅ Athena profile stored as template"`
3. Login to game - skins should now appear in locker
4. Check lobby - news should display
5. Check lobby background - should no longer be default/black

---

## 📝 LINE NUMBERS REFERENCE

- **Lines 5-17:** `initProfile` function
- **Line 23:** `contentPagesRouter` import
- **Lines 76-89:** Athena profile loading
- **Line 98:** `mainRoutes` mounting
- **Lines 330-430:** Content pages endpoint (the GOOD one)
- **Line 812:** Blocking regex route (the BAD one)
