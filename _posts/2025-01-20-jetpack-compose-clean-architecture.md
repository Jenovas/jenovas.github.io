---
layout: post
title: "Jetpack Compose - Clean Architecture, How to manage state"
date: 2025-01-20 10:00:00 +0200
categories: [tutorials, android-development]
tags: ["Android", "Kotlin", "Jetpack Compose", "Clean Architecture", "State Management", "MVI"]
---

# Jetpack Compose with Clean Architecture: A Practical Guide to State Management

How do I manage state? How should I trigger navigation? How should I show a snackbar? State vs Single Events? How to keep it clean and scalable? What is the right way to do it? These are questions you probably ask yourself every single time you start working on a new screen, new app, new composable. In this guide I'll share a few proven patterns for building scalable, testable, and maintainable Android applications using a few examples from my own trading strategy management app. Link to repo - 

<!--more-->

---

## Why Clean Architecture Matters for State Management

Without proper architecture, your Compose app quickly becomes a tangled mess of UI logic, business rules, and data handling. Clean Architecture solves this by creating clear boundaries between layers, making your state management predictable and your code easier to test.

---

## Recommended Practices

### 1. Separate Domain Logic from UI State

Keep your business entities pure and independent of UI concerns. Your domain models should contain business logic and validation.

```kotlin
// Domain Entity - Pure business logic
data class Strategy(
    val id: String,
    val name: String,
    val isActive: Boolean = true,
    val createdAt: LocalDateTime,
    val updatedAt: LocalDateTime
) {
    init {
        require(id.isNotBlank()) { "Strategy ID cannot be blank" }
        require(name.isNotBlank()) { "Strategy name cannot be blank" }
        require(name.length <= 100) { "Strategy name cannot exceed 100 characters" }
    }

    fun activate(): Strategy = copy(isActive = true, updatedAt = LocalDateTime.now())
    fun deactivate(): Strategy = copy(isActive = false, updatedAt = LocalDateTime.now())
}
```

**Why this matters**: Your business logic stays framework-independent and fully testable without Android dependencies.

### 2. Use Use Cases for Business Operations

Encapsulate business operations in use cases that coordinate between repositories and apply business rules.

```kotlin
class GetStrategiesUseCase(
    private val strategyRepository: StrategyRepository
) {
    operator fun invoke(): Flow<List<Strategy>> {
        return strategyRepository.getStrategies()
    }

    fun getActiveStrategies(): Flow<List<Strategy>> {
        return strategyRepository.getActiveStrategies()
    }
}
```

**Why this matters**: Business logic is centralized, reusable, and easy to test in isolation.

### 3. Implement Proper MVI with StateFlow and Channel

Use a single StateFlow for screen state and Channel for one-time effects, following the MVI pattern.

```kotlin
class StrategiesViewModel(
    private val getStrategiesUseCase: GetStrategiesUseCase,
    private val saveStrategyUseCase: SaveStrategyUseCase
) : ViewModel() {

    private val _uiState = MutableStateFlow(StrategiesUiState())
    
    val state: StateFlow<StrategiesUiState> = combine(
        getStrategiesUseCase(),
        _uiState
    ) { strategies, currentState ->
        currentState.copy(
            isLoading = false,
            strategies = strategies.map { it.toStrategyItem() }
        )
    }
    .catch { throwable ->
        emit(_uiState.value.copy(error = throwable.message))
    }
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000L), StrategiesUiState(isLoading = true))

    private val _effects = Channel<StrategiesEffect>(capacity = Channel.BUFFERED)
    val effects = _effects.receiveAsFlow()

    fun handleEvent(event: StrategiesEvent) {
        when (event) {
            is StrategiesEvent.ToggleStrategyActive -> toggleStrategyActive(event.strategyId)
            is StrategiesEvent.DeleteStrategy -> deleteStrategy(event.strategyId)
            // ... other events
        }
    }
}
```

**Why this matters**: Clear separation between ongoing state and one-time effects prevents UI bugs and makes testing easier.

### 4. Define Comprehensive State Models

Create rich state models that include computed properties and all UI-relevant information.

