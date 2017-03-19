---
layout:     post
title:      Building Tesla's battery range calculator with React Part 2 (Redux version)
date:       2017-03-17 10:35:29
summary:    Rebuilding Tesla's Battery Range Calculator with React/Redux 
categories: blog development react
---

This tutorial is the second part of building Tesla's battery range calculator with React.
In the part 1, after constructing the project through create-react-app, we implemented each component by subdividing the UI and managed the state and event using local state and props, and completed the entire app.

> 
Check out the [part 1](https://gyver98.github.io/blog/development/react/2017/02/09/react-tesla-battery-range-calculator-part1-korean/)

In this part, we will introduce <span class="bg-dark-gray white">Redux</span>, a state management solution, to see how we can transform our application into an application that manages the state of the app with Redux.

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

Redux is a state management library, which is a great tool that allows you to store the state of our app somewhere, mutate the state, and receive the updated state.
In other words, with Redux, we have one place where we can refer the state, change the state, and get the updated state. 
Redux also works well with React, but it is also framework-agnostic and even can be used with Angular or jQuery applications.

>
It is recommended that you read Dan Abramov's [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367#.uz11a0vkc) before choosing Redux.

## 2. Data flow in Redux

As you saw in the part 1, in React, the data is passed through the component using props.
This is called __unidirectional data flow__ that flows from parent to child.
Due to these characteristics, communication between components other than parent-child relationship is not clear.

![enter image description here](https://lh3.googleusercontent.com/42IhN3FAY3DSZ866Z1klFF2zK7OPQ-Ufqd-k1YqS_Kdklem4O1nwSYIYm-u-UUgkZUpw4hSNTA=s944 "data flow1.png")

React does not recommend direct component-to-component communication as shown above.
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
Actions are plain objects describing __what happened__ in the app, and serve as the sole way to describe an __intention to mutate the data__. It's one of the __fundamental design choices__ of Redux.
Here are some examples to be implemented in our app soon.


![enter image description here](https://lh3.googleusercontent.com/1zQwJBmCRp4X9YPAu4Mu0TlTNuSxUepDXMp8jIz2cA74xbRs7dTjJwmF4jEzbzeQTBl09YDzfQ=s944 "actions.png")

Forcing all of these state changes into action will give us a clear understanding of what's going on in your app.
When something happens, we can see why it happened.

Now we need a function called <span class="bg-dark-gray white">reducer</span> to bind these states and actions together. 
Reducer is nothing more than a function that takes a state and an action as arguments and returns a __new state__. 

In a word:

> 
(state, action) => state 

Actions only describe that something happened and don't specify how the application's state changes in response.
This is the job of reducers.
Here is one example of a reducer to implement in our app.


![enter image description here](https://lh3.googleusercontent.com/RVYAdVRC71UVO2mQLcGm7_3tkC1O1zZ7ougM45iZt9X8Rzxnd0Cktu4q0gW8zEoNNDt5Oc2XAQ=s944 "reducer.png")


## 4. Redux Three Principles

I have mentioned <span class="bg-dark-gray white">Flux</span> for a while.
Flux is a pattern of state management, not a downloadable tool like Redux. Redux, on the other hand, is a practical implementation of the Flux pattern and has three main principles.

### 4.1 Single source of truth

>
The state of the entire application is stored in an object tree within a single store.

![state tree](https://lh3.googleusercontent.com/BKBEU-IaqTlTxZ3VkAGHen5-bGTQpcKhknNdt2TRfD5FIW4n-YO2BpsUHsC-QA3CYtNxYm6GMw=s944 "state tree.png")  

Since all states exist in one place, this is called a <span class="bg-dark-gray white">single source of truth</span>.
This <span class="bg-dark-gray white">one-store</span> approach of Redux is one of the primary differences between it and __Flux's multiple store__ approach. 

What are the advantages of a single state tree? This makes it easier to debug applications or perform internal inspections and to easily implement some features that were previously difficult to implement (for example, undo / redo).

### 4.2 State is read-only

> The only way to change the state is to emit an action that describes what happened. 

In other words, the application does not directly change the state, but instead expresses the intention to transform the state by passing the action.

In fact, if you look at the Redux API, you can see that it consists of just four methods.

```
store.dispatch(action)
store.subscribe(listener)
store.getState()
replaceReducer(nextReducer)
```

As you can see, there is no __setState()__ method. Therefore, passing an action is the only channel that can mutate the state of the application.

### 4.3 Changes are made with pure functions

>
You write reducers as pure functions to specify the concrete way the state tree is transformed by action.

Reducers are pure functions that take a previous state and action and return a new state.
Keep in mind that you must return a <span class="bg-dark-gray white">new state</span> object instead of changing the old state.

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

First, let's look at the overall component UI layout of the app that will be implemented in this article.

_![enter image description here](https://lh3.googleusercontent.com/YlCnB9gS8_KJgCfW1d8Qb8Ws01zOzj-huYRxu35Vqx6wnH9UJm_LCa85suCubWlYlDC8keXUMw=s1100 "compoent layout.png")_
 

Placing React and Redux logic inside a single component can be messy, so it is recommended that you create a <span class="bg-dark-gray white">Presentational component</span> for presentation purposes only, and a <span class="bg-dark-gray white">Container component</span>, an upper wrapper component that handles Redux and dispatches actions.

The role of the parent Container component is to provide state values to presentational components, to manage events, and to communicate with Redux on behalf of presentational components.

## 6. List State and Actions For Each Component
전체 컴포넌트 레이아웃을 참조 하여 각 컴포넌트의 state와 action리스트를 만들도록 한다.

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
	


## 7. Create Action Creators For Each Action

>
코딩을 시작하기에 앞서 part1에서 완성했던 코드베이스가 필요하므로 part1을 보지 않고 바로 part2를 진행하기를 원한다면 [여기서](https://github.com/gyver98/part1-react-tesla-battery-range-calculator-tutorial) 코드를 클론하여 먼저 코드 베이스를 구축하도록 한다.

npm start 후 애플리케이션이 제대로 동작하는지 확인하자.

이제 action 리스트를 만들었으니 <span class="bg-dark-gray white">action creators</span>를 만들 차례이다. action creators는 말 그대로 action을 만들어주는 함수이다. Redux에서 action crators는 단지 action 오브젝트를 리턴하고 필요하면 인자값을 전달해준다.

```
const changeWheel = (value) => {
  return {
    type: 'CHANGE_WHEEL',
    value
  }
}
```
이러한 action creators는 dispatch 함수에 결과값으로 전달되어 dispatch가 동작하게 된다.

```
dispatch(changeWheel(size))
```

dispatch 함수는 store.dispatch로 직접 액세스 할 수도 있지만 대개의 경우 <span class="bg-dark-gray white">react-redux</span>의 connect와 같은 helper를 사용하여 액세스 하게된다. <span class="bg-dark-gray white">connect</span>에 대해서는 조금 뒤에 살펴보도록 하겠다. 

### 7.1 Create Action.js

src/actions 디렉토리안에 index 파일을 만들고 다음과 같이 action creators를 정의한다.

src/actions/index.js

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
* index.js 의 코드는 [여기서](https://gist.github.com/gyver98/9d088084834ec6a0f893c8576c7d9204#file-index-js) 확인 할 수 있다.

action creator에 따라 defalut values가 필요하므로 우리는 이를 src 디렉토리 아래 constants/counterDefaultVal에 이 상수값을 정의한 후 import 하여 사용하도록 한다.

/src/constants/counterDefaultVal.js

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

* counterDefaultVal.js 의 코드는 [여기서](https://gist.github.com/gyver98/e560ca69057d40e0688000b94d7c0fd9#file-counterdefaultval-js) 확인 할 수 있다.


## 8. Create Reducers For Each Action
Reducers는 Redux store에서 받아온 state와 action오브젝트를 받아서 Redux에 다시 저장될 새로운 state를 반환하는 함수이다. 여기서 주어진 state를 직접 수정하지 않는 것이 중요하다. Reducers는 순수 함수이어야만 하고 그래서 새로운 state를 리턴해야만 한다.

* Reducer functions는 사용자 action이 발생할 때 앞으로 만들게 될 Container에서 호출되어진다. 
* Reducer가 state를 변경하게 되면, Redux는 새로운 state를 각 컴포넌트에 전달하고 React는 각 컴포넌트를 다시 렌더링하게 된다.

### 8.1 Immutable Data Structures

* 자바스크립트 primitive 데이타 타입(number, string, boolean, undefined, and null) => <span class="bg-dark-gray white">immutable</span>
* Object, array and function => <span class="bg-dark-gray white">mutable</span>

데이터 구조의 변경은 버그가 발생하기 쉬운 것으로 알려져 있다.
우리 store는 state 오브젝트와 배열로 이루어지기 때문에 state를 변경하지 못하게하는 전략을 구현해야만한다.

여기 state를 변경하는 세 가지 방법이 있다:

__ES5__

```
// Example One
state.foo = '123';

// Example Two
Object.assign(state, { foo: 123 });

// Example Three
var newState = Object.assign({}, state, { foo: 123 });
```

위의 예제에서 첫번째, 두번째는 state 오브젝트를 변경한다. 두번째 예제에서는 Object.assign()이 모든 인자값을 첫번째 인자인 원래 state와 병합함으로서 변경이 이루어진다.

세번째 예제에서는 state와 { foo: 123 }이 첫번째 인자인 새로운 오브젝트에 병합이 되므로 원래의 state를 변경하지 않고 새로운 값으로 오브젝트의 복사본을 만들게된다.

ES6에서 도입된 <span class="bg-dark-gray white">spread operator</span>는 state를 변경하지 않는 보다 간결한 방법을 제공한다.

__ES6 (ES2015)__

```
const newState = { ...state, foo: 123 };
```

> 
spread operator에 대한 자세한 내용은 [여기](http://redux.js.org/docs/recipes/UsingObjectSpreadOperator.html)를 참조

### 8.2 Create Reducer for change climate

먼저 우리가 만들어 볼 예제는 ChangeClimate로 테스트 주도 개발 방식으로 만들어보겠다.
Part1에서 우리의 앱은 <span class="bg-dark-gray white">create react app</span>을 통해 만들어졌기 때문에 기본적으로 test runner로 <span class="bg-dark-gray white">jest</span>를 사용하게 된다. 

jest는 다음의 명명 규칙중 하나를 사용하여 테스트 파일을 찾는다.

```
Files with .js suffix in __tests__ folders
Files with .test.js suffix
Files with .spec.js suffix
```

src/reducers 디렉토리를 만들고 teslaRangeApp.spec.js 생성한뒤 테스트를 작성한다.

```
describe('test reducer', () => {
  it('should handle initial stat', () => {
    expect(
      appReducer(undefined, {})
    ).toEqual(initialState)
  })
})
```

테스트를 작성한 후 npm test 명령어를 실행하자. 아래와 같은 테스트 실패 메시지를 볼 수 있어야 한다. 왜냐하면 아직 appReducer를 작성해서 넘기지 않았기 때문이다.
 
![enter image description here](https://lh3.googleusercontent.com/gxuCGjMSqDK92QZeBN1Pfg0tS4ErO49MISJwasZFoKq3pAHSTrrox1uQfyfUL9gT-Amz_1i0KA=s1100 "npm-test.jpg")

첫번째 테스트를 성공시키기 위해 같은 reducers 디렉토리 안에 teslaRangeApp.js를 생성하고 inital state와 reducer 함수를 작성해야 한다.

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
그 다음에  teslaRangeApp.spec.js에서 teslaRangeApp을 import 하고 initialState를 설정하자.

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

다시 npm test를 실행하면 테스트가 성공 할 것이다. 현재의 테스트 케이스에서는 action type이 {} 이기 때문에 initialState 가 리턴되었다.

![enter image description here](https://lh3.googleusercontent.com/KIVhW-zKx7br3A801T6AcsPuCUu2YuJiqHAYDLvT4nfQZMwjiGM3LVKFyQeQNuMK1PqOxkGncA=s1100 "npm test2.png")

이제 CHANGE_CLIMATE action을 테스트 해보자.
teslaRangeApp.spec.js에 아래의 climateChangeState와 CHANGE_CLIMATE 테스트 케이스를 추가한다.


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

그 다음에 teslaRangeApp.js에 CHANGE_CLIMATE 케이스와 updateStats, calculateStats 함수를 추가한다. 그리고 part1에서 사용했던 BatteryService.js를 import한다.


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

테스트 결과를 확인해보면 두 개의 테스트 케이스가 성공한것을 볼 수 있다.

![enter image description here](https://lh3.googleusercontent.com/dIjLLo1-FWnI3QjYtcsw6ULllFxqAr_FDepujQe39wFNCA7ISlpHAGziqVKBdMgaWCrueVKElg=s944 "npm test.png")

지금까지 우리가 구현한 것은 전체 애플리케이션에서 사용자가 에어콘을 on/off 했을때 발생할 상태의 변화를 Redux Store나 View 없이 Action과 Reducer 관점에서만 test runner를 통해 구현해본것이다.

_![enter image description here](https://lh3.googleusercontent.com/TokzyESk55lNISeM0nS5IjNN2Xk6Tjv3KWGOVv-Zt5Z79hkMDXBOusHPMHzhFoWRQvDIFwyP1A=s1050 "reducer1.png")_


_![enter image description here](https://lh3.googleusercontent.com/2OtGE5flaL5zZGKXnoQpmJHHMsSw2yasxfshu1a5cKMU-ErslBc2RbYpWy6cV3njtmsgiuhy9w=s1050 "reducer.png")_


지금까지 작성한 teslaRangeApp.js는 [여기](https://gist.github.com/gyver98/d0749fe0280f3d471f87305993167b97#file-teslarangeapp-js)에서 
테스트 코드는 [여기](https://gist.github.com/gyver98/f482176b8c904a9ef1c64becb87b8023#file-teslarangeapp-spec-js)에서 확인할 수 있다.


### 8.3 Create Reducer for others

위에서 한 방식을 참고해서 나머지 테스트 케이스들을 만들게되면 최종적으로 우리 전체앱의 reducers가 정의된 teslaRangeApp.js 파일과 이를 테스트하는 teslaRangeApp.spec.js를 정의하게 된다.

최종코드는 다음에서 확인 할 수 있다.

* [teslaRangeApp.js](https://gist.github.com/gyver98/2f8c3a8e7652de29c090818f6b7999ea#file-final-teslarangeapp-js)
* [teslaRangeApp.spec.js](https://gist.github.com/gyver98/f18ce2f9d04cf2b762f5ec4c2d0f9418#file-final-teslarangeapp-spec-js)

코드를 완성 후 테스트를 했을때 총 7개의 테스트 케이스가 성공해야 한다.

![enter image description here](https://lh3.googleusercontent.com/HHs8ASsrwlD7_4m2EUSAe5OdL-P5G1jmUufJjiqNzCdpkhJWkj4B4w2hzwv4WlTYtbHln5Y-1Q=s944 "test case.png")

## 9. The views: smart and dumb components
이미  <span class="bg-dark-gray white">5. Divide The App Into Containers and Components</span> 에서 언급했듯이 우리의 애플리케이션은 프리젠테이션 전용 목적의 Presentational 컴포넌트 (<span class="bg-dark-gray white">dumb component</span>) 와 Redux와 커뮤니케이션 하면서 Actions를 담당하는 상위 Wrapper 컴포넌트인 Container 컴포넌트 (<span class="bg-dark-gray white">smart component</span>)를 만들것이다.

smart component는 action을 담당하는데, 만일 하위에 있는 dumb component에서 어떤 action이 필요한 경우
smart component에서는 props를 통해 함수를 전달하게되고, dumb component는 이를 콜백으로 처리하게 된다.

이미 part 1 에서 프리젠테이션 목적의 dumb 컴포넌트들은 만들어 놓았으므로 이를 재사용할것이다.
여기서는 각각의 dumb 컴포넌트들을 감싸는 상위 wrapper로서 container 컴포넌트를 생성하도록 한다.

### 9.1 The view layer binding
store를 view에 연결하기위해서 Redux는 약간의 도움이 필요하다. 두 가지를 하나로 묶을 뭔가가 필요한데 react를 사용하는 앱에서는 이것이 바로 <span class="bg-dark-gray white">react-redux</span>이다. 기술적으로, 컨테이너 컴포넌트는 store.subscribe()를 사용하여 Redux state 트리의 일부를 읽고 프리젠테이션 컴포넌트에 그 상태를 렌더링 할 수 있도록 props로 제공하는 React 컴포넌트일뿐이다. 따라서 우리는 직접 수작업으로 container 컴포넌트를 작성할 수도 있지만 이것은 Redux 공식 문서에 의하자면 권장되지 않는다. 왜냐하면 react-redux는 수작업으로 수행하기 어려운 많은 성능 최적화를 수행하기 때문이다. 이러한 이유로 우리는 직접 container 컴포넌트를 작성하는 대신에 react-redux에서 제공되는 <span class="bg-dark-gray white">connect()</span> 함수를 이용하여 작성하도록 한다.

먼저 필요한 패키지들을 설치하자.

* __npm install --save redux__
* __npm install --save react-redux__


### 9.2 TeslarCar Container
connect()를 사용하려면 <span class="bg-dark-gray white">mapStateToProps</span>라는 특별한 함수를 정의해야한다. 이 함수는 현재의 Redux store 상태를 프리젠테이션 컴포넌트에 전달할 props로 변환하는 방법을 알려준다. TeslarCar 컨테이너는 현재 store에 저장된 wheelsize를 가져와 TeslarCar 컴포넌트에서 이를 렌더링 할 수 있도록 props로 전달한다. 이 props는 state가 갱신될때마다 업데이트 되어질것이다.


 ![enter image description here](https://lh3.googleusercontent.com/F8H8wq4y-i-sOv3EaR2_Mdlh1vh9aiMs1UeEJ_fjm9LtaEziv8Wdp57F0uN3G8hHM8fzG2LOAQ=s944 "teslacar_cont.png")

mapStateToProps 함수를 정의한 후, 아래와 같이 connect() 함수를 정의했다.

```
const TeslaCarContainer = connect(mapStateToProps, null)(TeslaCar)
```

connect()는 두번째 인자로 store의 dispatch 메소드를 첫번째 인자로 받는 <span class="bg-dark-gray white">mapDispatchToProps</span>를 넘길 수 있는데 TeslaCar 컴포넌트에서는 액션이 필요하지 않으므로 null을 넘기도록 했다.

> 
connect()()에서 보여지는 또 하나의 괄호는 이상하게 보일 수도 있다. 이러한 형태는 사실 두 개의 함수 호출을 의미하는데, 첫번째 connect()는 또 다른 함수를 리턴하고 두 번째 함수에서는 React 컴포넌트를 전달해주어야 한다.
여기서는 TeslaCar 컴포넌트가 전달되고 있다. 이러한 패턴은 currying 또는  partial application이라 불리우며 functional programing의 한 형태이다.

/src/containers/TeslaCarContainer.js를 생성하고 코드를 작성하자.

* TeslaCarContainer 의 코드는 [여기서](https://gist.github.com/gyver98/7fa2b19d0bf023200a196ff1ec26f5d5#file-teslarcarcontainer-js) 확인 할 수 있다.

### 9.3 TeslaStats Container

TeslaStats 컨테이너도 TeslaCar 컨테이너와 마찬가지로 mapStatToProps함수만 정의하여 connect()에 전달하도록 한다. 

![enter image description here](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaStatsContainer.png?raw=true)

/src/containers/TeslaStatsContainer.js를 생성하고 코드를 작성하자.

* TeslaStatsContainer의 코드는 [여기서](https://gist.github.com/gyver98/065b988b03b0c823f7d8373f2235ec1e#file-teslastatscontainer-js) 확인 할 수 있다.

### 9.4 TeslaSpeedCounter Container

TeslaSpeedCounter 컨테이너에서는 TeslarSpeedCounter 컴포넌트에서 발생하는 사용자 액션을 처리할 수 있도록 <span class="bg-dark-gray white">mapDispatchToProps</span> 함수를 추가적으로 정의하도록 한다.

_![enter image description here](https://lh3.googleusercontent.com/y6yxY-K0hrjYHzPvmGS8esNBFVjPyPl4DUG-JIAbVL9wb60NUDZ1g_K8hMEAaj243ptIC0kPUg=s1050 "tesla-counter-cont.png")_

/src/containers/TeslaSpeedCounterContainer.js를 생성하고 코드를 작성하자.

* TeslaSpeedCounterContainer의 코드는 [여기서](https://gist.github.com/gyver98/f1758643b7a9f3a5bcae546abda5861d#file-teslaspeedcountercontainer-js) 확인 할 수 있다.

### 9.5 TeslaTempCounter Container
TeslaTempCounter 컨테이너는 전달되는 state와 action creators를 제외하고 TeslaSpeedCounter 와 거의 동일하다.

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaTempCounterContainer.png?raw=true)_

/src/containers/TeslaTempCounterContainer.js를 생성하고 코드를 작성하자.

* TeslaTempCounterContainer의 코드는 [여기서](https://gist.github.com/gyver98/0986225c521d3213875a9849bf1e9d80#file-teslatempcountercontainer-js) 확인 할 수 있다.

### 9.6 TeslaClimateContainer

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaClimateContainer.png?raw=true)_

다음의 파일을 생성하고 코드를 완성하자.
/src/containers/TeslaClimateContainer.js

* TeslaClimateContainer의 코드는 [여기서](https://gist.github.com/gyver98/bd677915a8b4ea68589497311c77eaee#file-teslaclimatecontainer-js) 확인 할 수 있다.


### 9.7 TeslaWheelsContainer

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/TeslaWheelsContainer.png?raw=true)_

다음의 파일을 생성하고 코드를 완성하자.
/src/containers/TeslaWheelsContainer.js

* TeslaWheelsContainer의 코드는 [여기서](https://gist.github.com/gyver98/2bc410b7c7aa07ac4def49702ba21738#file-teslawheelscontainer-js) 확인 할 수 있다.

이로서 part 1 에서 생성했던 각각의 프리젠테이션 컴포넌트들에 대응하는 컨테이너 컴포넌트들을 react-redux의 connect()를 통해 만들어보았다.

## 10. Provider

이제 지금까지 만들었던 모든 것들을 한데 모아 우리의 앱이 작동하도록 만들어보자.
지금까지 우리는 action 오브젝트를 정의했고, action 오브젝트를 만들어주는 action creators를 생성했다. 그리고 action이 발생하면 실제 처리하고 새로운 state를 리턴하는 reducers를 만들었다. 그 다음에 각각의 프리젠테이션 컴포넌트를 Redux store에 연결해주는 컨테이너 컴포넌트를 작성하였다.

이제 모든 컨테이너 컴포넌트에서 store에 접근 할 수 있는 방법이 필요한데, <span class="bg-dark-gray white">Provider</span>가 바로 그 역할을 수행한다.
Provider 컴포넌트는 전체 애플리케이션을 감싸면서 하위 컴포넌트들이 connect()를 통해서 store와 커뮤니케이션 할 수 있도록 한다.

우리 앱의 최상위 컴포넌트인 App.js는 다음과 같다.

* 코드는 [여기서](https://gist.github.com/gyver98/46b3929798503d057bf23e64a72c2011#file-app-js) 확인할 수 있다

_![enter image description here](https://github.com/gyver98/blog-images/blob/master/2017-03-05-react-tesla-battery-range-calculator-part2-korean/App%20layout.png?raw=true)_


## 11. How they all work together

드디어 모든 퍼즐 조각이 완성되었다. 이제 이 모든 퍼즐 조각들이 한데 묶여 어떻게 동작하는지 사용자가 speed up 이벤트를 발생시켰을때의 경우를 예로 다음의 애니메이션에서 확인해보자.
 
_![enter image description here](https://lh3.googleusercontent.com/beUr8-FlmI4IcxZ6XPcLVMtaq3nz_cRxOEhYnvWEBLJGP82N-HHjzHw-wcw3HovVx8ExiBq5Bw=s1100 "redux flow.gif")_


이제 npm start를 해보면 정상적으로 컴파일이 되어 애플리케이션이 기동될것이다.

하지만 아직 몇 가지 할 일이 남아있다.
 
* 먼저 part 1에서 작성했던 /containers/TeslaBattery.css 의 모든 내용을 카피해서 App.css에 추가하도록 한다.

App.css 코드는 [여기서](https://gist.github.com/gyver98/fb061ac3997b055bf4628dcfdd83cb51#file-app-css) 확인 할 수 있다.

* 다음으로 /components/TeslaCounter/TeslaCounter.js를 열고 onClick 이벤트 핸들러를 다음과 같이 수정한다.
왜냐하면 part 2에서는 더 이상 TeslaBattery.js 에서 이벤트 핸들링을 하지 않기 때문이다.

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


* 다음으로 ES6 <span class="bg-dark-gray white">Object destructuring</span>을 이용하여 props를 반복적으로 사용하지 않도록 해보자.

```
const TeslaCounter = (props) => (
  <p className="tesla-counter__title">{props.initValues.title}</p>
  ...

-->
const TeslaCounter = ({ initValues, currentValue, increment, decrement }) => (
  <p className="tesla-counter__title">{initValues.title}</p>
  ...
```


* TeslaCounter.js 코드는 [여기서](https://gist.github.com/gyver98/5c7f4755023643a84dc7514209f22997#file-teslacounter-js) 확인 할 수 있다.


드디어 Redux 버전의 Tesla Battery Range Calculator 앱이 완성되었다!!

## 12. One more thing : Redux Dev Tools

<span class="bg-dark-gray white">Redux Dev Tool</span>을 사용하면 Redux state tracking을 훨씬 쉽게 볼 수 있고 time travel debugging과 같은 멋진 기능도 활용할 수 있다.

여기서는 Chrome 기준으로 살펴보겠다.

* Chrome extenstion [install](https://www.google.com.au/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwijoqLQxdzSAhUEspQKHaEDA0AQFggZMAA&url=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Fredux-devtools%2Flmhkpmbekcpmknklioeibfkpmmfibljd%3Fhl%3Den&usg=AFQjCNFg4ldS78uapjCGBaNjL9NvIwZGhg&sig2=YuyPlshxe2eVaKrx0ReXfQ&bvm=bv.149760088,d.dGo)

* Add for Redux store

App.js 파일을 열고 creatStore 부분을 다음과 같이 수정한다.

```
const store = createStore(appReducer);
-->
const store = createStore(appReducer, window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__());
```

* 브라우저에서 확인

_![enter image description here](https://github.com/gyver98/gyver98.github.io/blob/master/images/redux%20dev%20tools.gif?raw=true)_

>
최종 프로젝트 코드는 [여기서](https://github.com/gyver98/redux-tesla-battery-range-calculator-tutorial) 확인할 수 있다.






{% include disqus.html %}




