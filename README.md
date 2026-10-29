# Exam Prep Tracker

Exam Prep Tracker is a web app for organizing exam preparation and practicing multiple-choice questions by exam, chapter, and topic.

## Live app

**Open the app:** [https://exam-prep-tracker-81cd5.web.app](https://exam-prep-tracker-81cd5.web.app)

Sign in with Google and connect Google Drive to use the app. Questions and study progress are saved both in the current browser and in a visible `ExamPrepData.json` file in that user's own Google Drive. This avoids storing question data in Firestore; each user uses their own Drive storage.

Personal notes can be created, categorized, searched, edited, and read in full or minimized previews. Topic notes, personal notes, coding questions, code drafts, test-case inputs/expected outputs, coding revision notes, and the resized profile photo are included in the same Drive file and downloadable backup. The coding section currently stores and displays code and test cases; it does not execute code or report test pass/fail results.

## Features

- Create exams with chapters and topics.
- Save notes on each topic before or alongside its questions.
- Create and edit formatted standalone notes with a sticky editing toolbar, text and heading colors, highlights, images, font sizes, alignment, headings, lists, quotes, undo/redo, and clear formatting. Paste formatted text, tables, and images directly from ChatGPT or other documents; organize notes with optional categories, search them, and expand or minimize each note while reading.
- Use an icon-labeled vertical navigation and see account name, photo, and email when provided by the sign-in account.
- Upload or change a profile photo; a resized copy is saved with the account's Drive data and backup.
- View total exams, chapters, topics, MCQ questions, coding questions, and overall MCQ practice progress on the dashboard.
- Add questions individually or import multiple-choice questions in bulk.
- After saving, open the selected topic to review and edit its MCQs; practice attempts are tracked per device and retained with saved progress.
- Filter the Question Bank by exam, chapter, topic, and status; search and practice the filtered questions.
- Practice questions and review revision questions.
- Organize coding questions by topic, save C++, Java, Python, and JavaScript code drafts, add multiple test cases, and review concept notes in Coding Revision.
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
git add index.html README.md firebase.json .firebaserc .gitignore .github/workflows/firebase-hosting.yml
git commit -m "Add Exam Prep Tracker"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git push -u origin main
```

The live app remains hosted on Firebase Hosting; GitHub stores the project source and README.

## Automatically deploy with GitHub Actions

The workflow in `.github/workflows/firebase-hosting.yml` deploys the site to Firebase Hosting whenever code is pushed to the `main` branch. It can also be started manually from the repository's **Actions** tab.

Before the first deploy, add a Firebase service-account key as a GitHub Actions secret:

1. In Google Cloud Console for `exam-prep-tracker-81cd5`, create a service account and grant it the **Firebase Hosting Admin** role.
2. Create and download a JSON key for that service account. Keep it private; never commit it or paste it into source code.
3. On GitHub, open **Settings → Secrets and variables → Actions → New repository secret**.
4. Set the secret name to `FIREBASE_SERVICE_ACCOUNT_EXAM_PREP_TRACKER` and paste the full contents of the JSON key as its value.
5. Commit and push the workflow and app changes to `main`. The workflow will publish them to the live Hosting site.

After saving the secret on GitHub, securely remove the downloaded key file from your computer if you do not need to retain it. If the key is ever exposed, revoke it in Google Cloud Console and replace the GitHub secret.

## Data and privacy

- Google sign-in uses Firebase Authentication. After sign-in, choose **Connect Google Drive** and approve Drive access.
- The app stores a visible `ExamPrepData.json` file in each user's own Google Drive and a separate local copy in that browser profile.
- On a new device, sign in and connect Drive to download the latest file. Each device's browser-local copy is separate.
- If the Drive file is deleted or Drive is full, the current device keeps its local copy. Delete the Drive file (and empty Drive trash if needed to reclaim quota), reconnect Drive, then use **Sync now / restore from this device** to upload that device's local copy again.
- Sync merges exams, chapters, topics, questions, coding questions, profile-photo choice, and deletion records across devices instead of replacing the entire backup. Independent practice attempts on different devices are added together. For edits to the same item, the most recently edited version is kept. Drive updates use an ETag precondition and retry the merge if another device changes the file at the same time.
- Connect Google Drive and use **Sync now** on each device to fetch and merge current data. Changes made while offline stay local until that device reconnects and syncs. Avoid using an old downloaded backup as the only copy; restoring a backup now merges its data into the current account.
- The Google Drive access token is temporary and kept only in memory. After reloading the page, reconnect Drive to sync again. The local copy remains available while Drive is disconnected.
- Keep a downloaded JSON backup too. Browser data can be lost if site data is cleared or a browser profile is removed.
- AI-assisted features are optional. Any API key you enter is for your own use; do not commit private API keys to GitHub.
