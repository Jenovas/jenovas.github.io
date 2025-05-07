---
layout: post
title: "How to Manage State Effectively in Jetpack Compose"
date: 2025-04-22 23:52:00 +0200
categories: [tutorials, android-development]
tags: ["Android", "Kotlin", "Jetpack Compose", "State Management", "MVI"]
---

# State Management Best Practices in Jetpack Compose

Managing state properly is one of the biggest challenges in any Android application, especially when using Jetpack Compose. In this post, we'll explore a setup that keeps your Compose apps clean, manageable, and easy to maintain.

<!--more-->

---

## Why Good State Management Matters

Jetpack Compose makes UI development more straightforward, but without a solid strategy your composables can become messy and hard to follow. With effective state management, your app will be:

- **Easier to understand & maintain**  
- **Simpler to debug & test**  
- **More scalable** as new features are added  

Below is a step-by-step guide to structuring state in your Compose screens.

---

## Recommended Practices

### 1. Keep ViewModels at the Top Level

Place your ViewModel only at the top-level composable for each screen. This centralizes business logic and keeps your UI code simple. It also makes it easy to create `@Preview` functions using fake states.

```kotlin
@Composable
fun MyScreen() {
    val screenModel = getScreenModel<CleanSetupScreenModel>()
    CleanSetupScreenContent(
        state = screenModel.state.collectAsStateWithLifecycle().value,
        event = screenModel::handleEvent
    )
}
```

### 2. Use a Single `StateFlow` for Screen State

Rather than juggling multiple state holders, expose **one** `StateFlow` that represents the entire screen's UI state. A sealed class helps manage all possible states:

```kotlin
sealed class CleanSetupScreenState {
    object Loading : CleanSetupScreenState()
    data class Error(val message: String) : CleanSetupScreenState()
    data class Success(
        val someIntValue: Int,
        val someStringValue: String,
        val data: CleanSetupDataClass
    ) : CleanSetupScreenState()
}
```

> **Benefit:** Your composable only needs a single `when` block to render every state.

### 3. Handle Events with One Callback

Avoid passing dozens of lambdas into your UI. Instead, define a single event callback on your ViewModel:

```kotlin
sealed class CleanSetupScreenEvent {
    object Refresh : CleanSetupScreenEvent()
    data class ToggleFavourite(val item: CleanSetupDataClass) : CleanSetupScreenEvent()
    object NavigateBack : CleanSetupScreenEvent()
    object NavigateToSettings : CleanSetupScreenEvent()
}

@Composable
fun CleanSetupScreenContent(
    state: CleanSetupScreenState,
    event: (CleanSetupScreenEvent) -> Unit
) {
    // UI here...
}
```

Call it like:

```kotlin
CleanSetupScreenContent(
    state = state,
    event = screenModel::handleEvent
)
```

### 4. Use Channels for One-Time Events

For navigation, snackbars, or other single-shot effects, channels are perfect:

```kotlin
class CleanSetupViewModel : ViewModel() {
    private val _effects = Channel<UiEffect>()
    val effects = _effects.receiveAsFlow()

    fun handleEvent(event: CleanSetupScreenEvent) {
        when (event) {
            CleanSetupScreenEvent.NavigateBack ->
                viewModelScope.launch { _effects.send(UiEffect.NavigateBack) }
            // …
        }
    }
}
```

Collect and act on them in your composable:

```kotlin
@Composable
fun ObserveEffects(effects: Flow<UiEffect>, navigator: Navigator) {
    LaunchedEffect(effects) {
        effects.collect { effect ->
            when (effect) {
                is UiEffect.NavigateBack -> navigator.pop()
                is UiEffect.NavigateToSettings -> navigator.push(SettingsScreen())
            }
        }
    }
}
```

### 5. Lifecycle-Aware State Collection

Prevent leaks and avoid stale UI by using lifecycle–aware collection:

```kotlin
val state by screenModel.state.collectAsStateWithLifecycle()
```

This automatically ties collection to the composable's lifecycle.

---

## Putting It All Together

Here's a concise example showing these practices in action:

```kotlin
@Composable
fun CleanSetupScreenContent(
    state: CleanSetupScreenState,
    event: (CleanSetupScreenEvent) -> Unit
) {
    Scaffold(
        topBar    = { CleanSetupTopBar() },
        bottomBar = { CleanSetupBottomBar(event) }
    ) { padding ->
        Box(
            modifier = Modifier
                .fillMaxSize()
                .padding(padding)
        ) {
            when (state) {
                is CleanSetupScreenState.Loading ->
                    CleanSetupLoadingIndicator(Modifier.align(Alignment.Center))

                is CleanSetupScreenState.Error ->
                    CleanSetupError(state.message, Modifier.align(Alignment.Center))

                is CleanSetupScreenState.Success ->
                    CleanSetupContent(state, event)
            }
        }
    }
}
```

