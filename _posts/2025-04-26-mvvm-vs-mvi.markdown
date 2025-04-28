---
layout: post
title: "MVVM vs MVI in Android Development: Finding the Right Fit for Your Compose App"
date: 2025-04-26 22:07:00 +0200
categories: [tutorials, android-development]
tags: ["Android", "Kotlin", "Jetpack Compose", "State Management", "MVVM", "MVI"]
---

The battle between MVVM and MVI never ends! Can they not coexist? Like a quote I like "Make Lovecraft Not Warcraft" we'll try today to do just that.
If you've been using Jetpack Compose, you probably heard about MVVM and MVI. Both patterns help improve state management and testability, but they have different approaches when it comes to events and state changes. Today we'll explore how each one works, show a simple example, and decide which one is the one true king of patterns! Or maybe both are?

---

## Why Architecture Matters

Let's start at the very beginning. Why should I even care about architecture? As your screens grow, a clear architecture pattern helps with managing state, flows, makes code easier to read, understand, maintain, test and debug it also helps reduce the amount of bugs. MVVM and MVI are similar in a way as they both enforce a unidirectional data flow:

```
Business logic → UI state → Composable
User action → Event/Intent → Business logic
```

This ensures Compose always observes a single source of truth.

---

## 1. A Quick Look at MVVM

In MVVM (`Model–View–ViewModel`):

1. You keep UI states in a ViewModel (`LiveData` or `StateFlow`).
2. Composables collects or observes those states and recompose on changes.
3. UI events (button taps, form submissions) call ViewModel methods directly.
4. You have multiple states that are all exposed and collected by Composables.

Here is an example:

```kotlin
// UI state
 data class CounterState(val count: Int = 0)

// ViewModel
class CounterViewModel : ViewModel() {
    private val _isLoading = MutableStateFlow(false)
    val isLoading: StateFlow<Boolean> = _isLoading.asStateFlow()
    
    private val _data = MutableStateFlow(MyData())
    val data: StateFlow<MyData> = _data.asStateFlow()

    private val _error = MutableStateFlow("")
    val error: StateFlow<String> = _error.asStateFlow()

    private val _counter = MutableStateFlow(0)
    val counter: StateFlow<Int> = _counter.asStateFlow()

    fun increment() = _counter.update { it + 1 }
    fun decrement() = _counter.update { it - 1 }
}

// Composable
@Composable
fun CounterScreen(viewModel: CounterViewModel = viewModel()) {
    val isLoading by viewModel.isLoading.collectAsState()
    val data by viewModel.data.collectAsState()
    val error by viewModel.error.collectAsState()
    val counter by viewModel.counter.collectAsState()

    Column(
        modifier = Modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(text = "Count: ${counter}")
        Spacer(modifier = Modifier.height(16.dp))
        Row {
            Button(onClick = { viewModel.decrement() }) { Text("-") }
            Spacer(modifier = Modifier.width(8.dp))
            Button(onClick = { viewModel.increment() }) { Text("+") }
        }
    }
}
```

**Why MVVM?**
- Familiar setup for many Android developers.
- Quick to implement for simple screens.
- Clear separation: ViewModel handles logic, Composable handles rendering.

---

## 2. Stepping Through MVI

In MVI (`Model–View–Intent`), every user interaction is an **Intent**, and you reduce that intent into a brand-new, immutable **UiState**. The flow looks like:

```text
User action → Intent → Reducer → UiState → UI
```

Here's the same example as MVI:

```kotlin
// Intents
sealed class CounterIntent {
    object Increment : CounterIntent()
    object Decrement : CounterIntent()
}

// UI state
data class CounterUIState(
    val count: Int = 0,
    val isLoading: Boolean = false,
    val error: String = "",
    val data: MyData = MyData()
)

// ViewModel
class CounterMviViewModel : ViewModel() {
    private val _state = MutableStateFlow(CounterUIState())
    val state: StateFlow<CounterUIState> = _state.asStateFlow()

    fun onIntent(intent: CounterIntent) {
        _state.value = when (intent) {
            CounterIntent.Increment -> _state.value.copy(count = _state.value.count + 1)
            CounterIntent.Decrement -> _state.value.copy(count = _state.value.count - 1)
        }
    }
}

// Composable
@Composable
fun CounterMviScreen(viewModel: CounterMviViewModel = viewModel()) {
    val uiState by viewModel.state.collectAsState()

    Column(
        modifier = Modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(text = "Count: ${uiState.count}")
        Spacer(modifier = Modifier.height(16.dp))
        Row {
            Button(onClick = { viewModel.onIntent(CounterIntent.Decrement) }) { Text("-") }
            Spacer(modifier = Modifier.width(8.dp))
            Button(onClick = { viewModel.onIntent(CounterIntent.Increment) }) { Text("+") }
        }
    }
}
```

**Why MVI?**
- One immutable state object simplifies reasoning about UI.
- All events go through a single handler (`onIntent`), making test coverage straightforward.
- Perfect for complex flows, form validation, and time-travel debugging.

---

## 3. When to Choose MVVM or MVI

<div class="pattern-comparison">
<div class="pattern">
<h3>MVVM</h3>

<div class="section">
<h4>Pros:</h4>
<ul>
<li>Less boilerplate</li>
<li>Easy to pick up</li>
<li>Fast iteration</li>
</ul>
</div>

<hr class="section-divider">

<div class="section">
<h4>Cons:</h4>
<ul>
<li>State can scatter across multiple flows</li>
<li>Harder to trace complex state changes</li>
</ul>
</div>

<hr class="section-divider">

<div class="section when-to-use">
<h4>When to Use:</h4>
<ul>
<li>Simple to medium screens</li>
<li>Teams familiar with ViewModel + LiveData/Flow</li>
</ul>
</div>
</div>

<div class="pattern">
<h3>MVI</h3>

<div class="section">
<h4>Pros:</h4>
<ul>
<li>Single source of truth</li>
<li>Predictable, immutable state</li>
<li>Excellent testability</li>
</ul>
</div>

<hr class="section-divider">

<div class="section">
<h4>Cons:</h4>
<ul>
<li>More code - sealed classes + reducer</li>
<li>Can feel verbose for trivial UIs</li>
</ul>
</div>

<hr class="section-divider">

<div class="section when-to-use">
<h4>When to Use:</h4>
<ul>
<li>Complex forms or wizards</li>
<li>Heavy-duty state transitions</li>
<li>Need replayable actions</li>
</ul>
</div>
</div>
</div>

<style>
.pattern-comparison {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}
.pattern {
  flex: 1;
  min-width: 300px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
.section {
  width: 100%;
}
.when-to-use {
  flex-grow: 1;
}
.pattern h4 {
  margin-bottom: 10px;
}
.section-divider {
  margin: 15px 0;
  border-top: 1px solid #444;
  width: 100%;
}
</style>

Both patterns integrate seamlessly with Compose's unidirectional rendering. You can even mix them—start with MVVM, then refactor to MVI as your screen's complexity grows.

---

## Final Thoughts

There's no one-size-fits-all. There is no one pattern to rule them all. Consider what's best for you and your team, screen complexity, code size. Jetpack Compose works well with both of them and both will keep your code clean and maintainable.

## Related Resources

If you're interested in seeing MVI in action with a clean architecture setup for Jetpack Compose, check out my post on [How to Manage State Effectively in Jetpack Compose]({% post_url 2025-04-22-clean-compose-setup %}). It demonstrates practical examples of implementing MVI and proper state management in a real-world Android application.

