---
layout:     post
title:      Building Tesla's battery range calculator with React Part 2 (Redux version)
date:       2017-03-20 10:35:29
summary:    Rebuilding Tesla's Battery Range Calculator with React/Redux 
categories: blog development react
---

This tutorial is the second part of building Tesla's battery range calculator with React.

In the part 1, after constructing the project through create-react-app, we implemented each component by subdividing the UI and managed the state and event using local state and props, and completed the entire app.

> 
Check out the [part 1](https://gyver98.github.io/blog/development/react/2017/02/09/react-tesla-battery-range-calculator-part1-korean/)

In this part, we will introduce <span class="bg-dark-gray white">Redux</span>, a state management solution, to see how we can transform our application into an application that manages the state of the app with Redux.

This is the final GIF image of our application.
_![](https://github.com/gyver98/gyver98.github.io/blob/master/images/redux%20dev%20tools.gif?raw=true)_

> Check out the part 2 [live demo](http://redux-tesla-charge-calculator.surge.sh/)

Before we taking a look into what Redux is, let's see why we need to use Redux to solve problems.

## 1. What problem do we solve?

Redux is getting becoming the de facto way to build React apps. But should Redux be used in all React apps? At the very least, not all apps will need an ambitious state management solution from the beginning. 

Today's front-end development trends are __component-based__. Components can have data state and UI state, and the state that they need to manage becomes more and more complicated as your app grows.

__State management__ solutions have emerged to solve the following problems, and Redux is becoming popular as a standard among other solutions. 

```
 - components share state
 - state should be accessible from anywhere
 - components need to mutate the state
 - components need to mutate the state of another component
```

Redux is a __state management library__, which is a great tool that allows you to store the state of our app somewhere, mutate the state, and receive the updated state.

In other words, with Redux, we have one place where we can refer the state, change the state, and get the updated state. 

Redux was written with React in mind, but it is also __framework-agnostic__ and even can be used with Angular or jQuery applications.

>
It is recommended that you read Dan Abramov's [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367#.uz11a0vkc) before choosing Redux.

## 2. Data flow in Redux

As you saw in the part 1, in React, the data is passed through the component using props.
This is called __unidirectional data flow__ that flows from parent to child.

Due to these characteristics, communication between components other than parent-child relationship is not clear.

![enter image description here](https://lh3.googleusercontent.com/42IhN3FAY3DSZ866Z1klFF2zK7OPQ-Ufqd-k1YqS_Kdklem4O1nwSYIYm-u-UUgkZUpw4hSNTA=s944 "data flow1.png")

React does not recommend direct __component-to-component__ communication as shown above.
There is a suggested way for this in React, but you have to implement it yourself.

According to React docs:

>
For communication between two components that don't have a parent-child relationship, you can set up your own global event system. ... Flux pattern is one of the possible ways to arrange this.

![enter image description here](https://lh3.googleusercontent.com/_QVuP6ZcvHsh2eeUoy_uHZorb2Ay3R9tB3CPqt7sAkSH8W548aTH57Petf8ppahrN7puOy7YKg=s944 "redux flow.png")	

This is where Redux comes in handy.

Redux provides a solution for managing all application state in a single place called a <span class="bg-dark-gray white">store</span>.
The component then <span class="bg-dark-gray white">dispatches</span> the state change to the store instead of passing it directly to the other components.

The components that need to be aware of state changes can <span class="bg-dark-gray white">subscribe</span> to the store.

>
Redux is, in a word, a state container that represents and manages the state of an app as a single object from a JavaScript-based application.

## 3. Redux Core Concept

Redux itself is very simple. The state of the app we created in the last article can be represented as a generic object like this: 

![enter image description here](https://lh3.googleusercontent.com/kF94wMUWGh2Xv7LxamrX-JlwBYdCCq0pKjZvzfKzUvt4Dpc58u_gX0C9zIAnuTyP52lSxXK0iA=s944 "state object.png")

This object is the same as the model without setters.

To change this state in Redux, you must dispatch an <span class="bg-dark-gray white">action</span>. 

Actions are plain objects describing __what happened__ in the app, and serve as the sole way to describe an __intention to mutate the data__. 
It's one of the __fundamental design choices__ of Redux.

Here are some examples to be implemented in our app soon.

![enter image description here](https://lh3.googleusercontent.com/1zQwJBmCRp4X9YPAu4Mu0TlTNuSxUepDXMp8jIz2cA74xbRs7dTjJwmF4jEzbzeQTBl09YDzfQ=s944 "actions.png")

Forcing all of these state changes into action will give us a clear understanding of what's going on in your app.
When something happens, we can see why it happened.

Now we need a function called <span class="bg-dark-gray white">reducer</span> to bind these states and actions together. 
Reducer is nothing more than a function that takes a state and an action as arguments and returns a __new state__. 

In a word:

> 
(state, action) => state 

Actions only describe that something happened and don't specify __how the application's state changes in response__.
This is the job of reducers.

Here is one example of a reducer to implement in our app.


![enter image description here](https://lh3.googleusercontent.com/RVYAdVRC71UVO2mQLcGm7_3tkC1O1zZ7ougM45iZt9X8Rzxnd0Cktu4q0gW8zEoNNDt5Oc2XAQ=s944 "reducer.png")


## 4. Redux Three Principles

I have mentioned <span class="bg-dark-gray white">Flux</span> for a while.
Flux is a __pattern of state management__, not a downloadable tool like Redux. Redux, on the other hand, is a __practical implementation of the Flux pattern__ and has three main principles.

### 4.1 Single source of truth

>
The state of the entire application is stored in an object tree within a __single store__.

![state tree](https://lh3.googleusercontent.com/BKBEU-IaqTlTxZ3VkAGHen5-bGTQpcKhknNdt2TRfD5FIW4n-YO2BpsUHsC-QA3CYtNxYm6GMw=s944 "state tree.png")  

Since all states exist in one place, this is called a <span class="bg-dark-gray white">single source of truth</span>.

This <span class="bg-dark-gray white">one-store</span> approach of Redux is one of the primary differences between it and __Flux's multiple store__ approach. 

What are the advantages of a single state tree? This makes it easier to debug applications or perform internal inspections and to easily implement some features that were previously difficult to implement (for example, undo / redo).

### 4.2 State is read-only

> The only way to change the state is to __emit an action__ that describes what happened. 

In other words, the application does not directly change the state, but instead expresses the intention to transform the state by passing the action.

In fact, if you look at the Redux API, you can see that it consists of just four methods.

```
store.dispatch(action)
store.subscribe(listener)
store.getState()
replaceReducer(nextReducer)
```

As you can see, there is no __setState()__ method. Therefore, __passing an action__ is the only channel that can mutate the state of the application.

### 4.3 Changes are made with pure functions

>
You write reducers as __pure functions__ to specify the concrete way the state tree is transformed by action.

Reducers are pure functions that take a previous state and action and return a new state.
Keep in mind that you must return a __new state__ object instead of changing the old state.

"Given the same arguments, it should calculate the next state and return it. No surprises. No side effects. No API calls. No mutations. Just a calculation." - [Redux Docs](http://redux.js.org/docs/basics/Reducers.html)

The pure function has the following characteristics.

```
- It does not make outside network or database calls.
- Its return value depends solely on the values of its parameters.
- Its arguments should be considered "immutable", 
  meaning they should not be changed.
- Calling a pure function with the same set of arguments will 
  always return the same value.
```

## 5. Divide The App Into Containers and Components

 
Now, let's re-build our Tesla calculator app that we made in Part 1 as a Redux version.

First, let's look at the __overall component UI layout__ of the app that will be implemented in this article.

_![enter image description here](https://lh3.googleusercontent.com/YlCnB9gS8_KJgCfW1d8Qb8Ws01zOzj-huYRxu35Vqx6wnH9UJm_LCa85suCubWlYlDC8keXUMw=s1100 "compoent layout.png")_
 

Placing React and Redux logic inside a single component can be messy, so it is recommended that you create a <span class="bg-dark-gray white">Presentational component</span> for presentation purposes only, and a <span class="bg-dark-gray white">Container component</span>, an upper wrapper component that handles Redux and dispatches actions.

The role of the parent Container component is to provide state values to presentational components, to manage events, and to communicate with Redux on behalf of presentational components.

## 6. List State and Actions For Each Component

Refer to the entire component layout to create a list of states and actions for each component.

```
TeslaCar Container :
	state : wheels
	action : N/A

TeslaStats Container :
	state : carstats(array)
	action : N/A
	
TeslaSpeedCounter Container : 
	state : config.spped
	action : SPEED_UP, SPEED_DOWN

TeslaTempCounter Container : 
	state : config.temperature
	action : TEMPERATURE_UP, TEMPERATURE_DOWN
	
TeslaClimate Container : 
	state : config.climate
	action : CHANGE_CLIMATE

TeslaWheel Container : 
	state : config.wheel
	action : CHANGE_WHEEL
```

## 7. Set up part 1 project code base

>
If you want to go directly to part 2 without looking at part 1, you need to build the codebase first by cloning the part 1 [code](https://github.com/gyver98/part1-react-tesla-battery-range-calculator-tutorial).

After the __npm start__, let's make sure the application works.

* __git clone__ https://github.com/gyver98/part1-react-tesla-battery-range-calculator-tutorial
![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/git%20clone.png?raw=true)


* __npm install__
![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/npm%20install.png?raw=true)


* __npm start__
![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/app.png?raw=true)


## 8. Create Action Creators For Each Action

Now that you have created an action list, it's time to create <span class="bg-dark-gray white">action creators</span>.

Action creator is a function that literally creates an action object. 
In Redux, action crators simply return an action object and pass the argument value if necessary.

__changeWheel action creator sample :__

```
const changeWheel = (value) => {
  return {
    type: 'CHANGE_WHEEL',
    value
  }
}
```

These action creators are passed to the dispatch function as the result value, and the dispatch is executed.

```
dispatch(changeWheel(size))
```

The dispatch function can be accessed directly from the store as __store.dispatch()__, but in most cases it will be accessed using a helper like react-redux's <span class="bg-dark-gray white">connect()</span>.
We'll look at __connect__ later. 

### 8.1 Create Action.js

Create an index file in the src/actions directory and define action creators as follows:

__src/actions/index.js__

```
import { counterDefaultVal } from '../constants/counterDefaultVal';

export const speedUp = (value) => {
  return {
    type: 'SPEED_UP',
    value,
    step: counterDefaultVal.speed.step,
    maxValue: counterDefaultVal.speed.max
  }
}

export const speedDown = (value) => {
  return {
    type: 'SPEED_DOWN',
    value,
    step: counterDefaultVal.speed.step,
    minValue: counterDefaultVal.speed.min
  }
}

export const temperatureUp = (value) => {
  return {
    type: 'TEMPERATURE_UP',
    value,
    step: counterDefaultVal.temperature.step,
    maxValue: counterDefaultVal.temperature.max
  }
}

export const temperatureDown = (value) => {
  return {
    type: 'TEMPERATURE_DOWN',
    value,
    step: counterDefaultVal.temperature.step,
    minValue: counterDefaultVal.temperature.min
  }
}

export const changeClimate = () => {
  return {
    type: 'CHANGE_CLIMATE'
  }
}

export const changeWheel = (value) => {
  return {
    type: 'CHANGE_WHEEL',
    value
  }
}

export const updateStats = () => {
  return {
    type: 'UPDATE_STATS'
  }
}
```
* Check out [index.js](https://gist.github.com/gyver98/9d088084834ec6a0f893c8576c7d9204#file-index-js)

Because we need __default values__ based on the action creator, we define this constant value in constants/counterDefaultVal under src directory and import it.

__src/constants/counterDefaultVal.js__

```
export const counterDefaultVal = {
  speed: {
    title: "Speed",
    unit: "mph",
    step: 5,
    min: 45,
    max: 70
  },
  temperature: {
    title: "Outside Temperature",
    unit: "°",
    step: 10,
    min: -10,
    max: 40
  }
}
```

* Check out [counterDefaultVal.js](https://gist.github.com/gyver98/e560ca69057d40e0688000b94d7c0fd9#file-counterdefaultval-js)


## 9. Create Reducers For Each Action

__Reducers__ is a function that receives state and action objects from a Redux store and returns a new state to be stored back into Redux.

It is important not to directly modify the given state here.
Reducers must be __pure functions__ and must return a __new state__.

* Reducer functions are called from the __Container__ that will be created when a user action occurs.
* When the Reducer returns a state, __Redux passes the new state__ to each component, and __React renders each component__ again.

### 9.1 Immutable Data Structures

* JavaScript primitive data type(number, string, boolean, undefined, and null) => <span class="bg-dark-gray white">immutable</span>
* Object, array and function => <span class="bg-dark-gray white">mutable</span>

Changes to the data structure are known to be buggy.
Since our store is consisted of state objects and arrays, we need to implement __a strategy to keep the state immutable__.

There are three ways to change the state here:

__ES5__

```
// Example One
state.foo = '123';

// Example Two
Object.assign(state, { foo: 123 });

// Example Three
var newState = Object.assign({}, state, { foo: 123 });
```

In the example above, the first and second mutate the state object.
The second example mutates because __Object.assign()__ merges all its arguments into the first argument. 

The third example __doesn't mutate the state__. It merges the contents of state and { foo: 123 } into a __new empty object__ which is the first argument.

The <span class="bg-dark-gray white">spread operator</span> introduced in __ES6__ provides a simpler way to keep the state immutable.

__ES6 (ES2015)__

```
const newState = { ...state, foo: 123 };
```

> 
For more information about the spread operator, 
see [here](http://redux.js.org/docs/recipes/UsingObjectSpreadOperator.html).

### 9.2 Create Reducer for change climate

First, we will create ChangeClimate through __test-driven development__ method.

In Part1, our app was generated through __create-react-app__, so we basically use <span class="bg-dark-gray white">jest</span> as test runner. 

The jest looks for a test file using one of the following naming conventions:

```
Files with .js suffix in __tests__ folders
Files with .test.js suffix
Files with .spec.js suffix
```

Create __teslaRangeApp.spec.js__ in src/reducers and create a test case.

```
describe('test reducer', () => {
  it('should handle initial stat', () => {
    expect(
      appReducer(undefined, {})
    ).toEqual(initialState)
  })
})
```

After create the test, run the <span class="bg-dark-gray white">npm test</span> command. You should be able to see the following test failure message. This is because we have not written the __appReducer__ yet.
 
![enter image description here](https://lh3.googleusercontent.com/gxuCGjMSqDK92QZeBN1Pfg0tS4ErO49MISJwasZFoKq3pAHSTrrox1uQfyfUL9gT-Amz_1i0KA=s1100 "npm-test.jpg")

To make the first test successful, we need to create __teslaRangeApp.js__ in the same reducers directory and write __inital state and reducer__ functions.

__sre/reducers/teslaRangeApp.js__

```
const initialState = {
  carstats:[
    {miles:246, model:"60"},
    {miles:250, model:"60D"},
    {miles:297, model:"75"},
    {miles:306, model:"75D"},
    {miles:336, model:"90D"},
    {miles:376, model:"P100D"}
  ],
  config: {
    speed: 55,
    temperature: 20,
    climate: true,
    wheels: 19
  }
}

function appReducer(state = initialState, action) {
  switch (action.type) {
    
    default:
      return state 
  }
}

export default appReducer;
```

Next, import teslaRangeApp.js from teslaRangeApp.spec.js and set initialState.

__sre/reducers/teslaRangeApp.spec.js__

```
import appReducer from './teslaRangeApp';

const initialState =  {
  carstats:[
    {miles:246, model:"60"},
    {miles:250, model:"60D"},
    {miles:297, model:"75"},
    {miles:306, model:"75D"},
    {miles:336, model:"90D"},
    {miles:376, model:"P100D"}
  ],
  config: {
    speed: 55,
    temperature: 20,
    climate: true,
    wheels: 19
  }
}

describe('test reducer', () => {
  it('should handle initial stat', () => {
    expect(
      appReducer(undefined, {})
    ).toEqual(initialState)
  })
})
```

Run npm test again and the test will succeed. 

In the current test case, the action type is {}, so the __initialState__ is returned.

![enter image description here](https://lh3.googleusercontent.com/KIVhW-zKx7br3A801T6AcsPuCUu2YuJiqHAYDLvT4nfQZMwjiGM3LVKFyQeQNuMK1PqOxkGncA=s1100 "npm test2.png")

Now let's test the CHANGE_CLIMATE action.

Add the following climateChangeState and CHANGE_CLIMATE test cases to teslaRangeApp.spec.js.

```
const climateChangeState = {
  carstats:[
    {miles:267, model:"60"},
    {miles:273, model:"60D"},
    {miles:323, model:"75"},
    {miles:334, model:"75D"},
    {miles:366, model:"90D"},
    {miles:409, model:"P100D"}
  ],
  config: {
    speed: 55,
    temperature: 20,
    climate: false,
    wheels: 19
  }
}

it('should handle CHANGE_CLIMATE', () => {
    expect(
      appReducer(initialState,{
        type: 'CHANGE_CLIMATE'
      })
    ).toEqual(climateChangeState)
  })
```

Then add the __CHANGE_CLIMATE__ case, __updateStats__, and __calculateStats__ functions to teslaRangeApp.js. Then import the __BatteryService.js__ that was used in part1.


```
import { getModelData } from '../services/BatteryService';

function updateStats(state, newState) {
  return {
    ...state,
    config:newState.config,
    carstats:calculateStats(newState)
  }  
}

function calculateStats(state) {
  const models = ['60', '60D', '75', '75D', '90D', 'P100D'];
  const dataModels = getModelData();
  return models.map(model => {
    const { speed, temperature, climate, wheels } = state.config;
    const miles = dataModels[model][wheels][climate ? 'on' : 'off'].speed[speed][temperature];
    return {
      model,
      miles
    };
  });
}

function appReducer(state = initialState, action) {
  switch (action.type) {
    case 'CHANGE_CLIMATE': {
      const newState = {
        ...state,
        config: {
          climate: !state.config.climate,
          speed: state.config.speed,
          temperature: state.config.temperature,
          wheels: state.config.wheels
        }
      };
      return updateStats(state, newState);
    }
    default:
      return state
  }
}    
```

If you check the test results, you can see that the two test cases are successful.

![enter image description here](https://lh3.googleusercontent.com/dIjLLo1-FWnI3QjYtcsw6ULllFxqAr_FDepujQe39wFNCA7ISlpHAGziqVKBdMgaWCrueVKElg=s944 "npm test.png")

What we have implemented so far is that the changes in the state that occur when the user turns the air conditioner on and off in the application through the test runner only from the __viewpoint of Action and Reducer__ without Redux Store or View.

_![enter image description here](https://lh3.googleusercontent.com/TokzyESk55lNISeM0nS5IjNN2Xk6Tjv3KWGOVv-Zt5Z79hkMDXBOusHPMHzhFoWRQvDIFwyP1A=s1050 "reducer1.png")_


_![enter image description here](https://lh3.googleusercontent.com/2OtGE5flaL5zZGKXnoQpmJHHMsSw2yasxfshu1a5cKMU-ErslBc2RbYpWy6cV3njtmsgiuhy9w=s1050 "reducer.png")_


* Check out [teslaRangeApp.js](https://gist.github.com/gyver98/d0749fe0280f3d471f87305993167b97#file-teslarangeapp-js) we've written so far

* Check out [teslaRangeApp.spec.js](https://gist.github.com/gyver98/f482176b8c904a9ef1c64becb87b8023#file-teslarangeapp-spec-js)


### 9.3 Create Reducer for others

If you create the rest of the test cases by referring to the above method, you finally define the __teslaRangeApp.js__ file in which the reducers of all the apps are defined and the __teslaRangeApp.spec.js__ to test them.

The final code can be found at:

> [teslaRangeApp.js](https://gist.github.com/gyver98/2f8c3a8e7652de29c090818f6b7999ea#file-final-teslarangeapp-js)
[teslaRangeApp.spec.js](https://gist.github.com/gyver98/f18ce2f9d04cf2b762f5ec4c2d0f9418#file-final-teslarangeapp-spec-js)

After completing the code and testing, a total of seven test cases must succeed.

![enter image description here](https://lh3.googleusercontent.com/HHs8ASsrwlD7_4m2EUSAe5OdL-P5G1jmUufJjiqNzCdpkhJWkj4B4w2hzwv4WlTYtbHln5Y-1Q=s944 "test case.png")

## 10. The views: smart and dumb components

As mentioned in __5. Divide The App Into  in Containers and Components__, we will create __Presentational components__ (dumb components) for presentation purposes and a __Container components__ (smart components) which are wrapper component responsible for Actions while communicating with Redux.

Smart components are __responsible for the actions__. If a dumb component underneath them needs to trigger an action, the smart component will pass a function through props, and the dumb component can then treat that as a __callback__.

We already have dumb components for presentation purposes in part 1 and will reuse them.

Here we create container components as upper __wrapper__ around each dumb components.

### 10.1 The view layer binding
 
Redux needs some help to connect the store to the view. It needs something to bind the two together. This is called the __view layer binding__. In an app that uses react, this is <span class="bg-dark-gray white">react-redux</span>.

Technically, a container component is just a React component that uses __store.subscribe()__ to read a part of the Redux state tree and supply __props__ to a presentational component it renders.

Hence, we can manually create container components, but this is not recommended for Redux official docs. This is because __react-redux performs many performance optimizations__ that are difficult to perform manually. 

For this reason, instead of writing the container component by hand, we write it using the <span class="bg-dark-gray white">connect()</span> function provided by react-redux.

Let's install the necessary packages first.

* __npm install --save redux__
* __npm install --save react-redux__



### 10.2 TeslarCar Container

To use __connect()__, you need to define a special function called <span class="bg-dark-gray white">mapStateToProps</span>. This function tells you __how to convert the current Redux store state to props__ to be passed to the presentation component.

The TeslarCar container takes the wheelsize stored in the current store and passes it to props so that it can be rendered by the TeslarCar component. This props will be updated every time the state is updated.


 ![enter image description here](https://lh3.googleusercontent.com/F8H8wq4y-i-sOv3EaR2_Mdlh1vh9aiMs1UeEJ_fjm9LtaEziv8Wdp57F0uN3G8hHM8fzG2LOAQ=s944 "teslacar_cont.png")

After defining the __mapStateToProps__ function, we defined the __connect()__ function as shown below.

```
const TeslaCarContainer = connect(mapStateToProps, null)(TeslaCar)
```

Connect() accepts <span class="bg-dark-gray white">mapDispatchToProps</span> as the second argument, which takes the dispatch method of the store as its first argument. In the TeslaCar component, we do not need an action, so we have to pass null.

> 
Another parenthesis in __connect()()__ may look weird. This form actually means two function calls, the __first connect() returns another function__, and the __second function needs you to pass a React component__.
In this case it's our TeslaCar component.
This pattern is called __currying__ or __partial application__ and is a form of functional programming.

Create __src/containers/TeslaCarContainer.js__ and write the code.

> Check out [TeslaCarContainer.js](https://gist.github.com/gyver98/7fa2b19d0bf023200a196ff1ec26f5d5#file-teslarcarcontainer-js)

### 10.3 TeslaStats Container

As with the TeslaCar container, define only the __mapStatToProps__ function and pass it to connect() in TeslaStats container. 

![enter image description here](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaStatsContainer.png?raw=true)

Create __src/containers/TeslaStatsContainer.js__ and write the code.

> Check out [TeslaStatsContainer.js](https://gist.github.com/gyver98/065b988b03b0c823f7d8373f2235ec1e#file-teslastatscontainer-js)

### 10.4 TeslaSpeedCounter Container

The __TeslaSpeedCounter container__ defines an additional <span class="bg-dark-gray white">mapDispatchToProps</span> function to handle the user actions that occur in the TeslarSpeedCounter component.

_![enter image description here](https://lh3.googleusercontent.com/y6yxY-K0hrjYHzPvmGS8esNBFVjPyPl4DUG-JIAbVL9wb60NUDZ1g_K8hMEAaj243ptIC0kPUg=s1050 "tesla-counter-cont.png")_

Create __src/containers/TeslaSpeedCounterContainer.js__ and write the code.

> Check out [TeslaSpeedCounterContainer.js](https://gist.github.com/gyver98/f1758643b7a9f3a5bcae546abda5861d#file-teslaspeedcountercontainer-js)

### 10.5 TeslaTempCounter Container

The __TeslaTempCounter container__ is almost identical to the TeslaSpeedCounter except for the state and action creators being passed.

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaTempCounterContainer.png?raw=true)_

Create __src/containers/TeslaTempCounterContainer.js__ and write the code.

> Check out [TeslaTempCounterContainer.js](https://gist.github.com/gyver98/0986225c521d3213875a9849bf1e9d80#file-teslatempcountercontainer-js)

### 10.6 TeslaClimateContainer

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaClimateContainer.png?raw=true)_

Create __src/containers/TeslaClimateContainer.js__ and write the code.

> Check out [TeslaClimateContainer](https://gist.github.com/gyver98/bd677915a8b4ea68589497311c77eaee#file-teslaclimatecontainer-js)


### 10.7 TeslaWheelsContainer

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaWheelsContainer.png?raw=true)_

Create __src/containers/TeslaWheelsContainer.js__ and write the code.

> Check out [TeslaWheelsContainer.js](https://gist.github.com/gyver98/2bc410b7c7aa07ac4def49702ba21738#file-teslawheelscontainer-js)

We have created the container components corresponding to the presentation components generated in part 1 through connect() of react-redux.

## 11. Provider

Let's put together all the things we've done so far and make our apps work. So far we have defined __action objects__ and created __action creators__ that create action objects. And when an action occurs, we have created __reducers__ that actually treat and return a new state. We then created a __container component__ that connects each of the presentation components to the Redux store.

Now every container component needs a way to access the store, which is what the <span class="bg-dark-gray white">Provider</span> does. The Provider component __wraps the entire application and allows subcomponents to communicate with the store via connect()__.

The top-level component of our app, __App.js__, looks like this:

> Check out [App.js](https://gist.github.com/gyver98/46b3929798503d057bf23e64a72c2011#file-app-js)

_![enter image description here](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/App%20layout.png?raw=true)_


## 12. How they all work together

Finally, all the puzzle pieces were completed. Now let's look at the following animation as an example of when all the puzzle pieces are tied together and the user triggers the __speed up__ event.
 
_![enter image description here](https://lh3.googleusercontent.com/beUr8-FlmI4IcxZ6XPcLVMtaq3nz_cRxOEhYnvWEBLJGP82N-HHjzHw-wcw3HovVx8ExiBq5Bw=s1100 "redux flow.gif")_


Now run __npm start__ and it will be compiled normally and the application will be started.

But there are still a few things to do.
 
* First, copy all the contents of __/containers/TeslaBattery.css__ that you created in part 1 and add them to __App.css__.

> Check out [App.css](https://gist.github.com/gyver98/fb061ac3997b055bf4628dcfdd83cb51#file-app-css)

* Next, open __/components/TeslaCounter/TeslaCounter.js__ and modify the __onClick__ event handler as follows:
This is because part 2 no longer handles event handling in __TeslaBattery.js__.

```
onClick={(e) => props.increment(e, props.initValues.title)}
-->
onClick={(e) => {
  e.preventDefault();
  props.increment(props.currentValue)}}

onClick={(e) => props.decrement(e, props.initValues.title)}
-->
onClick={(e) => {
  e.preventDefault();
  props.decrement(props.currentValue)}} 
```

* Next, let's not use props repeatedly by using ES6 <span class="bg-dark-gray white">Object destructuring</span>.

```
const TeslaCounter = (props) => (
  <p className="tesla-counter__title">{props.initValues.title}</p>
  ...

-->
const TeslaCounter = ({ initValues, currentValue, increment, decrement }) => (
  <p className="tesla-counter__title">{initValues.title}</p>
  ...
```


> Check out [TeslaCounter.js](https://gist.github.com/gyver98/5c7f4755023643a84dc7514209f22997#file-teslacounter-js)


Finally, the __Redux version of Tesla Battery Range Calculator app__ is completed!

## 13. One more thing : Redux Dev Tools

The <span class="bg-dark-gray white">Redux Dev Tool</span> makes it much easier to view Redux state tracking and take advantage of cool features like time travel debugging.

We'll install it on Chrome here.

* Chrome extenstion [install](https://www.google.com.au/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwijoqLQxdzSAhUEspQKHaEDA0AQFggZMAA&url=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Fredux-devtools%2Flmhkpmbekcpmknklioeibfkpmmfibljd%3Fhl%3Den&usg=AFQjCNFg4ldS78uapjCGBaNjL9NvIwZGhg&sig2=YuyPlshxe2eVaKrx0ReXfQ&bvm=bv.149760088,d.dGo)

* Add for Redux store

Open the __App.js__ file and modify the __creatStore__ part as follows:

```
const store = createStore(appReducer);
-->
const store = createStore(appReducer, window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__());
```

* Check on browser

_![enter image description here](https://github.com/gyver98/gyver98.github.io/blob/master/images/redux%20dev%20tools.gif?raw=true)_

> Check out [final project code](https://github.com/gyver98/redux-tesla-battery-range-calculator-tutorial)

> Check out [live demo](http://redux-tesla-charge-calculator.surge.sh/)


## Resources
* [Redux docs](http://redux.js.org/docs/introduction/) 
* [A cartoon intro to Redux](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6#.4j7d5vz4l) 
* [Leveling Up with React: Redux](https://css-tricks.com/learning-react-redux/) 
* [Getting Started with Redux](https://egghead.io/courses/getting-started-with-redux) 



{% include disqus.html %}