```kotlin
data class StrategiesUiState(
    val isLoading: Boolean = false,
    val strategies: List<StrategyItem> = emptyList(),
    val error: String? = null,
    val searchQuery: String = "",
    val showActiveOnly: Boolean = false
) {
    val hasStrategies: Boolean get() = strategies.isNotEmpty()
    val activeStrategiesCount: Int get() = strategies.count { it.isActive }
    val filteredStrategies: List<StrategyItem> get() {
        return strategies.filter { strategy ->
            val matchesSearch = searchQuery.isBlank() || 
                strategy.name.contains(searchQuery, ignoreCase = true)
            val matchesActive = !showActiveOnly || strategy.isActive
            matchesSearch && matchesActive
        }
    }
}
```

**Why this matters**: Computed properties keep UI logic centralized and prevent duplicate filtering logic across composables.

### 5. Use Sealed Interfaces for Events and Effects

Define all possible user interactions and side effects using sealed interfaces.

```kotlin
sealed interface StrategiesEvent {
    object LoadStrategies : StrategiesEvent
    object RefreshStrategies : StrategiesEvent
    data class ToggleStrategyActive(val strategyId: String) : StrategiesEvent
    data class DeleteStrategy(val strategyId: String) : StrategiesEvent
    data class SearchStrategies(val query: String) : StrategiesEvent
    object DismissError : StrategiesEvent
}

sealed interface StrategiesEffect {
    data class ShowMessage(val message: String) : StrategiesEffect
    data class NavigateToDetail(val strategyId: String) : StrategiesEffect
    object NavigateToCreate : StrategiesEffect
}
```

**Why this matters**: Exhaustive when statements ensure all events are handled and make adding new features safer.

### 6. Implement Proper Dependency Injection

Use constructor injection for ViewModels and avoid service locator patterns inside ViewModels.

```kotlin
// Koin DI Configuration
val domainModule = module {
    singleOf(::MockStrategyRepository) bind StrategyRepository::class
    factoryOf(::GetStrategiesUseCase)
    factoryOf(::SaveStrategyUseCase)
}

val viewModelModule = module {
    viewModel { StrategiesViewModel(get(), get(), get(), get()) }
}

// ViewModel with injected dependencies
class StrategiesViewModel(
    private val getStrategiesUseCase: GetStrategiesUseCase,
    private val saveStrategyUseCase: SaveStrategyUseCase,
    private val deleteStrategyUseCase: DeleteStrategyUseCase,
    private val getStrategyByIdUseCase: GetStrategyByIdUseCase
) : ViewModel()
```

**Why this matters**: Clear dependencies make testing easier and ensure ViewModels don't violate the dependency inversion principle.

### 7. Implement Clean Screen Structure

Follow a consistent three-layer composable structure for all screens.

```kotlin
@Composable
fun StrategiesScreen(
    navController: NavHostController,
    viewModel: StrategiesViewModel = koinViewModel()
) {
    val snackbarHostState = remember { SnackbarHostState() }
    
    // Handle side effects
    ObserveAsEvents(viewModel.effects) { effect ->
        when (effect) {
            is StrategiesEffect.ShowMessage -> {
                // Show snackbar
            }
            is StrategiesEffect.NavigateToDetail -> {
                // Navigate to detail screen
            }
        }
    }

    val state by viewModel.state.collectAsStateWithLifecycle()
    
    StrategiesScreenContent(
        state = state,
        onEvent = viewModel::handleEvent
    )
}

@Composable
private fun StrategiesScreenContent(
    state: StrategiesUiState,
    onEvent: (StrategiesEvent) -> Unit
) {
    // UI implementation
}
```

**Why this matters**: Consistent structure makes navigation and state management predictable across all screens.

### 8. Handle Business Logic in Use Cases, Not ViewModels

Keep ViewModels focused on coordinating between UI and business logic.

