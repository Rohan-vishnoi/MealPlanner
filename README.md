# 🍽️ Meal Planner App

A modern, native Android application for planning daily, weekly, and monthly meals. Built with a Kotlin-first approach, leveraging Jetpack Compose for the UI and Room for local database management.

## 🚀 Tech Stack
- **Language:** Kotlin
- **UI Toolkit:** Jetpack Compose
- **Local Storage:** Room Database (SQLite)
- **Architecture:** MVVM (Model-View-ViewModel) + Repository Pattern
- **Asynchronous Programming:** Kotlin Coroutines & Flow

## 🏗️ Architecture (High-Level Design)
The application follows Google's recommended architecture for modern Android development, ensuring a unidirectional data flow and clean separation of concerns.

**Data Flow:**
`UI Layer (Jetpack Compose) ↔ ViewModel ↔ Repository ↔ Data Layer (Room DB)`

1. **UI Layer (View):** Displays data and captures user interactions. Completely unaware of database logic.
2. **ViewModel:** Holds the UI state and processes user intents.
3. **Repository:** The single source of truth for the application's data.
4. **Data Layer (Room):** Local SQLite database handling data persistence.

## 🗄️ Database Schema (Low-Level Design)
The app uses a relational structure to easily support default and custom meal types.

### 1. `MealType` (Entity / Table)
Stores the categories of meals (e.g., Breakfast, Lunch, Dinner, Evening Snack).
- `id`: Int (Primary Key, Auto-generated)
- `name`: String

### 2. `PlannedMeal` (Entity / Table)
Stores the actual food entries linked to a specific date and meal type.
- `id`: Int (Primary Key, Auto-generated)
- `date`: String (Format: "YYYY-MM-DD")
- `mealTypeId`: Int (Foreign Key linking to `MealType`)
- `foodDescription`: String (e.g., "Oatmeal and berries")

## 📁 Project Structure
```
com.yourname.mealplanner
│
├── data/                  # Data Layer
│   ├── local/             # Room Database components
│   │   ├── entities/      # MealType.kt, PlannedMeal.kt
│   │   ├── dao/           # MealDao.kt
│   │   └── AppDatabase.kt
│   └── repository/        # MealRepository.kt
│
├── ui/                    # UI Layer
│   ├── viewmodel/         # PlannerViewModel.kt
│   └── screens/           # Jetpack Compose UI
│       ├── DailyPlannerScreen.kt
│       └── components/    # MealCard.kt, AddMealDialog.kt
│
└── MainActivity.kt        # Entry point
```

## 🛠️ Prerequisites & Testing
- **IDE:** Android Studio (Latest stable version recommended).
- **Physical Device Testing:** Testing on a physical device like the **Pixel 9 Pro XL** is highly recommended to accurately gauge real-world performance, scrolling smoothness with Jetpack Compose, and touch interactions.
  - *To enable physical testing:* Go to your Pixel's `Settings > About phone` > Tap `Build number` 7 times. Go to `Developer Options` > Enable `USB debugging` or `Wireless debugging`.

## 🚀 Getting Started
1. Clone this repository.
2. Open the project in Android Studio.
3. Sync Gradle files.
4. Run the app on your connected Pixel device or emulator.
