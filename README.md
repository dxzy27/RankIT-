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

## API Endpoints

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| GET | `/api/categories` | List all categories | No |
| GET | `/api/topics` | List all topics | No |
| GET | `/api/topics/{id}` | Get a single topic | No |
| GET | `/api/leaderboard/{topicId}` | Get leaderboard standings | No |
| POST | `/api/submissions` | Submit a ballot | Firebase JWT |
| POST | `/api/topics/{id}/suggestions` | Suggest a candidate | No |
| GET | `/api/user` | Get current user | Firebase JWT |
| POST | `/api/logout` | Log out | Firebase JWT |

Authenticated endpoints require a Firebase ID Token:
```http
Authorization: Bearer <firebase_id_token>
```

The backend middleware decodes this token and automatically creates or updates the user record in MySQL on each request.

> For local testing without a real token, you can pass a raw UID string (20+ characters) as the Bearer value.

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
