# Exam Prep Tracker

Exam Prep Tracker is a web app for organizing exam preparation and practicing multiple-choice questions by exam, chapter, and topic.

## Live app

**Open the app:** [https://exam-prep-tracker-81cd5.web.app](https://exam-prep-tracker-81cd5.web.app)

Sign in with Google and connect Google Drive to use the app. Questions and study progress are saved both in the current browser and in a visible `ExamPrepData.json` file in that user's own Google Drive. This avoids storing question data in Firestore; each user uses their own Drive storage.

## Features

- Create exams with chapters and topics.
- Add questions individually or import multiple-choice questions in bulk.
- Filter the Question Bank by exam, chapter, topic, and status; search and practice the filtered questions.
- Practice questions and review revision questions.
- Back up and restore your question data.
- Optional AI-assisted question creation using your own API key.
- Sign in with Google and sync a JSON data file with the signed-in user's Google Drive.

## Run locally

The app is a single-page website in `index.html` and loads Firebase SDK modules from Google's CDN. From the project folder, start a local web server:

```powershell
python -m http.server 8000
```

Then open [http://localhost:8000](http://localhost:8000).

## Google and Firebase setup

This project is configured for the Firebase project `exam-prep-tracker-81cd5`. Firebase Authentication is used for Google sign-in. Google Drive stores each user's data file. To use your own Firebase project:

1. Create a Firebase project and register a Web app.
2. Enable Google in **Authentication → Sign-in method**.
3. In the linked Google Cloud project, enable the **Google Drive API**. The app requests the `drive.file` permission so it can create and update its own `ExamPrepData.json` file.
4. Update the Firebase configuration in `index.html` to match your Firebase Web app.
5. Add your local development domain and deployed website domain to Firebase Authentication's authorized domains.

The Firebase web configuration is public client configuration, not a server secret. The app does not write new study data to Firestore. If an older Firestore `userData/{userId}` backup exists, the app reads it once when no Drive file exists and copies it to Drive; keep the old Firestore rules until that migration succeeds.

## Deploy to Firebase Hosting

Install Node.js, then from the project folder run:

```powershell
npx firebase-tools login
npx firebase-tools init hosting
npx firebase-tools deploy --only hosting
```

When initializing Hosting, select the existing Firebase project, use `.` as the public directory, configure it as a single-page app if prompted, and do not overwrite `index.html`.

## Upload to GitHub

Create a new empty repository on GitHub, then run these commands from the project folder (replace the URL with your repository's URL):

```powershell
git init
git add index.html README.md firebase.json .firebaserc .gitignore
git commit -m "Add Exam Prep Tracker"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git push -u origin main
```

The live app remains hosted on Firebase Hosting; GitHub stores the project source and README.

## Data and privacy

- Google sign-in uses Firebase Authentication. After sign-in, choose **Connect Google Drive** and approve Drive access.
- The app stores a visible `ExamPrepData.json` file in each user's own Google Drive and a separate local copy in that browser profile.
- On a new device, sign in and connect Drive to download the latest file. Each device's browser-local copy is separate.
- If the Drive file is deleted or Drive is full, the current device keeps its local copy. Delete the Drive file (and empty Drive trash if needed to reclaim quota), reconnect Drive, then use **Sync now / restore from this device** to upload that device's local copy again.
- If Drive has a file, sync compares its `updated` timestamp with the local copy and uses the newer one. Simultaneous edits on different devices are not merged; sync the latest copy before switching devices.
- The Google Drive access token is temporary and kept only in memory. After reloading the page, reconnect Drive to sync again. The local copy remains available while Drive is disconnected.
- Keep a downloaded JSON backup too. Browser data can be lost if site data is cleared or a browser profile is removed.
- AI-assisted features are optional. Any API key you enter is for your own use; do not commit private API keys to GitHub.
