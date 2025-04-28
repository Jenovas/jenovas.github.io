---
layout: post
title: "How to Manage State Effectively in Jetpack Compose"
date: 2025-04-22 23:52:00 +0200
categories: [tutorials, android-development]
tags: ["Android", "Kotlin", "Jetpack Compose", "State Management", "MVI"]
---

Managing state properly is one of the biggest challenges in any Android application, especially when using Jetpack Compose. In this article, I want to share a setup I've found very effective for keeping Compose apps clean, manageable, and easy to maintain.

## Why Good State Management is Important

Jetpack Compose simplifies state management, but without a solid strategy, your composables can quickly get messy. Effective state management makes your app:

- Easier to understand and maintain
- Simpler to debug and test
- More scalable as your app grows

Here's my preferred way of structuring state management in Compose apps.

## Recommended State Management Practices

### 1. Keep ViewModels at the Top Level

I recommend placing ViewModels only at the top-level composable for each screen. This centralizes your logic and keeps your UI clean. This also greatly helps with creating @Previews with simple fake states.

```kotlin
val screenModel = getScreenModel<CleanSetupScreenModel>()
```

### 2. Use a Single StateFlow for Screen State

Instead of juggling multiple states, use one `StateFlow` that represents your entire screen state. You can use a sealed class to manage different UI states (Loading, Success, Error):

```kotlin
sealed class CleanSetupScreenState {
    object Loading : CleanSetupScreenState()

    data class Error(val error: String) : CleanSetupScreenState()

    data class Success(
        val someIntValue: Int,
        val someStringValue: String,
        val cleanSetupDataClass: CleanSetupDataClass
    ) : CleanSetupScreenState()
}
```

This simplifies your UI code significantly. As your app grows you'll quickly find that a single screen can require 10+ states and your composable gets "ugly" pretty quickly, using a single state fixes that.

### 3. Handle Events with One Callback

Keep your UI events organized by using a single callback method from the ViewModel. As your app grows you'll quickly find that you'll be passing sometimes 10+ on click / event callbacks to every single composable. This get's hard to manage and understand what happens where and what callback does what.

```kotlin
CleanSetupScreenContent(
    state,
    screenModel::handleEvent
)
```

Use a sealed class for event handling as well:

```kotlin
sealed class CleanSetupScreenEvent {
    object Refresh : CleanSetupScreenEvent()
    data class ToggleFavourite(val clickedDataClass: CleanSetupDataClass) : CleanSetupScreenEvent()
    object NavigateBack : CleanSetupScreenEvent()
    object NavigateToSettings : CleanSetupScreenEvent()
}
```

### 4. Channels for One-Time Events

To handle one-time actions like navigation or snackbar notifications, channels are ideal:

```kotlin
private val _effects = Channel<UiEffect>()
val effects = _effects.receiveAsFlow()
```

Collect these effects easily in your composable:

```kotlin
ObserveAsEvents(effects) {
    when (it) {
        is UiEffect.NavigateBack -> navigator.pop()
        is UiEffect.NavigateToSettings -> navigator.push(SettingsScreen())
    }
}
```

### 5. Lifecycle-Aware State Collection

Use `collectAsStateWithLifecycle` to safely collect your state according to the composable lifecycle:

```kotlin
val state by screenModel.state.collectAsStateWithLifecycle()
```

This prevents common lifecycle-related bugs and ensures efficient resource use.

## Example of a Clean Setup

Here's a concise example showing these practices in action:

```kotlin
@Composable
fun CleanSetupScreenContent(
    state: CleanSetupScreenState,
    event: (CleanSetupScreenEvent) -> Unit
) {
    Scaffold(
        topBar = { CleanSetupTopBar() },
        bottomBar = { CleanSetupBottomBar(event) }
    ) {
        Box(modifier = Modifier.fillMaxSize().padding(it)) {
            when (state) {
                is CleanSetupScreenState.Loading -> CleanSetupLoadingIndicator(modifier = Modifier.align(Alignment.Center))
                is CleanSetupScreenState.Error -> CleanSetupError(state.error, modifier = Modifier.align(Alignment.Center))
                is CleanSetupScreenState.Success -> CleanSetupContent(state, event)
            }
        }
    }
}
```

This clear separation keeps the UI easy to read and update.

## Why This Method Works

I've found this method consistently helpful because it:
- Makes debugging straightforward
- Keeps UI code clean and focused
- Simplifies testing significantly
- Scales well as the project grows

## Final Thoughts

Good state management in Jetpack Compose doesn't have to be complicated. By following these simple practices, you'll find your Compose apps become easier and more enjoyable to develop.

Give this method a try in your next project and see the difference it makes!

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