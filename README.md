<p align="center">
  <img src="https://raw.githubusercontent.com/felangel/bloc/master/docs/assets/flutter_weather_logo.png" alt="Flutter Weather" width="120" />
</p>

<h1 align="center">🌦️ Flutter Weather</h1>

<p align="center">
  <em>A production-quality weather app built with Flutter & the BLoC pattern, following the official <a href="https://bloclibrary.dev/tutorials/flutter-weather/">BLoC Library tutorial</a>.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter" alt="Flutter" />
  <img src="https://img.shields.io/badge/Dart-3.x-0175C2?logo=dart" alt="Dart" />
  <img src="https://img.shields.io/badge/State%20Management-BLoC%20/%20Cubit-6C63FF" alt="BLoC" />
  <img src="https://img.shields.io/badge/API-Open--Meteo-orange" alt="Open-Meteo" />
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License" />
</p>

---

## 📖 About

This project is a fully functional weather application that pulls **live weather data** from the [Open-Meteo API](https://open-meteo.com/) (no API key required). It demonstrates best practices for building Flutter apps with clean architecture and the BLoC/Cubit state management pattern.

**Based on:** [bloclibrary.dev/tutorials/flutter-weather](https://bloclibrary.dev/tutorials/flutter-weather/)

### What This App Does

- Search any city and get real-time weather conditions
- Displays temperature, weather condition (sunny, cloudy, rainy, snowy)
- Toggle between **Celsius** and **Fahrenheit**
- **Dynamic theming** — app color changes based on weather condition
- **Pull-to-refresh** to update weather data
- **Persists state** across app restarts using HydratedBloc

---

## 🏗️ Architecture

The app follows a **layered architecture** to cleanly separate concerns:

```
┌─────────────────────────────────────────────┐
│              Presentation Layer              │
│         (Widgets, Pages, UI Logic)           │
├─────────────────────────────────────────────┤
│            Business Logic Layer              │
│          (WeatherCubit, ThemeCubit)          │
├─────────────────────────────────────────────┤
│              Repository Layer                │
│           (WeatherRepository)                │
├─────────────────────────────────────────────┤
│                Data Layer                    │
│       (OpenMeteoApiClient — HTTP calls)      │
└─────────────────────────────────────────────┘
```

### Project Structure

```
flutter_weather/
├── lib/
│   ├── app.dart                    # Root app widget, theme setup
│   ├── main.dart                   # Entry point, HydratedBloc init
│   ├── weather/
│   │   ├── cubit/
│   │   │   ├── weather_cubit.dart  # Business logic for fetching weather
│   │   │   └── weather_state.dart  # Weather states (loading, success, failure)
│   │   ├── models/
│   │   │   └── weather.dart        # UI-layer weather model (w/ JSON serialization)
│   │   ├── view/
│   │   │   ├── weather_page.dart   # Main page with BlocProvider setup
│   │   │   └── weather_view.dart   # UI rendering based on state
│   │   └── widgets/
│   │       ├── weather_empty.dart
│   │       ├── weather_error.dart
│   │       ├── weather_loading.dart
│   │       └── weather_populated.dart
│   ├── search/
│   │   └── view/
│   │       └── search_page.dart    # City search screen
│   ├── settings/
│   │   └── view/
│   │       └── settings_page.dart  # Celsius/Fahrenheit toggle
│   └── theme/
│       └── cubit/
│           └── theme_cubit.dart    # Dynamic theme based on weather
│
├── packages/
│   ├── open_meteo_api/             # API client package (data layer)
│   │   ├── lib/
│   │   │   └── src/
│   │   │       ├── models/
│   │   │       │   ├── location.dart
│   │   │       │   └── weather.dart
│   │   │       └── open_meteo_api_client.dart
│   │   └── test/
│   │
│   └── weather_repository/         # Repository package (abstraction layer)
│       ├── lib/
│       │   └── src/
│       │       ├── models/
│       │       │   └── weather.dart
│       │       └── weather_repository.dart
│       └── test/
│
├── pubspec.yaml
└── README.md
```

---

## 🧩 Key Concepts & Packages

| Package | Purpose |
|---------|---------|
| `flutter_bloc` | State management — provides `BlocProvider`, `BlocBuilder`, `BlocListener` |
| `hydrated_bloc` | Persists cubit/bloc state across app restarts automatically |
| `equatable` | Value equality for state classes (prevents unnecessary rebuilds) |
| `json_annotation` + `json_serializable` | JSON serialization for weather models |
| `build_runner` | Code generation for JSON serialization |
| `http` | HTTP client for API requests |
| `mocktail` | Mocking library for unit tests |
| `bloc_test` | Testing utilities for blocs/cubits |

---

## ⚙️ How It Works

### 1. Data Layer — `open_meteo_api`

A standalone Dart package that handles all HTTP communication with the Open-Meteo API. It fetches geocoding data (city → latitude/longitude) and current weather conditions. This package knows nothing about Flutter or BLoC — it's pure Dart.

### 2. Repository Layer — `weather_repository`

Wraps the API client and exposes a clean, simplified interface. The repository transforms raw API models into domain models the app cares about. If you ever switch to a different weather API, you only change this layer.

### 3. Business Logic Layer — `WeatherCubit`

Consumes the repository, manages app state (initial, loading, success, failure), and exposes a `fetchWeather(city)` method. Uses `HydratedCubit` so weather data survives app restarts.

### 4. Presentation Layer — Widgets

Reacts to cubit state changes using `BlocBuilder`. Different widgets render for each state: empty, loading, populated, or error. A `ThemeCubit` listens to weather changes and dynamically updates the app's color scheme.

---

## 🚀 Getting Started

### Prerequisites

- [Flutter SDK](https://flutter.dev/docs/get-started/install) (3.x or later)
- Dart SDK (3.x or later)
- An editor — [VS Code](https://code.visualstudio.com/) with the [Bloc extension](https://marketplace.visualstudio.com/items?itemName=FelixAngelov.bloc) recommended

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/flutter_weather.git
cd flutter_weather

# 2. Install dependencies
flutter pub get

# 3. Generate serialization code
dart run build_runner build --delete-conflicting-outputs

# 4. Run the app
flutter run
```

> **No API key needed!** The Open-Meteo API is completely free and requires no authentication.

---

## 🧪 Running Tests

The project includes unit tests for all three layers:

```bash
# Run all tests
flutter test

# Run tests for a specific package
cd packages/open_meteo_api && dart test
cd packages/weather_repository && dart test

# Run with coverage
flutter test --coverage
```

---

## 🎨 Features Breakdown

### Dynamic Theming
The app color scheme changes in real-time based on the current weather condition — warm colors for sunny, cool blues for rainy, gray for cloudy, and icy tones for snow.

### State Persistence (HydratedBloc)
Weather data and user preferences (temperature units) are stored locally. Closing and reopening the app restores the last known state instantly — no loading spinner on restart.

### Pull-to-Refresh
Swipe down on the weather screen to refresh data. The cubit re-fetches from the API and the UI updates reactively.

### Unit Conversion
Toggle between Celsius and Fahrenheit in the settings page. The conversion happens in the presentation layer while the cubit always stores raw data.

---

## 🌐 API Reference

This app uses the [Open-Meteo API](https://open-meteo.com/en/docs):

| Endpoint | Purpose |
|----------|---------|
| `geocoding-api.open-meteo.com/v1/search?name={city}` | City name → coordinates |
| `api.open-meteo.com/v1/forecast?latitude={lat}&longitude={lon}&current_weather=true` | Coordinates → weather |

**Response example:**
```json
{
  "current_weather": {
    "temperature": 22.3,
    "weathercode": 0,
    "windspeed": 8.1,
    "winddirection": 180
  }
}
```

Weather codes map to conditions: `0` = Clear, `1-3` = Cloudy, `51-67` = Rainy, `71-77` = Snowy, etc.

---

## 📱 Screenshots

<!-- Replace these with your actual screenshots -->
| Search | Weather | Settings |
|--------|---------|----------|
| ![Search](screenshots/search.png) | ![Weather](screenshots/weather.png) | ![Settings](screenshots/settings.png) |

---

## 🤝 Contributing

Contributions are welcome! If you find a bug or want to add a feature:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📚 Learn More

- [BLoC Library Documentation](https://bloclibrary.dev/)
- [Original Tutorial](https://bloclibrary.dev/tutorials/flutter-weather/)
- [Flutter Official Docs](https://flutter.dev/docs)
- [Open-Meteo API Docs](https://open-meteo.com/en/docs)

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

<p align="center">
  Built with ❤️ using <a href="https://flutter.dev">Flutter</a> & <a href="https://bloclibrary.dev">BLoC</a>
</p>
