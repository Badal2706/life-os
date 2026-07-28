# Setup guide — free cross-device sync

Total cost: $0. You need a Google account (your Gmail works).

## 1. Create a free Firebase project

1. Go to https://console.firebase.google.com
2. Click "Add project" (or "Create a project").
3. Give it any name, e.g. `my-routine-tracker`. Click Continue.
4. Disable Google Analytics for this project (not needed) → Create project.
5. Wait for it to finish, then click Continue.

## 2. Turn on Firestore (the free database)

1. In the left sidebar, click "Build" → "Firestore Database".
2. Click "Create database".
3. Choose "Start in production mode" → Next.
4. Pick any location close to you → Enable.

## 3. Set the security rules

1. Still in Firestore, click the "Rules" tab at the top.
2. Replace everything with this:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /routineGoalsApps/{docId} {
      allow read, write: if true;
    }
  }
}
```

3. Click "Publish".

(This allows anyone who knows your private sync code — a random 64-character
id derived from it — to read/write that one document. Nobody can guess it or
browse other documents. Keep your sync code private, same as a password.)

## 4. Register a web app and get your config

1. Click the gear icon (top left, next to "Project Overview") → "Project settings".
2. Scroll to "Your apps" → click the `</>` (web) icon.
3. Give it a nickname, e.g. `routine-web` → Register app.
4. You'll see a code block with a `firebaseConfig` object like:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "my-routine-tracker.firebaseapp.com",
  projectId: "my-routine-tracker",
  storageBucket: "my-routine-tracker.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef123456"
};
```

5. Copy those 6 values.
6. Open `index.html` in this folder, find the `firebaseConfig` block near the
   top of the `<script>` section, and paste your real values in place of the
   `YOUR_...` placeholders. Save the file.

## 5. Host it for free

1. Go to https://app.netlify.com/drop
2. Drag this whole folder (`pwa-app`) onto the page.
3. You'll get a live HTTPS URL, e.g. `https://something.netlify.app`.
4. Open that URL on your laptop and your phone.
5. Tap "⚙ Sync" in the app, type any private phrase (e.g. a made-up sentence
   only you know), tap Connect — do this on both devices with the **same
   phrase**. Now both devices show and edit the same data in real time.

No further cost: Firebase's free tier (Spark plan) and Netlify's free tier
comfortably cover an app this small — no credit card required anywhere.

## Notes

- If you ever lose your sync code, your data is still safe in Firestore, but
  you'd need to remember or guess it to reconnect — there's no "forgot
  password" recovery since there's no login system.
- To view/edit your raw data manually, go to Firestore Database → Data tab in
  the Firebase console; your data lives under the `routineGoalsApps` collection.
