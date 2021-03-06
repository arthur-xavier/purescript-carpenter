## Module Carpenter

#### `CarpenterEffects`

``` purescript
type CarpenterEffects eff = (props :: ReactProps, state :: ReactState ReadWrite | eff)
```

#### `EventHandler`

``` purescript
type EventHandler = forall eff. Eff (CarpenterEffects eff) Unit
```

General purpose event handler for React events.

#### `ActionHandler`

``` purescript
type ActionHandler action = action -> EventHandler
```

Handler for dispatches of actions.
Useful when handling actions dispatched by child components, e.g.:

```purescript
type MyProps = { onSubmit :: ActionHandler MyAction }
-- ...
createFactory myComponentClass {onSubmit: dispatch AnotherAction}
```

#### `Dispatcher`

``` purescript
type Dispatcher action = action -> EventHandler
```

Type synonym for the action dispatcher function `dispatch`.
Takes an action of type `action` and returns an `EventHandler`.

#### `Yielder`

``` purescript
type Yielder state eff = (state -> state) -> Aff (CarpenterEffects eff) state
```

Type synonym for the `yield` function which takes a function from the
current state to the new state of the component and asynchronously
updates it.

#### `Update`

``` purescript
type Update state props action eff = Yielder state eff -> Dispatcher action -> action -> props -> state -> Aff (CarpenterEffects eff) state
```

Type synonym for an action handler which takes a `Yielder` supplied by
React's internal rendering function, a `Dispatcher` used to dispatch
new actions to the same component, the dispatched action and the
component's current props and state.

The supplied `yield` function asynchronously updates the component's state.

#### `Render`

``` purescript
type Render state props action = Dispatcher action -> props -> state -> Array ReactElement -> ReactElement
```

Type synonym for a pure render function which takes a `Dispatcher` supplied
by React's internal rendering function and the current props and state for
the component, and returns a `ReactElement`.

The supplied `dispatch` function sends actions to the update function.

#### `spec`

``` purescript
spec :: forall state props action eff. state -> Update state props action eff -> Render state props action -> ReactSpec props state eff
```

Constructs a React component based on an initial state, an update function
and a render function.

#### `spec'`

``` purescript
spec' :: forall state props action eff. state -> action -> Update state props action eff -> Render state props action -> ReactSpec props state eff
```

Constructs a React component spec based on an initial state,
an initial action, an update function and a render function.

#### `defaultUpdate`

``` purescript
defaultUpdate :: forall state props action eff. Update state props action eff
```

A default implementation for the update function which does not perform
any changes to the state, that is, ignores all actions.

#### `mockUpdate`

``` purescript
mockUpdate :: forall state props action eff. Update state props action eff -> action -> props -> state -> Aff eff state
```

Generates an update function for testing with mock `yield` and `dispatch`
functions, which do not depend on React, but return the modified state and
behave as expected.


