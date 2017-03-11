---
layout:     post
title:      Building Tesla's battery range calculator with React Part 2
date:       2017-03-05 11:20:19
summary:    Rebuilding Tesla's Battery Range Calculator with React 
categories: blog development react
---

이번 튜토리얼은  React로 Tesla's battery range calculator 만들기의 두번째 파트이다.
첫번째 파트에서는 create-react-app 을 통해 프로젝트를 구성한 후 UI를 세분화하여 각각의 컴포넌트를 구현하고 state와 props를 이용하여 상태와 이벤트를 관리하여 전체 앱을 완성하였다.

> 파트 1은 [여기서](https://gyver98.github.io/blog/development/react/2017/02/09/react-tesla-battery-range-calculator-part1-korean/) 확인할 수 있다.

이번 파트에서는 상태관리 솔루션인 Redux를 도입하여 어떻게 앱의 상태를 관리하는 애플리케이션을 만들 수 있는지 살펴보겠다.

`Redux`가 무었인지 알아보기전에 왜 `Redux`를 사용해야 하는지 어떤 문제를 해결하기 위해 필요한건지 먼저 알아보자.

## 1. 우리가 해결하고자 하는 문제는 무었인가

`Redux`의 인기는 날로 더해져 이제 `React` 앱 개발의 표준이 되어버린듯하다. 그렇다고 모든 `React` 앱에서 `Redux`를 사용해야할까? 적어도 모든 앱이 처음부터 거창한 상태관리 솔루션을 필요로 하진 않을 것이다. 요즘의 프론트엔드 개발 트렌드는 컴포넌트 기반이라 할 수 있는데 컴포넌트는 데이타 상태와 UI 상태를 갖을 수 있으며 앱이 커질수록 관리해야 하는 상태는 점점 복잡해지기 마련이다. 아래와 같은 문제를 해결하기 위해 상태관리 솔루션이 등장하였고 `Redux`는 그러한 여러 솔루션 중에 표준처럼 여겨질만큼 인기를 얻고 있다. 

```
 - 컴포넌트들은 상태를 공유해야한다
 - 상태는 어디서든 접근할 수 있어야 한다
 - 컴포넌트는 상태를 변경할 필요가 있다
 - 컴포넌트는 다른 컴포넌트의 상태를 변경할 필요가 있다
```

Redux는 상태관리 라이브러리로서 우리앱의 상태를 어딘가에 저장할 수 있게 하고, 상태를 변경하게 해주며 변경된 상태를 사용할 수 있도록 도와주는 훌륭한 도구이다. 즉 Redux를 통해 우리는 상태를 찾고, 상태를 변경하며, 업데이트된 상태를 얻을 수 있는 단 하나의 장소를 갖게되는 것이다.

또한 Redux는 React와 궁합이 잘 맞지만 프레임웍과 무관하기때문에 Angular 또는 jQuery 애플리케이션에서도 사용할 수 있다.

## 2. Data flow in Redux

지난번 포스트에서 본 것 처럼, React에서는 컴포넌트를 통해 데이타가 전달된다. 즉, 부모에서 자식으로 흐르는 단방향 데이타 흐름인것이다. 이런 특성상 부모-자식 관계가 아닌 컴포넌트끼리의 커뮤니케이션은 명확하지가 않다.

![enter image description here](https://lh3.googleusercontent.com/42IhN3FAY3DSZ866Z1klFF2zK7OPQ-Ufqd-k1YqS_Kdklem4O1nwSYIYm-u-UUgkZUpw4hSNTA=s944 "data flow1.png")

React에서는 위의 그림처럼 직접적인 component-to-component 커뮤니케이션을 권장하지 않는다. React에서 이를 위해 제안하는 방법이 있으나 직접 구현해야만 한다. React docs에 따르면 :

>
For communication between two components that don't have a parent-child relationship, you can set up your own global event system. ... Flux pattern is one of the possible ways to arrange this.
부모 - 자식 관계가 아닌 컴포넌트 커뮤니케이션의 경우, 자체 전역 이벤트 시스템을 설정할 수 있다. ... Flux 패턴은 이것을 가능하게 하는 방법 중 하나이다.

![enter image description here](https://lh3.googleusercontent.com/_QVuP6ZcvHsh2eeUoy_uHZorb2Ay3R9tB3CPqt7sAkSH8W548aTH57Petf8ppahrN7puOy7YKg=s944 "redux flow.png")	

이 부분이 바로 Redux가 유용한 지점이다. 
Redux는 "store"라 불리우는 한 곳에서 모든 애플리케이션 상태를 저장하는 솔루션을 제공한다. 그런 다음 컴포넌트는 상태 변경을 다른 컴포넌트에 직접 전달하지 않고 스토어로 "dispatch"한다. 상태 변경을 인식해야하는 컴포넌트는 스토어를 "subscribe" 하여 업데이트를 할 수 있다.

>
Redux는 한 마디로 자바스크립트 기반의 앱에서 싱글 오브젝트로 앱의 상태를 표현하고 관리하는 상태 컨테이너라 할 수 있다.

##3. Redux Core Concept
Redux 자체는 아주 심플하다. 우리가 지난 포스팅에서 만들었던 앱의 상태는 다음과 같이 일반 오브젝트로 표현될 수 있다. 

![enter image description here](https://lh3.googleusercontent.com/kF94wMUWGh2Xv7LxamrX-JlwBYdCCq0pKjZvzfKzUvt4Dpc58u_gX0C9zIAnuTyP52lSxXK0iA=s944 "state object.png")
이 오브젝트는 `setters`가 없는 `model`과 같다.
Redux에서 이 상태를 변경하기 위해서는 `action`을 `dispatch`해야 한다. `action`은 무슨 일이 일어나는지를 묘사하는 일반 자바스크립트 오브젝트이다. 여기 앞으로 우리 앱에서 구현할 몇 가지 예가 있다.


![enter image description here](https://lh3.googleusercontent.com/1zQwJBmCRp4X9YPAu4Mu0TlTNuSxUepDXMp8jIz2cA74xbRs7dTjJwmF4jEzbzeQTBl09YDzfQ=s944 "actions.png")

이처럼 모든 상태 변화를 액션으로 강제하는 것은 앱에서 무슨 일이 일어나는지에 대한 명료한 이해를 갖게 만들어준다. 무슨 일이 생기면 왜 그런 변화가 일어났는지 알 수 있게 되는 것이다.

이제 이러한 `state`와 `action`을 한데 묶기 위해 `reducer`라 불리우는 함수가 필요하다. 
reducer는 단지 `state`와 `action`을 인자로 받아 새로운 `state`을 리턴하는 함수에 지나지 않는다. 이를 한 마디로 표현하면 :

> 
(state, action) => state 

`action`은 어떤 일이 일어나는지에 대해서만 설명하지 어떻게 애플리케이션의 상태를 변경하는지에 대해서는 관여하지 않는다. 그건 바로 `reducer`가 할 일이다. 여기 우리앱에서 구현할 reducer의 한 가지 예가 있다.


![enter image description here](https://lh3.googleusercontent.com/RVYAdVRC71UVO2mQLcGm7_3tkC1O1zZ7ougM45iZt9X8Rzxnd0Cktu4q0gW8zEoNNDt5Oc2XAQ=s944 "reducer.png")


여기 한 눈에 redux가 어떻게 동작하는 보여주는 애니메이션이 있어 퍼왔다. (출처 : http://slides.com/jenyaterpil/redux-from-twitter-hype-to-production#/9)

![redux store](https://lh3.googleusercontent.com/2kZ0VzZOaiAzLYeHnnSeam-1yAUZt55PTP2mdKaU1giRHW6o1BucRNk5BE9Q7Du9DYam1aMcFg=s944 "redux store1.gif")



## 4. Redux Three Principles
위에서 잠시 `Flux`에 대해 언급했었다. `Flux`는 `state`관리에 관한 패턴이지 `Redux`와 같이 다운로드 받을 수 있는 툴이 아니다. 반면 `Redux`는 `Flux pattern`을 실체적으로 구현한 툴이며 다음과 같은 세 가지 주요한 원칙을 지닌다.

### 4.1 Single source of truth

>
전체 애플리케이션의 `state`는 단 하나의 `store`내에 오브젝트 트리 형태로 저장된다.

![state tree](https://lh3.googleusercontent.com/BKBEU-IaqTlTxZ3VkAGHen5-bGTQpcKhknNdt2TRfD5FIW4n-YO2BpsUHsC-QA3CYtNxYm6GMw=s944 "state tree.png")  

모든 `state`가 한 곳에 존재하기 때문에 이를 `single source of truth'라 한다.
이러한 Redux의 `one-store` 접근 방식은 Flux의 `multiple-store` 접근 방식과 차별된다. 
싱글 `state` 트리는 어떠한 장점이 있을까? 이것은 더쉽게 애플리케이션을 디버깅하거나 내부 검사를 할 수 있게 하고, 기존에는 구현하기 어려웠던 일부 기능 (예 : undo/redo)을 쉽게 구현하는것이  가능해진다.

### 4.2 State is read-only

> state를 변경하는 유일한 방법은 어떤 일이 발생했는지 설명하는 하나의 action을 발생시키는 것이다. 

즉, 애플리케이션은 직접 `state`를  변경하지 않고 대신 `action`을 전달하여  `state`를 변경하고자 하는 의도를  표현하는 것이다.

실제로 Redux API를 살펴보면 단지 4개의 메소드로 구성되어 있는 것을 확인 할 수 있다.

```
store.dispatch(action)
store.subscribe(listener)
store.getState()
replaceReducer(nextReducer)
```

보다시피, setState() 메소드가 없다. 따라서 `action`을 전달하는 것만이 애플리케이션의 상태를 변경할 수 있는 유일한 채널이 되는 것이다.

### 4.3 Changes are made with pure functions

>
state 트리가  action에 의해 변환되는 구체적인 방법을 지정하기위해 순수 함수로서의 reducers를 써야 한다.

reducers는 이전 `state`와 `action`을 받고 새로운 `state`을 리턴하는 순수 함수에 다름 아니다. 명심해야 할 것은 이전 `state`을 변경하는 것이 아니라 새로운 `state` 오브젝트를 리턴해야 한다는 점이다.

순수 함수는 다음과 같은 특징을 갖는다.

```
외부 네트워크나 데이터베이스 호출을 하지 않는다.
리턴 값은 오직 입력 변수의 값에 의존한다.
입력 변수는 "불변" 으로 간주되어야한다. 즉, 변경해서는 안된다.
동일한 입력 변수를 갖는 순수 함수를 호출하면 항상 동일한 값이 리턴된다.
```

## 5. Divide The App Into Containers and Components

이제 파트 1 에서 만들었던 우리의 Tesla calculator앱을 Redux 버전으로 만들어보도록 하겠다. 먼저 앱의 전체적인 컴포넌트 구성을 살펴보자.

_![enter image description here](https://lh3.googleusercontent.com/YlCnB9gS8_KJgCfW1d8Qb8Ws01zOzj-huYRxu35Vqx6wnH9UJm_LCa85suCubWlYlDC8keXUMw=s1100 "compoent layout.png")_
 
 
React와 Redux 로직을 하나의 컴포넌트 내부에 같이 두는 것은 지저분해 보일 수 있기 때문에  프리젠테이션 전용 목적의 `Presentational` 컴포넌트와  Redux를 처리하고 `Actions`를 발송하는 상위 Wrapper 컴포넌트인  `Container` 컴포넌트를 만드는 것이 권장된다.

상위 Container 컴포넌트의 역할은 Presentational 컴포넌트에게  state 값을 전달하고 이벤트를 관리하며 Presentational 컴포넌트를 대신하여 Redux와 커뮤니케이션 하는 것이라 할 수 있다.

## 6. List State and Actions For Each Component
전체 컴포넌트 레이아웃을 참조 하여 각 컴포넌트의 state와 action 리스트를 만들도록 한다.

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
코딩을 시작하기에 앞서 part1에서 완성했던 코드베이스가 필요하므로 part1을 보지 않고 바로 part2를 진행하기를 원한다면 [여기서](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial) 코드를 클론하여 먼저 코드 베이스를 구축하도록 한다.

`npm start` 후 애플리케이션이 제대로 동작하는지 확인하자.

이제 `action` 리스트를 만들었으니 `action creators`를 만들 차례이다. `action creators`는 말 그대로 `action`을 만들어주는 함수이다. `Redux`에서 `action crators`는 단지 `action` 오브젝트를 리턴하고 필요하면 인자값을 전달해준다.

```
const changeWheel = (value) => {
  return {
    type: 'CHANGE_WHEEL',
    value
  }
}
```
이러한 `action creators`는 `dispatch` 함수에 결과값으로 전달되어 `dispatch`가 동작하게 된다.

```
dispatch(changeWheel(size))
```

`dispatch` 함수는 `store.dispatch`로 직접 액세스 할 수도 있지만 대개의 경우 `react-redux`의 `connect`와 같은 helper를 사용하여 액세스 하게된다. `connect`에 대해서는 조금 뒤에 살펴보도록 하겠다. 

### 7.1 Create Action.js

`src/actions` 디렉토리안에 `index` 파일을 만들고 다음과 같이 `action creators`를 정의한다.

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

`action creator`에 따라  defalut values가 필요하므로 우리는 이를 src 디렉토리 아래 constants/counterDefaultVal에 이 상수값을 정의한 후 `import` 하여 사용하도록 한다.

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

## 8. Create Reducers For Each Action
Reducers는 Redux store에서 받아온 `state`와 `action`오브젝트를 받아서 Redux에 다시 저장될 새로운 `state`를 반환하는 함수이다. 여기서 주어진 `state`를 직접 수정하지 않는 것이 중요하다. Reducers는 순수 함수이어야만 하고 그래서 새로운 `state	`을 리턴해야만 한다.

1. Reducer functions는 사용자 action이 발생할 때 앞으로 만들게 될 `Container`에서 호출되어진다. 
2. Reducer가 state를 변경하게 되면, Redux는 새로운 state를 각 컴포넌트에 전달하고 React는 각 컴포넌트를 다시 렌더링하게 된다.

### 8.1 Immutable Data Structures

* 자바스크립트 primitive 데이타 타입(number, string, boolean, undefined, and null) => immutable
* Object, array and function => mutable

데이터 구조의 변경은 버그가 발생하기 쉬운 것으로 알려져 있다.
우리 `store`는 state 오브젝트와 배열로 이루어지기 때문에 state를 변경하지 못하게하는 전략을 구현해야만한다.

여기 state를 변경하는 세 가지 방법이 있다:

ES5

```
// Example One
state.foo = '123';

// Example Two
Object.assign(state, { foo: 123 });

// Example Three
var newState = Object.assign({}, state, { foo: 123 });
```

위의 예제에서 첫번째, 두번째는 state 오브젝트를 변경한다. 두번째 예제에서는 Object.assign()이 모든 인자값을 첫번째 인자와 병합함으로서 변경이 이루어진다. 
세번째 예제에서는 state와 { foo: 123 }이 첫번째 인자인 새로운 오브젝트에 병합이 되므로 원래의 'state'를 변경하지 않고 새로운 값으로 오브젝트의 복사본을 만들게된다.

ES6에서 도입된 'spread operator`는 'state'를 변경하지 않는 보다 간결한 방법을 제공한다.

ES6 (ES2015)

```
const newState = { ...state, foo: 123 };
```

> 
spread operator에 대한 자세한 내용은 [여기](http://redux.js.org/docs/recipes/UsingObjectSpreadOperator.html)를 참조

### 8.2 Create Reducer for speed up counter

먼저 우리가 만들어 볼 예제는 speed counter로 테스트 주도 개발 방식으로 만들어보겠다.
Part1에서 우리의 앱은 `create react app`을 통해 만들어졌기 때문에 기본적으로 test runner로 `Jest`를 사용하게 된다. 

Jest는 다음의 명명 규칙중 하나를 사용하여 테스트 파일을 찾는다.

```
Files with .js suffix in __tests__ folders.
Files with .test.js suffix.
Files with .spec.js suffix.
```

src/reducers 디렉토리리를 만들고 teslaRangeApp.spec.js 생성한뒤 테스트를 작성한다.

```
describe('test reducer', () => {
  it('should handle initial stat', () => {
    expect(
      appReducer(undefined, {})
    ).toEqual(initialState)
  })
})
```

테스트를 작성한 후 `npm test` 명령어를 실행하자. 아래와 같은 테스트 실패 메시지를 볼 수 있어야 한다. 왜냐하면 아직 appReducer를 작성해서 넘기지 않았기 때문이다.
 
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

다시 npm test를 실행하면 테스트가 성공 할 것이다.

![enter image description here](https://lh3.googleusercontent.com/KIVhW-zKx7br3A801T6AcsPuCUu2YuJiqHAYDLvT4nfQZMwjiGM3LVKFyQeQNuMK1PqOxkGncA=s1100 "npm test2.png")

```
import { getModelData } from '../services/BatteryService';

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

function updateStats(state, newState) {

    return {
      ...state,
      config:newState.config,
      carstats:calculateStats(newState)
    }  
}


function appReducer(state = initialState, action) {
  switch (action.type) {
    case 'SPEED_UP': {
      console.log('SPEED_UP');
      const newState = {
          ...state,
          config: {
            climate:state.config.climate,
            speed:action.value + action.step,
            temperature:state.config.temperature,
            wheels:state.config.wheels
          }
      };
      return updateStats(state, newState);
    }    
    case 'SPEED_DOWN': {
      console.log('SPEED_DOWN');
      const newState = {
          ...state,
          config: {
            climate:state.config.climate,
            speed:action.value - action.step,
            temperature:state.config.temperature,
            wheels:state.config.wheels
          }
      };
      return updateStats(state, newState);
    }        
    case 'TEMPERATURE_UP': {
      console.log('TEMPERATURE_UP');
      const newState = {
          ...state,
          config: {
            climate:state.config.climate,
            speed:state.config.speed,
            temperature:action.value + action.step,
            wheels:state.config.wheels
          }
      };
      return updateStats(state, newState);
    }
    case 'TEMPERATURE_DOWN': {
      console.log('TEMPERATURE_DOWN');
      const newState = {
          ...state,
          config: {
            climate:state.config.climate,
            speed:state.config.speed,
            temperature:action.value - action.step,
            wheels:state.config.wheels
          }
      };
      return updateStats(state, newState);
    }        
    case 'CHANGE_CLIMATE': {
      console.log('CHANGE_CLIMATE');
      const newState = {
          ...state,
          config: {
            ...state.config,
            climate:!state.config.climate,
          }
      };
      return updateStats(state, newState);
    }
    case 'CHANGE_WHEEL': {
      console.log('CHANGE_WHEEL');
      const newState = {
          ...state,
          config: {
            climate:state.config.climate,
            speed:state.config.speed,
            temperature:state.config.temperature,
            wheels:action.value
          }
      };
      return updateStats(state, newState);
    }
    default:
      return state 
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

export default appReducer;
```



{% include disqus.html %}




