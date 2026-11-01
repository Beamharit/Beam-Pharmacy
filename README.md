# Beam Pharmacy — Voice Task Manager (v2)

## Features
- Thai speech-to-text, when supported by the browser (Web Speech API, microphone permission and often internet required)
- Rule-based extraction of Thai dates/times in simple utterances (not full AI; check the parsed result before saving)
- Create, edit, complete, delete and search tasks; Today, Upcoming and status filters
- Priority and due date + time
- Notification permission and on-time notifications **while the web app is open/active**. Checks every 15 seconds and when returning to the app, with a 5-minute late window. This is **not** server push and cannot guarantee reminders when the app/browser is closed, asleep or suspended.
- Existing tasks are preserved by using the original `beam-pharmacy-tasks-v1` localStorage key.
- Offline PWA caching (speech recognition may require an internet connection).

## Install / update on GitHub Pages
Replace `index.html`, `sw.js`, `README.md` with the files in this package. Keep the `logo.png` and `manifest.webmanifest` at the repository root. GitHub Pages must be enabled for the `main` branch at `/ (root)`. Open https://beamharit.github.io/Beam-Pharmacy/ after deployment. If seeing the old version, reload / reopen the app.

## Next step for real background push
Add a backend / cloud scheduler (e.g. Cloudflare Workers + durable scheduled task storage, or Firebase Cloud Messaging + server scheduler) to store and dispatch scheduled Web Push notifications using VAPID keys; subscribe devices to Push API and handle incoming `push` events in the service worker. iOS requires an installed Home Screen web app for Web Push on supported iOS versions. This is not implemented in the present static GitHub Pages build. Cloud sync would require authentication and server storage too.

## Privacy
Tasks are currently stored in localStorage on each browser/device and do not sync between devices. Do not use it to store patient-identifying or confidential clinical information. Some browsers may send spoken audio to a speech-recognition provider.
