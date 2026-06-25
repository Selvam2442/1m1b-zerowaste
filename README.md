# 🍃 Zero-Waste Portion Master: Comprehensive Documentation

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)
![PostgreSQL](https://img.shields.io/badge/postgresql-4169e1?style=for-the-badge&logo=postgresql&logoColor=white)
![Gemini AI](https://img.shields.io/badge/Google%20Gemini-8E75B2?style=for-the-badge&logo=google&logoColor=white)
![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)
![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)

**Zero-Waste Portion Master** is an intelligent, full-stack pantry management and recipe generation application. It bridges the gap between what you have in your kitchen and what you can actually cook, drastically reducing food waste. By leveraging the power of Google's Gemini AI, robust PostgreSQL data management, and a lightning-fast FastAPI backend, it acts as your personal, highly-customized digital sous-chef.

---

## 📖 Table of Contents
1. [About The Project](#-about-the-project)
2. [Deep-Dive into Features](#-deep-dive-into-features)
3. [System Architecture & Logic](#-system-architecture--logic)
4. [Database Schema](#-database-schema)
5. [API Documentation](#-api-documentation)
6. [File Structure Breakdown](#-file-structure-breakdown)
7. [Installation & Setup Guide](#-installation--setup-guide)
8. [Roadmap & Future Upgrades](#-roadmap--future-upgrades)

---

## 🌍 About The Project

Food waste is a massive global issue, often starting at home when ingredients are forgotten in the back of the fridge or when we lack the inspiration to combine random pantry items into a cohesive meal. 

This application solves that exact problem. It allows users to digitize their pantry, set strict dietary profiles, and dynamically calculate ingredient scaling. The core engine is an AI wrapper that reads the exact inventory state and crafts a custom recipe—eliminating waste while catering to the user's cultural and dietary needs.

---

## ✨ Deep-Dive into Features

### 1. 🧠 Context-Aware AI "Scrap-to-Meal" Chef
Unlike standard recipe generators, this AI is heavily constrained and context-aware.
* **How it works:** When a user selects checkboxes of current pantry items, the frontend sends the IDs to the backend. The backend queries the database for the exact quantities and units of those items, grabs the user's saved preferences, and injects them into a highly specific prompt.
* **Translation & Localization:** The AI is strictly instructed to return the recipe in the user's preferred language (e.g., Tamil, Hindi, English).
* **Diet & Spice Compliance:** The prompt enforces dietary rules (e.g., Vegan, Gluten-Free) and heat tolerance (Mild, Medium, Spicy) before sending the request to `gemini-2.5-flash`.

### 2. 📦 Dynamic Pantry Inventory
A real-time database interface for managing household food.
* **Validation:** Prevents saving empty or malformed data using FastAPI's Pydantic schemas.
* **Display:** Renders a clean, Bootstrap-style list of ingredients directly beneath the input form for instant feedback.

### 3. ⚙️ "Self-Healing" User Management
To ensure a frictionless user experience, the application implements background error correction.
* **How it works:** If a user tries to save an item to their pantry before explicitly creating a profile, the database would normally crash (Foreign Key constraint violation). The FastAPI backend intercepts this, quietly builds a default "User #1" profile in the background, and successfully saves the item without throwing an error to the user.

### 4. 📱 Mobile-First Tabbed UI
The frontend is built without heavy frameworks like React or Angular, utilizing highly optimized Vanilla JavaScript and modern CSS variables.
* **Modal Interactivity:** Uses hidden overlays and CSS animations (`@keyframes`) to create native-feeling popup modals for settings.
* **Custom Toast Alerts:** Replaces clunky browser `alert()` popups with custom, auto-fading notification toasts for success and error states.

---

## 🏗️ System Architecture & Logic

The application follows a standard **Client-Server-Database** architecture.

1. **The Client (Browser):** The user interacts with `index.html`. Using the JavaScript `fetch()` API, it sends JSON payloads to the backend.
2. **The Server (FastAPI):** `main.py` receives the request. It uses SQLAlchemy to open a session with the database.
3. **The Database (PostgreSQL):** Stores or retrieves the requested data.
4. **The AI Layer (Google GenAI):** If a recipe is requested, the Server compiles the database info, makes an external HTTPS call to Google's servers, waits for the generated text, formats it, and returns it to the Client.

---

## 🗄️ Database Schema

The PostgreSQL database utilizes relational tables defined in `models.py`.

### `users` Table
| Column Name | Type | Description |
| :--- | :--- | :--- |
| `user_id` | Integer (PK) | Primary Key. |
| `name` | String | User's display name. |
| `email` | String | User's contact/login email. |
| `password_hash`| String | Encrypted password. |
| `language` | String | Preferred recipe language (Default: English). |
| `diet` | String | Dietary restrictions (Default: Anything). |
| `spice_level` | String | Heat tolerance (Default: Medium). |

### `pantry` Table
| Column Name | Type | Description |
| :--- | :--- | :--- |
| `item_id` | Integer (PK) | Primary Key. |
| `user_id` | Integer (FK) | Links the item to a specific user. |
| `ingredient_name`| String | The name of the food (e.g., Rice). |
| `quantity` | Float | The amount available (e.g., 2.5). |
| `unit` | String | The measurement (e.g., kg, lbs, cups). |
| `expiration_date`| Date | When the food expires. |

---

## 🔌 API Documentation

The FastAPI backend automatically generates interactive Swagger documentation. Once the server is running, visit `http://127.0.0.1:8000/docs`.

### Core Endpoints
* `POST /update-profile`: Accepts JSON to update user language, diet, and spice preferences. Creates the user if they do not exist.
* `GET /profile/{user_id}`: Fetches the current settings for the settings modal.
* `POST /pantry`: Adds a new ingredient to the database. Contains self-healing logic for missing users.
* `GET /pantry`: Retrieves the full list of saved ingredients.
* `POST /generate-recipe`: The AI engine. Accepts selected item IDs and extra text, compiles the AI prompt, and returns the markdown recipe.

---

## 📂 File Structure Breakdown

```text
zero-waste-master/
│
├── zero-waste-backend/
│   ├── database.py       # Handles the PostgreSQL connection engine and SessionLocal.
│   ├── models.py         # Defines the SQLAlchemy ORM tables (Users, Pantry).
│   └── main.py           # The FastAPI application, API endpoints, and AI logic.
│
└── zero-waste-frontend/
    └── index.html        # The complete UI containing all CSS styles and JavaScript fetch logic.
```

---

## 🚀 Installation & Setup Guide

Follow these steps to deploy the application locally on your machine.

### Phase 1: Prerequisites
1. Install **Python 3.8** or higher.
2. Install **PostgreSQL** and a management tool like **pgAdmin**.
3. Obtain a **Google Gemini API Key** from [Google AI Studio](https://aistudio.google.com/app/apikey).

### Phase 2: Database Setup
1. Open pgAdmin and connect to your local server.
2. Right-click "Databases" -> Create -> Database.
3. Name the database `zerowaste` and save.

### Phase 3: Backend Setup
1. Open your terminal and navigate to the project folder.
2. Create and activate a virtual environment:
   ```bash
   python -m venv .venv
   # Windows:
   .venv\Scripts\activate
   # macOS/Linux:
   source .venv/bin/activate
   ```
3. Install the required Python libraries:
   ```bash
   pip install fastapi uvicorn sqlalchemy psycopg2-binary pydantic google-genai
   ```
4. Configure `database.py`: Open the file and update the `DATABASE_URL` with your exact PostgreSQL username and password.
   * *Example:* `postgresql://postgres:YOUR_PASSWORD@localhost:5432/zerowaste`
5. Configure `main.py`: Paste your Gemini API key into the `genai.Client()` initializer.

### Phase 4: Booting the Application
1. In your terminal, navigate into the backend folder:
   ```bash
   cd zero-waste-backend
   ```
2. Start the Uvicorn server:
   ```bash
   uvicorn main:app --reload
   ```
3. Open the `index.html` file in any modern web browser (Chrome, Edge, Safari).

---
### 📱 Get the App
[**📥 Click Here to Download the Zero-Waste Portion Master APK**](Zero-Waste%20Portion%20Master.apk?raw=true)

## 🗺️ Roadmap & Future Upgrades

The architecture is designed to be highly scalable. Planned future implementations include:

- [ ] **Docker Containerization:** Packaging the FastAPI server, PostgreSQL database, and frontend into a single `docker-compose.yml` for instantaneous cross-platform deployment.
- [ ] **OCR Receipt Parsing:** Integrating Tesseract.js to allow users to snap a photo of their grocery receipt and automatically populate the `pantry` database.
- [ ] **Automated Expiry Alerts:** Implementing a Celery worker to scan the database daily and send push notifications for items expiring within 48 hours.
- [ ] **JWT Authentication:** Upgrading the self-healing User #1 logic to a full JWT (JSON Web Token) secure login system for multi-tenant household support.
- [ ] **DALL-E Integration:** Chaining a secondary AI prompt to generate a highly-styled photograph of the recipe generated by Gemini.

---
*Developed with Python, FastAPI, and Google Gemini.*
