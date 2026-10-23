# Exam Prep Tracker

Exam Prep Tracker is a web app for organizing exam preparation and practicing multiple-choice questions by exam, chapter, and topic.

## Live app

**Open the app:** [https://exam-prep-tracker-81cd5.web.app](https://exam-prep-tracker-81cd5.web.app)

Sign in with Google to use the app. Your questions and study progress are saved to your Firebase account so you can access them from another device.

## Features

- Create exams with chapters and topics.
- Add questions individually or import multiple-choice questions in bulk.
- Filter the Question Bank by exam, chapter, topic, and status; search and practice the filtered questions.
- Practice questions and review revision questions.
- Back up and restore your question data.
- Optional AI-assisted question creation using your own API key.
- Sign in with Google and sync your data through Firebase Firestore.

## Run locally

The app is a single-page website in `index.html` and loads Firebase SDK modules from Google's CDN. From the project folder, start a local web server:

```powershell
python -m http.server 8000
```

Then open [http://localhost:8000](http://localhost:8000).

## Firebase setup

This project is configured for the Firebase project `exam-prep-tracker-81cd5`. To use your own Firebase project:

1. Create a Firebase project and register a Web app.
2. Enable Google in **Authentication → Sign-in method**.
3. Create a **Cloud Firestore** database.
4. In Firestore Rules, allow each signed-in user to access only their own `userData/{userId}` document:

   ```text
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /userData/{userId} {
         allow read, write: if request.auth != null
                            && request.auth.uid == userId;
       }
     }
   }
   ```

5. Update the Firebase configuration in `index.html` to match your Firebase Web app.
6. Add your local development domain and deployed website domain to Firebase Authentication's authorized domains.

The Firebase web configuration is public client configuration, not a server secret. Firestore access must remain protected by security rules.

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
git add index.html README.md firebase.json .firebaserc
git commit -m "Add Exam Prep Tracker"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git push -u origin main
```

The live app remains hosted on Firebase Hosting; GitHub stores the project source and README.

## Data and privacy

- Cloud data is stored in Firestore under `userData/{your Firebase user ID}`.
- Each signed-in account can access only its own document when the rules above are published.
- The app also keeps a local browser copy and provides backup/restore controls.
- AI-assisted features are optional. Any API key you enter is for your own use; do not commit private API keys to GitHub.
