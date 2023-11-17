# Jetpack Compose #

Jetpack Compose is a modern way to build UI for Android apps. With compose we write user interfaces
with Kotlin. So we can use Kotlin to define both how the app looks and how it behaves. This
makes it easy to pass data and events between UI and other parts of the app.

## Composable ##

A Composable is a function that defines a UI component. The function doesn't return anything and is
annotated with `@Composable`. There are several built-in composable provided by Compose such as
Image, Text, Button, etc.

Compose uses a declarative UI approach i.e. we need to declare everything about how the UI should
look using Composable functions.

## Recomposition ##

Composables are immutable, so there is no way to update a Composable once it's been created. So when
app data changes, to refresh the UI Compose automatically re-executes the Composable functions for
updated UI. This process is called Recomposition. UI elements are controlled by the inputs to
Composable functions, so we don't have to worry about keeping app data and UI in sync. Compose
optimises and skips any work for elements that haven't changed.

## State in Compose ##

A state is any value in an app that can change over time. Composables are stateless by default i.e.
they don't automatically remember their state. For example, Text fields don't remember the text
value we entered.

## Composition and Recomposition ##

Composition is a description of the UI built by Compose when it executes composables. Composable
functions transform data into UI.

When the state changes, Compose re-executes the affected composable functions with the new state,
which updates the UI. This is called recomposition and is automatically scheduled by Compose.

Composition can only be produced by an initial composition, which is when Compose runs the
composable for the first time. It keeps track of the composables that are called to describe the
UI.The only way to modify the composition is through recomposition. To do this Compose needs to know
what state to track so that it observes that and schedules recomposition whenever the state changes.

`State` and `MutableState` are used to make the state observable and tracked by Compose. The value
to be observed is wrapped in a State object.

```
// amountInput is an state and is observed by the TextField
var amountInput = mutableStateOf("0")

TextField(
    value = amountInput.value,
    onValueChange = { amountInput.value = it }
)
```

And now, whenever the value changes a recomposition it scheduled. But, when the composable is
re-executed it resets the value to the initial value, because the state is not saved.

To preserve the state across recompositions we need to save it in memory using `remember` function.

```
var amountInput = remember { mutableStateOf(“0”) }

TextField(
    value = amountInput.value,
    onValueChange = { amountInput.value = it }
)
```

## State Hoisting ##

In a composable function when we define variables to hold the state, it gets tied to the composable.
State hoisting is a pattern of moving state to its caller to make the component stateless. The
stateless composable doesn't hold, define or modify a new state.

We should hoist the state when we need to :

* Share the state with multiple composable functions.

* Create a stateless composable that can be reused in the app.

```
// state in the composable itself
var amountInput = remember { mutableStateOf(“0”)}

TextField(
    value = amountInput.value,
    onValueChange = { amountInput.value = it }
)
```

```
// state being passed into the composable
@Composable
fun EditNumberField(
    value: String,
    onValueChange: (String) -> Unit,
    modifier: Modifier = Modifier
) {
    TextField(
        value = value,
        onValueChange = onValueChange
    )
}
```

## Instrumented Testing with Compose ##

Instrumentation tests test an actual instance of the app and its UI, so the UI content must be set.
We do this using `composeTestRule`

```
@get:Rule
val composeTestRule = createComposeRule()

@Test
fun calculateTip_20PercentOf10_return2() {
    composeTestRule.setContent {
        TipCalculatorTheme {
            TipTimeLayout()
        }
    }
}
```

UI components can be accessed as nodes through the `composeTestRule`. A common way to do this is to
access a node that contains a particular text with the `onNodeWithText()` method. In instrumentation
tests with Compose, assertions can be called directly on UI components.