# RankIt

RankIt is a community ranking app where users vote on topics by arranging candidates in their preferred order. The votes are tallied using Borda Count to produce a community leaderboard.

This repo is a monorepo containing two projects:

- **`RankIt/`** — Flutter mobile app (Android, iOS, Web)
- **`RankIt-Web/`** — Laravel 10 REST API + Filament admin panel

---

## What it does

Users can browse ranking topics by category (Movies, Food, Games, etc.), drag and drop candidates into their preferred order, and submit a ballot. Points are assigned based on rank position (1st = 10pts, 2nd = 9pts, down to 10th = 1pt), and the totals across all votes determine the community standings.

The app also uses Gemini AI to generate short summaries of the community results, and Cloudinary for image uploads.

---

## Project Structure

```
RankIT-/
├── RankIt/RankIt/       # Flutter app
└── RankIt-Web/          # Laravel backend + admin panel
```

---

## Flutter App

**Stack:** Flutter (Dart), Firebase Auth, Cloud Firestore, Gemini API, Cloudinary, Provider

### Setup

1. Install dependencies:
   ```bash
   cd RankIt/RankIt
   flutter pub get
   ```

2. Add your `google-services.json` (Android) and `GoogleService-Info.plist` (iOS) to the respective platform folders.

3. Fill in your API keys in `lib/config/secrets.dart`:
   ```dart
   class Secrets {
     static const String geminiApiKey        = 'YOUR_GEMINI_API_KEY';
     static const String cloudinaryCloudName = 'YOUR_CLOUDINARY_CLOUD_NAME';
     static const String cloudinaryPreset    = 'YOUR_CLOUDINARY_UPLOAD_PRESET';
   }
   ```

4. Set your backend URL in `lib/services/config_service.dart`:
   ```dart
   static const String defaultBaseUrl = 'http://127.0.0.1:8000';
   ```

5. Run the app:
   ```bash
   flutter run
   ```

> If you want to test without Firebase, set `useMock = true` in `lib/services/auth_service.dart` to use local mock data instead.

---

## Laravel Backend

**Stack:** Laravel 10, MySQL, Filament v3, custom Firebase JWT middleware

### Setup

1. Install dependencies:
   ```bash
   cd RankIt-Web
   composer install
   ```

2. Copy and configure the env file:
   ```bash
   cp .env.example .env
   ```
   ```ini
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=rankit
   DB_USERNAME=root
   DB_PASSWORD=your_password
   ```

3. Generate the app key:
   ```bash
   php artisan key:generate
   ```

4. Run migrations and seed:
   ```bash
   php artisan migrate:fresh --seed
   ```

5. Start the server:
   ```bash
   php artisan serve
   ```

The API runs at `http://127.0.0.1:8000` and the admin panel is at `/admin`.

---

## How to Use

### As a User (Mobile App)

1. **Sign in** — create an account with email and password, or use Google Sign-In.
2. **Browse topics** — the home screen shows ranking topics grouped by category (Movies, Food, Games, etc.). You can search or filter to find what you're looking for.
3. **Open a topic** — tap any topic to see its candidates and the current community leaderboard.
4. **Vote** — tap the vote button and drag candidates into your preferred order. You can rank up to 10 items. Submit when you're done.
5. **See results** — the leaderboard updates to reflect your vote. You can see how the community ranks each candidate based on everyone's combined ballots.
6. **AI summary** — tap the summary button on any leaderboard to get a short Gemini-generated insight about the community's preferences.
7. **Suggest a candidate** — if you think something is missing from a topic's list, you can submit a suggestion for the admin to review.
8. **Profile** — view your account info and manage your session from the profile screen.

---

### As an Admin (Web Panel)

The admin panel is available at `/admin` on the Laravel server. Log in with your admin credentials.

- **Dashboard** — shows an overview of total categories, topics, candidates, and submitted ballots.
- **Categories** — create and manage the top-level categories that topics are grouped under.
- **Ranking Topics** — create ranking topics, assign them to a category, set a description, and manage which candidates belong to them.
- **Candidates** — add or edit individual candidate items (name, description, image) within any topic.
- **Candidate Suggestions** — review suggestions submitted by users. Approve them to add the candidate to the topic, or dismiss them.

---

## How Borda Count Works

Each user submits an ordered list of up to 10 candidates. Points are awarded based on position:

| Position | Points |
|:---:|:---:|
| 1 | 10 |
| 2 | 9 |
| ... | ... |
| 10 | 1 |

Points are summed across all votes to produce the final standings.
