# Feed My Sheep – Prototype App

## Overview

**Feed My Sheep** is a service missionary coordination app designed to make missionary efforts more effective. The app allows missionaries to view assignments, track service activities, and navigate using offline/online maps.
This is a prototype intended for review by the committee, with a backend that can easily swap to the LDS Church’s authentication and hosting systems when granted access.

---

## Core Features

* **Offline-first support** with automatic background sync when connected.
* **Map integration** (OpenStreetMap for prototype; can swap to LDS or other APIs).
* **Pin markers** for assignments, locations, and service opportunities.
* **Role-based accounts** (service missionaries, leaders, coordinators).
* **Scalable backend API** with placeholder authentication.
* **AI integration potential** (e.g., automated assignment recommendations, natural language reporting).

---

## Tech Stack

**Frontend:** Kotlin (Android Studio) – Native Android
**Backend:** Node.js + Express (VS Code) – REST API
**Database:** SQLite (offline) + MongoDB Atlas (online sync)
**Maps:** OpenStreetMap (via Leaflet or MapLibre SDK)
**Version Control:** Git + GitHub

---

## Folder Structure

### Frontend (Android Studio)

```
feed-my-sheep-frontend/
 ├── app/src/main/java/com/feedmysheep/
 │   ├── MainActivity.kt
 │   ├── ui/       # UI components
 │   ├── data/     # Local storage & sync logic
 │   └── network/  # API calls
 ├── app/src/main/res/
 │   ├── layout/   # XML layouts
 │   ├── drawable/ # Icons/images
 │   └── values/   # Strings, colors
```

### Backend (VS Code)

```
feed-my-sheep-backend/
 ├── src/
 │   ├── index.js         # Server entry point
 │   ├── routes/          # API route handlers
 │   ├── controllers/     # Business logic
 │   ├── models/          # Database models
 │   └── config/          # DB & API config
 ├── package.json
 ├── .env                 # Environment variables
```

---

## Version Control Setup

We will use **two GitHub repositories**:

* `feed-my-sheep-backend` – for API, database, and sync logic.
* `feed-my-sheep-frontend` – for Android app code.

**Why separate repos?**

* Allows backend and frontend teams to work independently.
* Easier to manage commits and avoid merge conflicts.

---

## Development Workflow

### Frontend Team (Android Studio)

1. Clone **frontend repo**:

   ```bash
   git clone https://github.com/YOURNAME/feed-my-sheep-frontend.git
   ```
2. Open in Android Studio.
3. Run on Android emulator or connected device.
4. Make API calls to backend (e.g., `http://localhost:3000/api/...`).

### Backend Developer (VS Code)

1. Clone **backend repo**:

   ```bash
   git clone https://github.com/YOURNAME/feed-my-sheep-backend.git
   ```
2. Install dependencies:

   ```bash
   npm install
   ```
3. Run server:

   ```bash
   npm run dev
   ```

---

## API Contract (Phase 1 – Example)

**GET /api/tasks**

```json
[
  {
    "id": 1,
    "title": "Help Sister Jones with yard work",
    "lat": 40.123,
    "lng": -111.654,
    "status": "open"
  }
]
```

**POST /api/tasks**

```json
{
  "title": "Visit Brother Smith",
  "lat": 40.567,
  "lng": -111.123
}
```

---

## Example Code – Backend (Node.js + Express)

```javascript
const express = require('express');
const app = express();
app.use(express.json());

let tasks = [];

app.get('/api/tasks', (req, res) => res.json(tasks));

app.post('/api/tasks', (req, res) => {
  const newTask = { id: Date.now(), ...req.body };
  tasks.push(newTask);
  res.status(201).json(newTask);
});

app.listen(3000, () => console.log('Backend running on port 3000'));
```

---

## Example Code – Frontend (Kotlin – API Call)

```kotlin
fun fetchTasks() {
    val url = "http://10.0.2.2:3000/api/tasks" // Use 10.0.2.2 for Android emulator
    val request = Request.Builder().url(url).build()
    val client = OkHttpClient()

    client.newCall(request).enqueue(object : Callback {
        override fun onFailure(call: Call, e: IOException) { e.printStackTrace() }
        override fun onResponse(call: Call, response: Response) {
            val json = response.body?.string()
            println(json)
        }
    })
}
```

---

## Learning Resources

**Frontend (Android Studio)**

* [Android Studio Beginner Tutorial](https://www.youtube.com/watch?v=fis26HvvDII)
* [Kotlin for Android Developers](https://developer.android.com/kotlin)

**Backend (VS Code)**

* [Node.js + Express Crash Course](https://www.youtube.com/watch?v=L72fhGm1tfE)
* [MongoDB Atlas Tutorial](https://www.youtube.com/watch?v=Of1phRTfp48)

**Maps (OSM)**

* [Leaflet.js Tutorial](https://www.youtube.com/watch?v=7ZLk3TBrk9I)
* [MapLibre SDK Android](https://maplibre.org/)

---

If you want, I can now make you **matching GitHub repos with the correct folder structure and placeholder code** so your team can start working immediately. That will save time and make onboarding painless.
