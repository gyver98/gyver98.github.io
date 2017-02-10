---
layout:     post
title:      Building Tesla's battery range calculator with React
date:       2017-02-09 11:37:19
summary:    React로 Tesla's battery range calculator 구현하기 
categories: blog development react
---


이 글에서는 React로 Tesla's battery range calculator을 구현하고 빌드한 후 Surge를 통해 호스팅 하는 과정을 공유하고자 한다.
이 튜토리얼은 Todd Motto의 Building Tesla's battery range calculator with Angular 2 reactive forms를 참조하여 React 버전으로 재구성한것임을 밝혀둔다.

이것이 우리가 만들 애플리케이션의 최종 GIF 이미지다.
![final](https://lh3.googleusercontent.com/5-7c6zipYR-AcmOXkW9n_DntW1JYWJz4P1m6GL0cqMbF48Tl86D7zDzZ1CBlJnDiMOgtaZAtWg=s944 "final.gif")

> 라이브 버전은 [여기서](http://cute-amusement.surge.sh) 확인할 수 있다.
 
> 소스코드는 [여기서](https://github.com/gyver98/react-tesla-range-calculator) 확인 할 수 있다. 
 
이제 단계별로 애플리케이션을 만들어보자.


## Project Setup and Creat-React-App

`Create React App`은 React application 개발을 위한 새로운 툴로서 복잡한 설정없이 바로 React 프로젝트를 시작할 수 있게 도와준다.
다음의 명령을 통해 쉽게 우리의 프로젝트 `react-tesla-range-calculator`를 설치하고 애플리케이션을 바로 시작할 수 있다.

>- npm install -g create-react-Application
>- create-react-app react-tesla-range-calculator
>- cd react-tesla-range-calculator
>- npm start

![create-react-app](https://lh3.googleusercontent.com/v7jnACqzmtuslHgKZ5DlohPUkxqX6RHOYg8CIE3f0vF-sSMWa0wqjt7dWsZJehCf5k-_gDZNMg=s944 "create-react-application.jpg")


`Create React App`을 통해 새로운 Application을 생성한 후 `http://localhost:3000/` 을 오픈하여 생성된 application을 확인해보자.
아래의 화면이 보인다면 성공적으로 프로젝트가 설정된 것이다.

![](https://lh3.googleusercontent.com/EeKbF6zn5lnouSSdulf4uvRPqxxCEo75P-shtCF5Fh4aOb3A3Xsu7sbSsiitLfd-UggKlz3D5Q=s944 "Screen Shot 2017-02-01 at 3.21.15 pm.png")

프로젝트를 본격적으로 시작하기에 앞서 먼저 프로젝트 소스 구조를 정리하자.
우리 프로젝트에 필요한 파일만 남기고 나머지는 삭제하도록 하자. (deleted App.test.js , logo.svg)
이제 우리 src 디렉토리는 다음과 같이 보여야 한다.

```
src
 - App.css
 - App.js
 - index.css
 - index.js
```

## Project entry point
가장 먼저 우리의 Tesla app을 시작하는 entry point를 설정해야 한다. 고맙게도 create-react-app이 이미 만들어 놓았다.

`src/App.js` 가 바로 우리 앱의 엔트리 포인트이다.
App.js 를 다음과 같이 수정하도록 하자.

```
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div>
        <h2>Let's get started</h2>
      </div>
    );
  }
}

export default App;
```
`npm start` 후 파일을 저장하면 자동으로 컴파일이 진행되어 업데이트된 화면을 볼 수 있다.

## Project images/assets

이 프로젝트에서 필요한 모든 이미지들은 다음에서 다운로드 받을 수 있다.

* 모든 이미지 [Download](https://toddmotto.com/static/assets.zip)  
* favicon.ico [Download](https://toddmotto.com/static/favicon.ico)

`assets.zip` 압축을 풀고 모든 이미지들을 `src/assets` 디렉토리에 위치시키고 다운 받은 `favicon.ico`를 소스 루트에 놓는다.

```
react-tesla-range-calculator/src/asstets
``` 

> 스텝을 따라하다가 뭔가 놓친거 같거나 확실하지 않다면 언제든지 [소스코드](https://github.com/gyver98/react-tesla-range-calculator)를 참조하도록 하자. 

## Data service
Tesla 사이트에서 얻을 수 있는 데이타는 하드 코드되 있고 아주 큰 데이타인데, 여기서는 이를 사용하기 쉽도록 Todd가 새롭게 만든 버전의 데이타를 사용하도록 하겠다. [link](https://github.com/toddmotto/angular-tesla-range-calculator/blob/master/src/app/tesla-battery/tesla-battery.service.ts)

우리는 Angular2에서 사용하는 `Injectable` decorator를 사용하지 않기 때문에 그 부분을 제외하고 export 부분만을 카피해서 `src/services/BatteryService.js` 에 저장하고  이를 `TeslaBattery Container`에서 `import` 해서 사용할 것이다.

이 데이타 서비스는 추후에 다시 언급하도록 하겠다.


Breaking Down the UI
-------------
거의 모든 React application UI는 여러 <span class="bg-dark-gray white">컴포넌트들의 조합</span>으로 구성되어진다. 
날씨 앱을 예로들자면, 지역명을 보여주는 컴포넌트, 현재 기온을 보여주는 컴포넌트, 5일간의 예측을 나타내는 그래프 컴포넌트들로 구성되어진다.

이러한 이유로 React 앱 개발에 앞서 UI를 컴포넌트 단위로 분해하여 보는것이 좋다.

> 애플리케이션을 컴포넌트들의 조합으로 바라보는 접근 방식에 대해서는 [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)를 참조


이 포스트에서 만들어볼 얘플리케이션의 레이아웃은 다음과 같다.
![layout](https://lh3.googleusercontent.com/JS_S82Te-_vx4OhM71m8Qn6qV5d7d6tFeCULfKBsGHTnqZFPx1s39LvTm29X8PoZCH6UGcumSA=s944 "layout.jpg")

UI를 컴포넌트 트리로 나타내보면 다음과 같다.

```
<App> -- Application entry point
 <Header></Header>
 	<TeslaBattery> -- Container
 		<TeslaCar />     -- Presentational Component
 		<TeslaStats />   -- Presentational Component 
 		<TeslaCounter /> -- Presentational Component
 		<TeslaClimate /> -- Presentational Component
 		<TeslaWheels />  -- Presentational Component
 		<TeslaNotice />  -- Presentational Component
 	</TeslaBattery>
</App>
```

## Container and presentational components
위에서 언급한 컴포넌트 트리를 보면 Container와 Presenataional component로 분류한것을 볼 수 있다.
이는 React로 애플리케이션을 개발할때 사용할 수 있는 유용한 패턴으로 컴포넌트들을 다음의 두 가지 범주로 나누게 되면 더 쉽게 재사용성을 높일 수 있게 된다.

* Container Component (statful component)
 - 어떻게 동작하는지에 관심이있다.
 - 일반적으로 일부 랩핑 div를 제외하고는 자체 DOM 마크업이 없으며 스타일을 갖지 않는다.
 - 프리젠테이션 또는 다른 컨테이너에 데이터와 동작을 제공한다.
 - 애플리케이션의 상태를 가지며 데이터 소스 역할을 한다.

* Presentational Component (statless component)
 - 어떻게 보이는지에 관심이있다.
 - 일반적으로 자체 DOM 마크업과 스타일을 가지고 있다.
 - Props를 통해 데이타와 콜백 함수를 받는다.
 - 상태를 거의 갖지 않으며 있다 하더라도 데이터 대신에 UI 상태를 갖는다.

이러한 패턴을 사용하면 어떤 이득이 있을까?

* 관심사의 분리 (Better separation of concerns)
* 재사용성 (Better reusability)
* 레이아웃 구성요소를 추출하여 중복 사용을 방지 

>  더 자세한 정보는 Dan Abramov(Redux creator) 의 [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.mbwo09sds)를 참조

## Header component
이제 우리의 첫번째 React 컴포넌트인 `Header`를 만들자.
`Header` 컴포넌트는 단순히 Tesla 로고와 텍스트가 있는 검정색의 바 이다.
`src/components/Header` 디렉토리를 생성하고 그 안에 `Header.js` 파일을 만들고 다음의 코드를 입력하자.

```
import React from 'react';
import './Header.css';
import logoUrl from '../../assets/logo.svg';

const Header = () => (
  <div className="header">
    <img src={logoUrl} alt="Tesla" />
  </div>
)

export default Header;
```

## Header component style
`src/components/Header` 디렉토리안에 `Header.css` 파일을 만들고 다음 스타일을 주자.

```
.header {
  padding: 25px 0;
  text-align: center;
  background: #222;
}

.header img {
  width: 100px;
  height: 13px;
}
```

> 컴포넌트에 스타일을 주는 방식은 여러가지가 있겠으나 여기서는 앞으로 컴포넌트를 만들때마다 `components` 디렉토리 안에 각 컴포넌트 디렉토리를 만들고 `JS`파일과 `CSS`파일을 쌍으로 만들것이다.

## Import Header component in App Container
`Header` 컴포넌트를 만들었으니 엔트리 포인트인 `App.js`에서 `Import`하여 사용해보자.

```
import React, { Component } from 'react';
import './App.css';
import Header from './components/Header/Header';

class App extends Component {
  render() {
    return (
      <div className="App">
        <Header />    
      </div>
    );
  }
}

export default App;
``` 

모든 수정된 파일을 저장하면 자동으로 업데이트되며 다음과 같이 Tesla 로고가 보여져야 한다.

![header](https://lh3.googleusercontent.com/gkOjRzStwc0JB9ITpDQL7Mx4R8A2UsIbig5ZRDVLkTHHzo_GSv3KfHdJPBmTNjluXz9ZlbO-QA=s944 "header")



## TeslaBattery Container
우리 앱에서 `TeslaBattery` 컴포넌트는 `Container`로서 데이타와 상태를 생성 관리하고 이를 다른 `presentational components`에게 전달하며 콜백 함수를 수행하고 상태를 변경하는 역할을 한다.
`src/containers` 디렉토리를 생성하고 그 안에 `TeslaBattery.js` 파일을 만들고 다음의 코드를 입력한다.

```
import React from 'react';
import './TeslaBattery.css';

class TeslaBattery extends React.Component {
  render() {
    return (
      <form className="tesla-battery">
        <h1>Range Per Charge</h1>
      </form>
    )
  }
}

export default TeslaBattery;
```

컨테이너의 관심은 어떻게 동작하는가에 있기 때문에 `TeslaBattery.css` 에는 최소한의 스타일만 준다.
앞으로 만들어질 컴포넌트들은 `TesalBattery` 컨테이너 안에 순차적으로 구성되어질 것이다.

## TeslaNotice Component
스태틱 텍스트 부분을 먼저 TeslaNotice 컴포넌트로 만들어보자.
`src/components/TeslaNotice` 디렉토리를 생성하고 그 안에 `TeslaNotice.js` 파일을 만들고 다음의 코드를 입력하자.

```
import React from 'react';
import './TeslaNotice.css';

const TeslaNotice = () => (
  <div className="tesla-battery__notice">
    <p>
      The actual amount of range that you experience will vary based
      on your particular use conditions. See how particular use conditions
      may affect your range in our simulation model.
    </p>
    <p>
      Vehicle range may vary depending on the vehicle configuration,
      battery age and condition, driving style and operating, environmental
      and climate conditions.
    </p>
  </div>
)

export default TeslaNotice;
```
`src/components/TeslaNotice ` 디렉토리안에 `TeslaNotice.css` 파일을 만들고 다음 스타일을  준다.

```
.tesla-battery__notice {
    margin: 20px 0;
    font-size: 15px;
    color: #666;
    line-height: 20px;
} 
```

그 다음에 `TeslaBattery.js`에서 `TeslaNotice` 컴포넌트를 사용할 수 있도록 `Import`한다.

```
...
import TeslaNotice from '../components/TeslaNotice/TeslaNotice';

class TeslaBattery extends React.Component {
  render() {
    return (
      <form className="tesla-battery">
        <h1>Range Per Charge</h1>
        <TeslaNotice />
      </form>
    )
  }
}...
```

> 앞으로도 이러한 패턴으로 컴포넌트를 생성하고 TeslaBattery 컨테이너에서 Import 하여 사용하는 방식으로 개발이 진행될것이다.

## TeslaCar Component
이번엔 멋진 TeslaCar를 렌더링해보자. 
`src/components/TeslaCar` 디렉토리를 생성하고 그 안에 `TeslaCar.js` 파일을 만들고 다음의 코드를 입력하자.

```
import React from 'react';
import './TeslaCar.css';

const TeslaCar = (props) => (
  <div className="tesla-car">
    <div className="tesla-wheels">
      <div className={`tesla-wheel tesla-wheel--front tesla-wheel--${props.wheelsize}`}></div>
      <div className={`tesla-wheel tesla-wheel--rear tesla-wheel--${props.wheelsize}`}></div>
    </div>
  </div>
);

TeslaCar.propTypes = {
  wheelsize: React.PropTypes.number
}

export default TeslaCar;
```

다음으로 `src/components/TeslaCar ` 디렉토리안에 `TeslaCar.css` 파일을 만들고 다음 스타일을 준다. 코드가 길어 여기서는 생략하였으므로 [소스코드]()를 확인해서 작업하도록 하자.

```
.tesla-car {
  width: 100%;
  min-height: 350px;
  background: #fff url(../../assets/tesla.jpg) no-repeat top center;
  background-size: contain; }

.tesla-wheels {
  height: 247px;
  width: 555px;
  position: relative;
  margin: 0 auto; }

...

```
그 다음에 `TeslaBattery.js`에서 `TeslaCar` 컴포넌트를 사용할 수 있도록 `Import`한다.

```
...
import TeslaCar from '../components/TeslaCar/TeslaCar';

class TeslaBattery extends React.Component {
  render() {
    return (
      <form className="tesla-battery">
        <h1>Range Per Charge</h1>
        <TeslaCar />
        <TeslaNotice />
      </form>
    )
  }
}
...
```

파일을 저장 후 업데이트 된 화면은 다음과 같다.

![enter image description here](https://lh3.googleusercontent.com/_l3ezUs5AA8V50X8FAFX-_cHDujEddpR0iv5Z1NQvrVeTuvKj-_WHlocKoESg3EMgEJ5hO4xXg=s944 "TeslaCar.png")

와우! 멋지긴 한데 뭔가 이상하다. 바퀴가 보이지 않는다.
원인을 찾아보자. 소스코드에 따르면 `TeslaCar'는 `props`를 넘겨 받고 `props.wheelsize`에 따라 클래스가 달라져야 한다.
즉 뭔가 필요한 데이타(이 경우엔 wheelsize)를 상위의 컴포넌트로 받아야만 적절하게 렌더링할 수 있다는 것이고 데이타를 전달받을 수 있는 커뮤니케이션 방법이 있어야 한다. 
 
React는 컴포넌트 트리로 구성이 되는데 데이타와 상태를 갖고 전달해주는 컨테이너와 데이타와 상태를 컨테이너로부터 수동적으로 전달받는 컴포넌트로 크게 구성된다고 볼 수 있는데, 바로 이 상태를 하위 컴포넌트에게 전달해주는 방법이 `props`인 것이다.  

크롬에서 [React Developer Tools](https://www.google.com.au/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwi10rn7soTSAhVJp5QKHYPcC5YQFggbMAA&url=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Freact-developer-tools%2Ffmkadmapgofadopljbjfkapdkoienihi%3Fhl%3Den&usg=AFQjCNEv0udXgBoaukzJa59I_vufhScUbQ&sig2=wTA5bB3JG2ZQ6wbSiDgq8g)를 이용하여 컴포넌트 트리를 확인해보면 이를 쉽게 이해할 수 있다.
![enter image description here](https://lh3.googleusercontent.com/zaaotVwcHReWQP5wl4odZXvW_wPCRHtPAHuK5aESdCMpSn3EaB96LYvPk3pGEb8DvN6UFy_dcA=s944 "props.jpg")

`props`는 자바스크립트 오브젝트 이며 이 경우에 Empty Object이다. 왜냐하면 상위 컴포넌트인 `TeslaBattery`에서 props를 넘기지 않았기 때문이다.

## Status of Application
여기서 우리 애플리케이션에서 관리해야 할 상태가 무엇이 있는지 생각해보자.
이 글 상단의 최종 앱 GIF 이미지를 보면 상태값은 다음과 같다.

* carstats (array) : 현재 선택된 조건 수치( speed, temperature, climate, wheel )에 따른 차 모델별 배터리 수치값 배열
* config (object): 현재 선택된 조건 (speed:55, temperature:20, climate:aricon on, wheel: 19) 

![enter image description here](https://lh3.googleusercontent.com/LZ4rlA_E8f5_Qt-dKNtBICK-R7zcZdtlcuLFM74IiZzT5Zmh_BOJLDTxz1uzsGtgA7i58Nvfaw=s944 "state.jpg")

이제 이 상태값을 관리하고 하위 컴포넌트에 전달할 수 있도록 `TeslaBattery` 컨테이너를 constructor 함수를 추가하고 초기값을 설정한다. TeslaCar 컴포넌트는 `props`를 통해 wheelsize input을 받아들이고 Teslar car 이미지를 렌더링한다.

```
...
class TeslaBattery extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      carstats: [],
      config: {
        speed: 55,
        temperature: 20,
        climate: true,
        wheels: 19
      }
    }
  }
  
  render() {
    const { config } = this.state;
    return (
      <form className="tesla-battery">
        <h1>Range Per Charge</h1>
        <TeslaCar wheelsize={config.wheels}/>
        <TeslaNotice />
      </form>
    )
  }
}...
```

`render()` 내에서 `const { a, b } = c` 의 형식의 코드는 ES6 의 Object Destructuring (객체 비구조화 할당) 문법이다. 필요한 값을 객체에서 꺼내 그 값으로 레퍼런스를 만들어준다.

> 개념적으로, React 컴포넌트는 JavaScript function과 같아 'props'라 불리우는 임의의 입력을 받아 무엇이 보여져야 하는지를 묘사하는 React 엘리먼트를 리턴한다.

파일을 저장하고 업데이트된 화면에서는 렌더링된 테슬라 차의 모습과 바퀴 애니메이션이 잘 동작하는 것을 볼 수 있다.
또한 컴포넌트 트리에서도 `props`가 잘 전달되는것을 확인할 수 있다.

![enter image description here](https://lh3.googleusercontent.com/6PBG4kbLiVk1QfkXo8pYnfYW33_7yH_ULxq_8uBvxYFloDxacD8WPXLYlduOa0AT-cG9_8qTqQ=s944 "teslacar props.jpg")

> 어떠한 함수들은 입력값을 변경하지 않고 언제나 같은 입력값이면 같은 출력값을 리턴한다는 의미에서 순수하다고 불리운다. (`Pure function`) 여기서 한 가지 중요한 React의 엄격한 룰은 모든 React 컴포넌트들은 `props`에 관해서는 순수 함수와 같이 동작해야 한다는 것이다. `props`는 reda-only여야 한다.


## TeslaStats Component
이제 우리는 `TeslaStats` 컴포넌트를 구현하고자 한다.
 `src/components/TeslaStats` 디렉토리를 생성하고 그 안에 `TeslaStats.js` 파일을 만들고 다음의 코드를 입력하자.
 
```
import React from 'react';
import './TeslaStats.css';

const TeslaStats = (props) => {
  const listItems = props.carstats.map((stat) => (
    <li key={stat.model}>
      <div className={`tesla-stats-icon tesla-stats-icon--${stat.model.toLowerCase()}`}></div>
      <p>{stat.miles}</p>
    </li>
  ));
  return (
    <div className="tesla-stats">
    <ul>
      {listItems}  
    </ul>
  </div>
  )
};

TeslaStats.propTypes = {
  carstats: React.PropTypes.array
}

export default TeslaStats;
```
`TeslaStats` 또한 상태를 전달받는 `presentational component`로서 `props`로 모델별 수치값이 담긴 배열 리스트를 받아 렌더링한다. 
먼저 자바스크립트에서 어떻게 리스트를 변환하는지를 생각해보자. 아래의 코드는 `map()` 함수를 이용하여 `numbers` 배열을 받아 두배의 값을 리턴한다. 이 코드는 `[2, 4, 6, 8, 10]`을 콘솔에 출력한다. 

```
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

React에서 배열을 리스트의 요소들로 변환하는 것은 거의 이와 똑같다. 
여기서 우리는 Javascript map() 함수를 사용하여 props.carstats 배열을 반복한다. 매 반복마다 `model`이 담긴 `<div>` 태그와 `miles`가 담긴 `<p>` 태그를 감싸고 있는 `<li>` 엘리먼트를 리턴한다. 최종적으로  listItems 배열을 <ul> 엘리먼트에 포함시켜 리턴한다. 

다음으로 `src/components/TeslaStats ` 디렉토리안에 `TeslaStats.css` 파일을 만들고 다음 스타일을 지정한다. 코드가 길어 여기서는 생략하였으므로 [소스코드]()를 확인해서 작업하도록 하자.

```
...
.tesla-stats {
  margin: -70px 0 30px; 
}
.tesla-stats ul {
  text-align: center; 
}
...    
```
그 다음에 `TeslaBattery.js`에서 `TeslaStats` 컴포넌트를 사용할 수 있도록 `Import`한다.

```
...
import TeslaStats from '../components/TeslaStats/TeslaStats';
...
render() {
  const { config, carstats } = this.state;
  return (
    <form className="tesla-battery">
      <h1>Range Per Charge</h1>
      <TeslaCar wheelsize={config.wheels}/>
      <TeslaStats carstats={carstats}/>
      <TeslaNotice />
    </form>
  )
}
...
```
`props`로  `carstats` 배열을 전달해야 하기 때문에 이제 앞서 만들었던 데이터 서비스인 `BatteryService`를 이용하여 값을 셋팅하도록 하자.

## CalculateStats and setState
먼저 `getModelData`를 `import` 한다. 
`componentDidMount()` 를 통해 컴폰넌트가 마운트 된후 `statsUpdate()` 함수를 호출하고
입력값으로 `carModels` 와 현재 상태값을 받는 `calculateStats()` 가 실행되면 `model`과 `miles` 값이 매칭된 오브젝트가 리턴되고, 이 리턴값이 `setState()` 를 통해 애플리케이션의 `source of truth`인 `state`를 업데이트 하게된다.

```
...
import { getModelData } from '../services/BatteryService';
...

calculateStats = (models, value) => {
  const dataModels = getModelData();
  return models.map(model => {
    const { speed, temperature, climate, wheels } = value;
    const miles = dataModels[model][wheels][climate ? 'on' : 'off'].speed[speed][temperature];
    return {
      model,
      miles
    };
  });
}
  
statsUpdate() {
  const carModels = ['60', '60D', '75', '75D', '90D', 'P100D'];
  // Fetch model info from BatteryService and calculate then update state
  this.setState({
    carstats: this.calculateStats(carModels, this.state.config)
  })  
}
  
componentDidMount() {
	this.statsUpdate(); 
}
...
```

한 가지 주의할 점은 Class 내에서 `this`로 접근하기 위해서는 `TeslaBattery` `constructor` 함수내 명시적인 바인딩이 필요하다.

```
...
this.calculateStats = this.calculateStats.bind(this);
this.statsUpdate = this.statsUpdate.bind(this);
...
```

여기서 우리의 index.css에 추가적인 스타일링이 필요하다.
src/index.css 파일을 열고 기존의 모든 코드를 삭제하고 다음을 추가하자

```
@font-face {
  font-family: 'RobotoNormal';
  src: url('./assets/fonts/Roboto-Regular-webfont.eot');
  src: url('./assets/fonts/Roboto-Regular-webfont.eot?#iefix') format('embedded-opentype'),
       url('./assets/fonts/Roboto-Regular-webfont.woff') format('woff'),
       url('./assets/fonts/Roboto-Regular-webfont.ttf') format('truetype'),
       url('./assets/fonts/Roboto-Regular-webfont.svg#RobotoRegular') format('svg');
  font-weight: normal;
  font-style: normal;
}

*, *:before, *:after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font: 300 14px/1.4 'Helvetica Neue', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
}

.cf:before,
.cf:after {
    content: '';
    display: table;
}
.cf:after {
    clear: both;
}
.cf {
  *zoom: 1;
}

``` 

지금까지의 작업 결과 화면은 다음과 같다.

![enter image description here](https://lh3.googleusercontent.com/oGPvtPfi8OkSCN3G9YOU1allXUfSgTx9FUS10qzobFK4xC4edzuVRgW_fvw4eym9hW0UjBBZqA=s944 "carstats.png")


