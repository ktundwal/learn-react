# Flux components

### Dispatcher
- maintains a list of `listeners`. offers `register()` method to register listeners
- has `dispatch()` method which takes an `action` as input param. When client code calls `dispatch()` all `listeners` will get called with `action` as input param

```javascript
export class Dispatcher {
    constructor() {
        this.__listeners = [];
    }
    dispatch(action) {
        this.__listeners.forEach(listener => listener(action))
    }
    register(listener) {
        this.__listeners.push(listener)
    }
}
```

### Store
* an abstract class that must be extended. Derived classes must implement following
    * `getInitialState()` : self explanatory. `Store` is a base class, doesnt define any state
    * `__onDispatch()` 
* requires a `dispatcher` as input param for its `constructor`
    * registers itself as a `listener` to provided `dispatcher`
    * calls `getInitialState()` (must be implemented by derived classes) and stores it as `__state` (own state)
    * just like `Dispatcher`, maintains a list of `listeners`. offers `addListener()` method to register listeners. All these listenerns will be called from `__emitChange()`
* implementes `__emitChange()` which will be used by derived classes when they change the state.

```javascript
export class Store {
    constructor(dispatcher) {
        this.__listeners = []
        this.__state = this.getInitialState()
        dispatcher.register(this.__onDispatch.bind(this))
    }

    __onDispatch() {
        throw new Error ('Subclass must override __onDispatch method of a Flux store')
    }

    getInitialState() {
        throw new Error ('Subclass must override getInitialState method of a Flux store')
    }

    addListener(listener) {
        this.__listeners.push(listener)
    }

    __emitChange() {
        this.__listeners.forEach(listener => listener(this.__state))
    }
}
```

# An application
This is a user application that uses above components for state management. This application has a 2 controls: a text box and 2 radio buttons

* First thing to do is import classes defined above
* Second, instantiate the `Dispatcher`
* Third, find an element in its `html` and do following when user interacts with it
    * get the new value of the control
    * call `Dispatcher.dispatch()` with a `const` string that defines what this action means. In this case it would be `TODO_FONTUPDATEACTION`
* Fourth, create a store by extending `Store` class and instantiate it
    * implement `getInitialState()` as required by the `Store` class
    * implement `__onDispatch()` as required by the store. This is what `Dispatcher.dispatch()` will call when action is received. Do following in this method
        * log to console that it received an action
        * call `__emitChange` to let all listeners know that state changed. _Reminder: dont confuse these listeners with `Dispatcher` listeners. They are different_
    * offer a `getter` that will return `__state` from base class. _Reminder: Base `Store` class simply holds whatever state derived class defines in `__state`_. In this case its a JavaScript `object` which has 2 keys: `userName` and `fontSize`
* Lastly, implement 2 methods 
    * first one adds a `listener` to its own store. This method will simply log the `state` to console
    * second one adds a `listener` to the `Dispatcher` defined in second step. This will log the `action` to console

```javascript
import  {Dispatcher, Store} from './flux'

const controlPanelDispatcher = new Dispatcher()

document.getElementById('userNameInput').addEventListener(`input`, ({target}) => {
    const name = target.value
    console.log('Dispatching ...', name)
    controlPanelDispatcher.dispatch(`TODO_NAMEINPUTACTION`)
})

document.forms.fontSizeForm.fontSize.forEach(element => {
    element.addEventListener(`change`, ({target}) => {
        controlPanelDispatcher.dispatch(`TODO_FONTUPDATEACTION`)
    })
})

class UserPrefsStore extends Store {
    getInitialState() {
        return {
            userName: "Jim",
            fontSize: "small"
        }
    }

    __onDispatch(action) {
        console.log('Store received to dispatch', action)
        this.__emitChange()
    }

    getUserPreferences() {
        return this.__state
    }
}

const userPrefsStore = new UserPrefsStore(controlPanelDispatcher)

userPrefsStore.addListener((state) => {
    console.info('The current state is ...', state)
})

controlPanelDispatcher.register(action => {
    console.info('Received action ...', action)
})
```