---

## Why This Works

- **Debugging** becomes straightforward  
- **UI code** stays clean and focused  
- **Testing** is much easier  
- **Scalability**: you can add new states or events without rewriting every composable  

---

## Final Thoughts

Good state management in Jetpack Compose doesn't have to be complicated. By:

1. Centralizing ViewModels  
2. Using a single `StateFlow`  
3. Handling events via a sealed callback  
4. Leveraging channels for one-time effects  
5. Collecting state lifecycle-aware  

…you'll find your Compose apps more robust, maintainable, and enjoyable to develop.

> Give it a try in your next project and experience the difference!



## Full Code

Screen
```kotlin
import algo.crafter.ui.screens.settings.SettingsScreen
import algo.crafter.ui.theme.AlgoCrafterSimpleTheme
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Button
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.Icon
import androidx.compose.material3.LargeTopAppBar
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TopAppBarDefaults
import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.getValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.compose.LocalLifecycleOwner
import androidx.lifecycle.compose.collectAsStateWithLifecycle
import androidx.lifecycle.repeatOnLifecycle
import cafe.adriel.voyager.core.screen.Screen
import cafe.adriel.voyager.koin.getScreenModel
import cafe.adriel.voyager.navigator.LocalNavigator
import cafe.adriel.voyager.navigator.currentOrThrow
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.withContext

class CleanSetupScreen : Screen {
    @Composable
    override fun Content() {
        val navigator = LocalNavigator.currentOrThrow
        val screenModel = getScreenModel<CleanSetupScreenModel>()
        val state by screenModel.state.collectAsStateWithLifecycle()
        val effects = screenModel.effects

        ObserveAsEvents(effects) {
            when (it) {
                is UiEffect.NavigateBack -> navigator.pop()
                is UiEffect.NavigateToSettings -> navigator.push(SettingsScreen())
            }
        }

        CleanSetupScreenContent(
            state,
            screenModel::handleEvent
        )
    }

    @Composable
    private fun CleanSetupScreenContent(
        state: CleanSetupScreenState,
        event: (CleanSetupScreenEvent) -> Unit
    ) {
        Scaffold(
            topBar = {
                CleanSetupTopBar()
            },
            bottomBar = {
                CleanSetupBottomBar(
                    event
                )
            }
        ) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(it)
            ) {
                when (state) {
                    is CleanSetupScreenState.Error -> {
                        CleanSetupError(
                            error = state.error,
                            modifier = Modifier.align(Alignment.Center)
                        )
                    }

                    is CleanSetupScreenState.Loading -> {
                        CleanSetupLoadingIndicator(
                            modifier = Modifier.align(Alignment.Center)
                        )
                    }

                    is CleanSetupScreenState.Success -> {
                        CleanSetupContent(
                            state,
                            event
                        )
                    }
                }
            }
        }
    }

    @OptIn(ExperimentalMaterial3Api::class)
    @Composable
    private fun CleanSetupContent(
        state: CleanSetupScreenState.Success,
        event: (CleanSetupScreenEvent) -> Unit
    ) {
        Column(
            modifier = Modifier
                .fillMaxSize(),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Text(
                text = "${state.someIntValue}",
                style = MaterialTheme.typography.headlineLarge,
                color = MaterialTheme.colorScheme.onSurface
            )

            Spacer(modifier = Modifier.height(16.dp))

            Text(
                text = state.someStringValue,
                style = MaterialTheme.typography.displaySmall,
                color = MaterialTheme.colorScheme.onSurface
            )

            Spacer(modifier = Modifier.height(16.dp))

            Text(
                text = "${state.cleanSetupDataClass.dataClassInt}",
                style = MaterialTheme.typography.titleLarge,
                color = MaterialTheme.colorScheme.onSurface
            )

            Spacer(modifier = Modifier.height(16.dp))

            Text(
                text = state.cleanSetupDataClass.dataClassString,
                style = MaterialTheme.typography.titleLarge,
                color = MaterialTheme.colorScheme.onSurface
            )

            Spacer(modifier = Modifier.height(16.dp))

            val tint =
                if (state.cleanSetupDataClass.isFavourite) MaterialTheme.colorScheme.primary else MaterialTheme.colorScheme.onSurface
            Icon(
                imageVector = Icons.Default.Star,
                contentDescription = "Star",
                modifier = Modifier
                    .size(48.dp)
                    .padding(16.dp)
                    .clickable {
                        event(
                            CleanSetupScreenEvent.ToggleFavourite(
                                state.cleanSetupDataClass
                            )
                        )
                    },
                tint = tint
            )
        }
    }

    @Composable
    private fun CleanSetupBottomBar(event: (CleanSetupScreenEvent) -> Unit) {
        Button(
            onClick = { event(CleanSetupScreenEvent.NavigateToSettings) },
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp)
        ) {
            Text(
                text = "Continue",
                style = MaterialTheme.typography.titleLarge,
                color = MaterialTheme.colorScheme.onPrimary
            )
        }
    }

    @OptIn(ExperimentalMaterial3Api::class)
    @Composable
    private fun CleanSetupTopBar() {
        LargeTopAppBar(
            title = { Text("Clean Setup") },
            colors = TopAppBarDefaults.topAppBarColors(
                containerColor = MaterialTheme.colorScheme.primary,
                titleContentColor = MaterialTheme.colorScheme.onPrimary,
            )
        )
    }

    @Composable
    private fun CleanSetupError(error: String, modifier: Modifier) {
        Text(
            text = error,
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.error,
            modifier = modifier
        )
    }

    @Composable
    private fun CleanSetupLoadingIndicator(modifier: Modifier) {
        CircularProgressIndicator(
            modifier = modifier,
            color = MaterialTheme.colorScheme.primary,
            strokeWidth = 4.dp
        )
    }

    @Preview()
    @Composable
    fun CleanSetupScreenPreview() {
        AlgoCrafterSimpleTheme {
            CleanSetupScreenContent(
                CleanSetupScreenState.Loading
            ) {}
            CleanSetupScreenContent(
                CleanSetupScreenState.Error("Error loading data")
            ) {}
            CleanSetupScreenContent(
                CleanSetupScreenState.Success(
                    12345,
                    "RandomString",
                    CleanSetupDataClass(111, "ImportantString", false)
                )
            ) {}
        }
    }
}

@Composable
private fun <T> ObserveAsEvents(flow: Flow<T>, onEvent: (T) -> Unit) {
    val lifecycleOwner = LocalLifecycleOwner.current
    LaunchedEffect(flow, lifecycleOwner.lifecycle) {
        lifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
            withContext(Dispatchers.Main.immediate) {
                flow.collect(onEvent)
            }
        }
    }
}
```

