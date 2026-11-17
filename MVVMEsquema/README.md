# Diari de Viatges / Crònica de Ruta

Aplicació Android amb **Jetpack Compose** i arquitectura **MVVM** per registrar rutes
o escapades, generar-ne la crònica visual i gestionar la persistència interna i externa.

## Arquitectura (MVVM)

```
View (Compose)  →  ViewModel (StateFlow)  →  Model (data classes immutables)
```

- **Model**: `RouteEntry` (data class immutable, @Serializable) i `Difficulty` (enum).
- **Estat**: `TravelDiaryUiState` — camps del formulari, errors de validació,
  històric de rutes i crònica actual. `isFormValid` és una propietat computada.
- **ViewModel**: `TravelDiaryViewModel` — `MutableStateFlow` privat exposat com a
  `StateFlow` amb `.asStateFlow()`; updates immutables amb `.copy()`; validació en
  temps real; mètodes per generar, desar i exportar cròniques.

## Estructura

```
app/src/main/java/com/example/diariviatges/
├── MainActivity.kt                  # crea el ViewModel (àmbit Activity)
├── model/
│   ├── RouteEntry.kt                # id, routeName, destination, difficulty,
│   │                                # estimatedBudget, date, isSolo, notes
│   └── Difficulty.kt                # FACIL / MODERADA / EXIGENT
├── ui/
│   ├── state/TravelDiaryUiState.kt
│   ├── viewmodel/TravelDiaryViewModel.kt
│   ├── theme/Theme.kt
│   ├── navigation/                  # Routes.kt + AppNavigation.kt (NavHost)
│   └── screens/
│       ├── FormScreen.kt            # formulari amb validació visual
│       ├── PostalScreen.kt          # targeta visual + exportar + compartir
│       └── HistoryScreen.kt         # històric de rutes desades
├── data/DiaryRepository.kt          # JSON a filesDir + exportació SAF
└── util/ChronicleFormatter.kt       # text Markdown de la crònica
```

## Requisits funcionals

1. **FormScreen** — Nom de ruta i destí (obligatoris, error si buits), dificultat amb
   `FilterChip`, pressupost amb `KeyboardType.Decimal`, data amb `DatePickerDialog`
   (Material 3), `Switch` "Ruta feta en solitari", notes multilínia i botó
   **"Generar Postal / Crònica"** desactivat si el formulari no és vàlid.
2. **Navegació** — Navigation Compose: `form → postal/{entryId}`. El ViewModel comparteix
   l'objecte complet; per navegació només es passa l'ID.
3. **Emmagatzematge intern** — Cada crònica es desa automàticament a
   `context.filesDir/diari_viatges.json` (kotlinx-serialization, `Dispatchers.IO`).
   En obrir l'app, el ViewModel llegeix el fitxer i mostra l'històric.
4. **Emmagatzematge extern** — A la postal, **"Exportar Crònica (.md / .txt)"** amb
   `ActivityResultContracts.CreateDocument("text/plain")` (SAF) i **"Compartir"** amb
   `Intent.ACTION_SEND` (WhatsApp, correu…).

## Requisits tècnics

- minSdk 26 · targetSdk 35 · Kotlin 2.0.21 · AGP 8.7.3 · Gradle 8.9
- Compose BOM 2024.12.01 · Material 3 · Navigation Compose · kotlinx-serialization

## Com obrir-lo

1. Obre la carpeta amb **Android Studio** (Ladybug o superior).
2. Accepta el sync de Gradle (descarregarà la distribució 8.9 i les dependències).
3. Executa la configuració `app` en un emulador o dispositiu amb API ≥ 26.
