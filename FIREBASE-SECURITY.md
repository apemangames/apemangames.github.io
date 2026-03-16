# Firebase Security — APEMAN Games

## The Problem

Right now the Firebase Realtime Database is completely open. Anyone can:
- Read every room, every player name, every game state
- Write/delete data in any room
- Dump the entire database with one URL

Try it yourself — paste this in a browser:
```
https://apeman-games-default-rtdb.firebaseio.com/rooms.json?shallow=true
```
You'll see every room code that's ever been created.

## The Fix

You need to add **Security Rules** in the Firebase Console. The API key in your code is fine — Firebase API keys are meant to be public. The rules are what control who can read and write.

### Step 1: Open Firebase Console

1. Go to https://console.firebase.google.com
2. Sign in with the Google account that owns the `apeman-games` project
3. Click on **apeman-games**

### Step 2: Set Security Rules

1. In the left sidebar, click **Realtime Database**
2. Click the **Rules** tab
3. Replace the existing rules with:

```json
{
  "rules": {
    "rooms": {
      "$roomId": {
        ".read": true,
        ".write": true
      }
    },
    ".read": false,
    ".write": false
  }
}
```

4. Click **Publish**

### What This Does

- **Blocks** reading the full database (no more dumping all rooms)
- **Allows** reading and writing within individual rooms (so the game still works)
- Players can still join rooms, update scores, etc.
- But nobody can list all rooms or access data outside a room

### Step 3 (Optional): Clean Up Old Rooms

There are ~100 old room codes sitting in the database from past games. You can delete them:

1. In Firebase Console → Realtime Database → **Data** tab
2. Hover over **rooms** and click the **X** to delete all
3. New rooms will be created automatically when someone hosts a game

### Step 4 (Optional, Stronger Rules)

If you want tighter security later, you could add rules like:
- Rooms auto-expire after 24 hours
- Only the host can delete a room
- Rate limit room creation

But the rules above are a good start — they stop the main risk (full database exposure) without breaking anything.

## What About the API Key in the Code?

The Firebase config block in your HTML (`apiKey`, `authDomain`, etc.) is **not a secret**. Firebase is designed this way — the API key just identifies your project. Security comes from the rules above, not from hiding the key.

Don't waste time trying to hide it — just make sure the rules are set.