```kotlin
// Wrong: Business logic in ViewModel
private fun toggleStrategyActive(strategyId: String) {
    viewModelScope.launch {
        val strategy = repository.getStrategy(strategyId)
        val updated = strategy.copy(
            isActive = !strategy.isActive,
            updatedAt = LocalDateTime.now()
        )
        repository.saveStrategy(updated)
    }
}

// Right: Business logic in domain entities and use cases
private fun toggleStrategyActive(strategyId: String) {
    viewModelScope.launch {
        try {
            val strategy = getStrategyByIdUseCase(strategyId)
            val updatedStrategy = if (strategy.isActive) {
                strategy.deactivate()
            } else {
                strategy.activate()
            }
            saveStrategyUseCase(updatedStrategy)
            _effects.send(StrategiesEffect.ShowMessage("Strategy updated"))
        } catch (e: Exception) {
            _effects.send(StrategiesEffect.ShowMessage("Failed: ${e.message}"))
        }
    }
}
```

**Why this matters**: Business rules stay in the domain layer where they belong, making them reusable and testable.

### 9. Map Domain Models to UI Models

Transform domain entities to UI-specific models in the ViewModel.

```kotlin
private fun Strategy.toStrategyItem(): StrategyItem {
    return StrategyItem(
        id = id,
        name = name,
        description = description,
        isActive = isActive,
        strategyType = strategyType.name,
        lastUpdated = updatedAt.toString(),
        isRecentlyModified = isRecentlyModified(),
        tags = tags
    )
}
```

**Why this matters**: UI models can be optimized for display purposes without affecting business logic.

### 10. Use Lifecycle-Aware State Collection

Prevent memory leaks and stale updates by using lifecycle-aware collection.

```kotlin
@Composable
fun StrategiesScreen(viewModel: StrategiesViewModel) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    
    ObserveAsEvents(viewModel.effects) { effect ->
        // Handle one-time effects
    }
}
```

**Why this matters**: Automatic lifecycle management prevents memory leaks and ensures UI updates stop when appropriate.

---

## Complete Implementation Example

Here's how all these practices work together in a real screen:

```kotlin
@Composable
private fun StrategiesScreenSharedScaffoldContent(
    state: StrategiesUiState,
    onEvent: (StrategiesEvent) -> Unit
) {
    Column(modifier = Modifier.fillMaxSize()) {
        // Header with statistics
        StrategiesHeaderCard(
            totalCount = state.strategies.size,
            activeCount = state.activeStrategiesCount,
            onCreateClick = { onEvent(StrategiesEvent.NavigateToCreateStrategy) },
            onRefreshClick = { onEvent(StrategiesEvent.RefreshStrategies) }
        )
        
        // Content based on state
        when {
            state.isLoading -> LoadingContent()
            state.error != null -> ErrorContent(state.error) { 
                onEvent(StrategiesEvent.DismissError) 
            }
            state.hasStrategies -> {
                StrategiesList(
                    strategies = state.filteredStrategies,
                    onToggleActive = { onEvent(StrategiesEvent.ToggleStrategyActive(it)) },
                    onDelete = { onEvent(StrategiesEvent.DeleteStrategy(it)) }
                )
            }
            else -> EmptyStrategiesContent()
        }
    }
}
```

---

## Why This Approach Works

**Predictable State Flow**: State flows in one direction from domain to UI, making bugs easier to track and fix.

**Testable Architecture**: Each layer can be tested independently with clear boundaries and mock dependencies.

**Scalable Design**: Adding new features follows established patterns without requiring architectural changes.

**Maintainable Code**: Clear separation of concerns makes code easier to understand and modify.

**Reusable Components**: Business logic in use cases can be shared across different UI components.

---

## Final Thoughts

Clean Architecture with proper state management isn't just about following patterns—it's about creating sustainable codebases that grow with your needs. By separating business logic from UI concerns, using reactive streams for state management, and maintaining clear boundaries between layers, you create applications that are both robust and enjoyable to work with.

The key is starting with simple patterns and building complexity gradually. Focus on the dependency rule, keep your domain layer pure, and let your UI layer be a simple reflection of your business state. Your future self (and your team) will thank you.

> Start with these patterns in your next Compose project and experience the difference that proper architecture makes.