ViewModel
```kotlin
class CleanSetupScreenModel() : ScreenModel, KoinComponent {
    private val _state = MutableStateFlow<CleanSetupScreenState>(CleanSetupScreenState.Loading)
    val state: StateFlow<CleanSetupScreenState> = _state.asStateFlow()

    private val _effects = Channel<UiEffect>()
    val effects = _effects.receiveAsFlow()

    init {
        refreshData()
    }

    fun handleEvent(event: CleanSetupScreenEvent) {
        when (event) {
            is CleanSetupScreenEvent.Refresh -> refreshData()
            is CleanSetupScreenEvent.ToggleFavourite -> {
                handeFavouriteEvent(event.clickedDataClass)
            }

            is CleanSetupScreenEvent.NavigateBack -> {
                screenModelScope.launch { _effects.send(UiEffect.NavigateBack) }
            }

            is CleanSetupScreenEvent.NavigateToSettings -> {
                screenModelScope.launch { _effects.send(UiEffect.NavigateToSettings) }
            }
        }
    }

    private fun refreshData() {
        screenModelScope.launch {
            // Simulate loading data
            _state.value = CleanSetupScreenState.Loading
            try {
                _state.value = CleanSetupScreenState.Success(
                    12345,
                    "RandomString",
                    CleanSetupDataClass(111, "ImportantString", false)
                )
            } catch (e: Exception) {
                _state.value = CleanSetupScreenState.Error(e.message.orEmpty())
            }
        }
    }

    private fun handeFavouriteEvent(
        clickedDataClass: CleanSetupDataClass
    ) {
        val favouriteClass = clickedDataClass.copy(isFavourite = true)
        val updatedState =
            (_state.value as? CleanSetupScreenState.Success)?.copy(cleanSetupDataClass = favouriteClass)
                ?: CleanSetupScreenState.Success(12345, "RandomString", favouriteClass)

        _state.value = updatedState
    }
}

sealed class CleanSetupScreenState {
    object Loading : CleanSetupScreenState()

    data class Error(
        val error: String
    ) : CleanSetupScreenState()

    data class Success(
        val someIntValue: Int,
        val someStringValue: String,
        val cleanSetupDataClass: CleanSetupDataClass
    ) : CleanSetupScreenState()
}

data class CleanSetupDataClass(
    val dataClassInt: Int,
    val dataClassString: String,
    val isFavourite: Boolean
)

sealed class CleanSetupScreenEvent {
    object Refresh : CleanSetupScreenEvent()
    data class ToggleFavourite(val clickedDataClass: CleanSetupDataClass) : CleanSetupScreenEvent()
    object NavigateBack : CleanSetupScreenEvent()
    object NavigateToSettings : CleanSetupScreenEvent()
}

sealed class UiEffect {
    object NavigateBack : UiEffect()
    object NavigateToSettings : UiEffect()
}
```
