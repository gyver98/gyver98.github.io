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



{% include disqus.html %}




