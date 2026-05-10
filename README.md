# 🍽️ Meal Planner App with AI Assistant

A modern, native Android application for planning daily, weekly, and monthly meals. It features an integrated AI Assistant powered by the Gemini LLM to review daily meal plans, estimate caloric intake, and provide healthy suggestions.

## 🚀 Tech Stack
- **Language:** Kotlin
- **UI Toolkit:** Jetpack Compose
- **Local Storage:** Room Database (SQLite)
- **Remote API / AI:** Google AI Client SDK for Android (Gemini API)
- **Architecture:** MVVM (Model-View-ViewModel) + Repository Pattern
- **Asynchronous Programming:** Kotlin Coroutines & Flow

## 🏗️ Architecture (High-Level Design)
The application follows Google's recommended architecture for modern Android development. With the addition of the AI Assistant, the Data Layer now handles both local persistence and remote API calls.

**Data Flow:**
`UI Layer (Jetpack Compose) ↔ ViewModel ↔ Repository ↔ [Local Data (Room) & Remote AI (Gemini)]`

1. **UI Layer (View):** Displays data, captures user inputs, and shows AI feedback.
2. **ViewModel:** Holds the UI state (including AI loading/error states) and processes user intents (e.g., clicking "Review My Plan").
3. **Repository:** The single source of truth. It fetches saved meals from the local database, formats them into a prompt, and queries the Gemini API for a review.
4. **Data Layer:**
    - **Local Source:** Room SQLite database handling data persistence.
    - **Remote Source:** Network client communicating with the Gemini LLM.

## 🤖 AI Assistant Integration Workflow
1. The user adds their meals for the day (e.g., Breakfast, Lunch, Dinner).
2. The user taps a "Review Plan with AI" button.
3. The `PlannerViewModel` asks the `MealRepository` for an analysis.
4. The `MealRepository` retrieves the day's `PlannedMeal` list from the `MealDao`.
5. The repository constructs a prompt containing the meals and asks Gemini to estimate calories, check for limits, and suggest healthier alternatives if needed.
6. The `GeminiApiService` executes the API call.
7. The response is passed back up to the UI and displayed in an `AIReviewCard`.

## 🗄️ Database Schema & Data Models (Low-Level Design)

### 1. Local Database Entities (Room)
**`MealType` (Table)**
- `id`: Int (Primary Key)
- `name`: String (e.g., "Breakfast", "Lunch")

**`PlannedMeal` (Table)**
- `id`: Int (Primary Key)
- `date`: String (Format: "YYYY-MM-DD")
- `mealTypeId`: Int (Foreign Key linking to `MealType`)
- `foodDescription`: String (e.g., "Oatmeal and berries")

### 2. Network Models (API)
**`AIReviewResponse` (Data Class)**
- `estimatedCalories`: Int
- `isOverLimit`: Boolean
- `suggestions`: List<String>
- `feedbackText`: String

## 📁 Project Structure
```
com.yourname.mealplanner
│
├── data/                  # Data Layer
│   ├── local/             # Local Database components
│   │   ├── entities/      # MealType.kt, PlannedMeal.kt
│   │   ├── dao/           # MealDao.kt
│   │   └── AppDatabase.kt
│   ├── network/           # Remote API components (NEW)
│   │   ├── GeminiApiService.kt  # Handles Gemini API requests
│   │   └── models/        # AIReviewResponse.kt
│   └── repository/        # MealRepository.kt (Coordinates Room + Gemini)
│
├── ui/                    # UI Layer
│   ├── viewmodel/         # PlannerViewModel.kt (Handles AI states)
│   └── screens/           # Jetpack Compose UI
│       ├── DailyPlannerScreen.kt
│       └── components/    
│           ├── MealCard.kt
│           ├── AddMealDialog.kt
│           └── AIReviewCard.kt  # Displays AI calorie warnings/suggestions (NEW)
│
└── MainActivity.kt        # Entry point
```

## 🛠️ Prerequisites & Testing
- **IDE:** Android Studio (Latest stable version recommended).
- **Physical Device Testing:** Testing on a physical device like the **Pixel 9 Pro XL** is highly recommended to accurately gauge real-world performance, scrolling smoothness with Jetpack Compose, and touch interactions.
- **Gemini API Key:** You will need to generate an API key from Google AI Studio and store it securely in your `local.properties` file.


## ☁️ Cloud Infrastructure (Serverless)
To minimize costs and secure API keys, the AI logic is offloaded to Google Cloud:

- **Compute:** Google Cloud Run (Dockerized Kotlin/JVM service).
- **Scaling:** Configured to `min-instances 0` to ensure charges only occur during active requests.
- **Security:** Gemini API keys are managed via Secret Manager, not stored on the mobile device.
- **Connectivity:** Android app communicates with the backend via HTTPS/REST[cite: 2].

## 🚀 Getting Started
1. Clone this repository.
2. Add your Gemini API key to `local.properties`: `GEMINI_API_KEY=your_key_here`
3. Open the project in Android Studio.
4. Sync Gradle files.
5. Run the app on your connected Pixel device or emulator.
