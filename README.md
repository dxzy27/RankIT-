<div align="center">

# 🏆 RankIt

**A community-powered ranking platform — vote, rank, and discover what the crowd thinks is best.**

[![Flutter](https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter&logoColor=white)](https://flutter.dev)
[![Laravel](https://img.shields.io/badge/Laravel-10.x-FF2D20?logo=laravel&logoColor=white)](https://laravel.com)
[![Firebase](https://img.shields.io/badge/Firebase-Auth%20%26%20Firestore-FFCA28?logo=firebase&logoColor=black)](https://firebase.google.com)
[![Filament](https://img.shields.io/badge/Filament-3.x-F59E0B?logo=php&logoColor=white)](https://filamentphp.com)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

</div>

---

## 📖 Overview

**RankIt** is a full-stack Mobile Cloud Computing (MCC) application that lets users create ranking topics (e.g. *"Best Marvel Movies"*, *"Top 10 Foods"*), vote by arranging candidates in their preferred order, and see live-aggregated community leaderboards powered by the **Borda Count** algorithm.

The project is split into two sub-projects in this monorepo:

| Folder | Description |
|---|---|
| [`RankIt/`](./RankIt/RankIt/) | Flutter mobile app (iOS, Android, Web) |
| [`RankIt-Web/`](./RankIt-Web/) | Laravel 10 REST API backend + Filament admin panel |

---

## ✨ Key Features

### 📱 Mobile App (Flutter)
- 🔐 **Firebase Authentication** — Email/Password and Google Sign-In with persistent sessions
- 🗂️ **Category Browsing** — Explore topics by category (Movies, Food, Games, Technology, etc.)
- 🖱️ **Drag-and-Drop Voting** — Intuitively rearrange candidates to submit your Top 10 ballot
- 📊 **Borda Count Leaderboards** — Votes are weighted (Rank 1 = 10pts → Rank 10 = 1pt) and aggregated in real time
- 🤖 **Gemini AI Summaries** — Auto-generated pop-culture insights on community preferences via `gemini-1.5-flash`
- 🖼️ **Cloudinary Image Uploads** — Upload cover photos for categories and ranking topics
- 🔍 **Live Search & Filters** — Find topics and categories instantly
- 🛡️ **Offline-First Fallback** — Local mock data, mock auth, and Unsplash images for local dev without credentials

### 🖥️ Backend & Admin (Laravel + Filament)
- 🚀 **RESTful API** — Full CRUD for categories, topics, candidates, ballots, and leaderboards
- 🔒 **Firebase JWT Middleware** — Decodes Firebase ID Tokens on every request to auto-register/sync users in MySQL
- 📋 **Filament Admin Panel** — Beautiful admin UI to manage topics, candidates, categories, and submissions
- 🧮 **Borda Count Engine** — Server-side aggregation of all user ballots into ranked leaderboards
- 💡 **Candidate Suggestions** — Users can suggest new candidate items for any topic

---

## 🏗️ Architecture

```
RankIt (Monorepo)
├── RankIt/                     # Flutter app
│   └── RankIt/
│       ├── lib/
│       │   ├── config/         # API credentials & secrets
│       │   ├── models/         # Category, Item, RankingList, User, Vote
│       │   ├── providers/      # Auth & Ranking state (Provider / ChangeNotifier)
│       │   ├── services/       # Firebase, Firestore, Cloudinary, Gemini, REST API
│       │   ├── theme/          # Dark Obsidian theme with glassmorphic cards
│       │   └── views/          # Screens (Home, Login, Leaderboard, Ranking, Profile)
│       ├── android/
│       ├── ios/
│       └── web/
│
└── RankIt-Web/                 # Laravel backend
    ├── app/
    │   ├── Filament/           # Admin panel resources & widgets
    │   ├── Http/
    │   │   ├── Controllers/    # API controllers
    │   │   └── Middleware/     # VerifyFirebaseToken middleware
    │   └── Models/             # Eloquent models
    ├── database/
    │   ├── migrations/         # MySQL schema (Firebase UIDs as string keys)
    │   └── seeders/            # Mock seed data
    └── routes/
        └── api.php             # API route definitions
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Mobile Framework | Flutter (Dart) |
| State Management | Provider (ChangeNotifier) |
| Auth | Firebase Authentication + Google Sign-In |
| Real-time Database | Cloud Firestore |
| AI | Google Gemini API (`gemini-1.5-flash`) |
| Image Storage | Cloudinary |
| Backend Framework | Laravel 10 (PHP 8.1+) |
| Database | MySQL |
| Admin Panel | Filament v3 |
| API Auth | Firebase JWT Middleware (custom) |

---

## 🚀 Getting Started

### Prerequisites

- [Flutter SDK](https://flutter.dev/docs/get-started/install) (Dart `^3.11.4`)
- [PHP](https://www.php.net/) `^8.1` + [Composer](https://getcomposer.org/)
- [MySQL](https://www.mysql.com/) server
- A [Firebase](https://firebase.google.com/) project with Auth + Firestore enabled
- A [Cloudinary](https://cloudinary.com/) account
- A [Gemini API Key](https://aistudio.google.com/app/apikey)

---

### 📱 Flutter App Setup (`RankIt/RankIt/`)

**1. Install dependencies**
```bash
cd RankIt/RankIt
flutter pub get
```

**2. Configure Firebase**

Place your `google-services.json` (Android) and `GoogleService-Info.plist` (iOS) into the respective platform folders. Then update `lib/firebase_options.dart` with your Firebase project config.

**3. Set API credentials**

Open `lib/config/secrets.dart` and fill in your keys:
```dart
class Secrets {
  static const String geminiApiKey        = 'YOUR_GEMINI_API_KEY';
  static const String cloudinaryCloudName = 'YOUR_CLOUDINARY_CLOUD_NAME';
  static const String cloudinaryPreset    = 'YOUR_CLOUDINARY_UPLOAD_PRESET';
}
```

**4. Point to backend**

In `lib/services/config_service.dart`, set the base URL of your running Laravel server:
```dart
static const String defaultBaseUrl = 'http://127.0.0.1:8000';
```

**5. Run the app**
```bash
flutter run
```

> 💡 **No credentials?** Enable mock mode in `lib/services/auth_service.dart` by setting `static bool useMock = true;` to develop locally without any cloud setup.

---

### 🖥️ Laravel Backend Setup (`RankIt-Web/`)

**1. Install PHP dependencies**
```bash
cd RankIt-Web
composer install
```

**2. Configure environment**

Copy the example env file and edit it:
```bash
cp .env.example .env
```

Update the database section in `.env`:
```ini
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=rankit
DB_USERNAME=root
DB_PASSWORD=YOUR_PASSWORD
```

**3. Generate app key**
```bash
php artisan key:generate
```

**4. Run migrations & seed**
```bash
php artisan migrate:fresh --seed
```

**5. Start the server**
```bash
php artisan serve
```
> The API will be live at `http://127.0.0.1:8000`

**6. Access the Admin Panel**

Navigate to `http://127.0.0.1:8000/admin` to access the Filament admin dashboard.

---

## 🔌 API Reference

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| `GET` | `/api/categories` | List all categories | ❌ |
| `GET` | `/api/topics` | List all ranking topics | ❌ |
| `GET` | `/api/topics/{id}` | Get a single topic with candidates | ❌ |
| `GET` | `/api/leaderboard/{topicId}` | Get Borda Count standings for a topic | ❌ |
| `POST` | `/api/submissions` | Submit / update a user ballot | ✅ Firebase JWT |
| `POST` | `/api/topics/{id}/suggestions` | Suggest a new candidate | ❌ |
| `GET` | `/api/user` | Get authenticated user profile | ✅ Firebase JWT |
| `POST` | `/api/logout` | Invalidate session | ✅ Firebase JWT |

**Authenticated requests** must include the Firebase ID Token:
```http
Authorization: Bearer <firebase_id_token>
```

---

## 🔒 Firebase JWT Middleware

The custom `VerifyFirebaseToken` middleware automatically:
1. Decodes the Firebase ID Token from the `Authorization` header
2. Extracts the user's UID, email, and display name
3. Creates or syncs the user record in the local MySQL `users` table
4. Attaches the user to the request for downstream controllers

> For local testing without a real Firebase token, you can pass a raw UID string (≥ 20 characters) as the Bearer token.

---

## 🗳️ How Borda Count Works

RankIt uses the **Borda Count** voting method to aggregate community preferences fairly:

| Rank Position | Points Awarded |
|:---:|:---:|
| 1st | 10 |
| 2nd | 9 |
| 3rd | 8 |
| ... | ... |
| 10th | 1 |

Every user submits an ordered ballot of up to 10 candidates. Points are summed across all votes, and candidates are ranked by total score — giving a true picture of the **community's collective preference**.

---

## 📂 Repository Structure

```
RankIT-/
├── RankIt/
│   └── RankIt/          # Flutter mobile application
├── RankIt-Web/          # Laravel REST API + Filament admin
└── .gitignore           # Monorepo-level ignores
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'Add some feature'`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License**.

---

<div align="center">
Made with ❤️ using Flutter, Laravel & Firebase
</div>
