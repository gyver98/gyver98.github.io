---
layout:     post
title:      Building Tesla's battery range calculator with React
date:       2017-02-13 11:37:19
summary:    Rebuilding Tesla's Battery Range Calculator with React 
categories: blog development react
---

In this article, I will share the process of rebuilding Tesla's battery range calculator with React.
Disclaimer: This tutorial is a reconstruction of the React version of Todd Motto's [Building Tesla's battery range calculator with Angular 2 reactive forms](https://toddmotto.com/building-tesla-range-calculator-angular-2-reactive-forms).
So this post will reuse some materials (data,images and css style). We will focus on rebuilding it in React way.
This is the final GIF image of our application.

![final](https://lh3.googleusercontent.com/ADOBXOthirfSi9f9j-f2giwZc_9Gtlb6qcNAmnR0y1rLVBKvRRyG4Zf5oPkvtlXE2dsKKFy0Bw=s944 "final.gif")

> Check out the [live version](http://cute-amusement.surge.sh) before we get started.
 
> Check out the [source code](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial). 
 
Now let's create an application step by step.

## Project Setup and creat-react-app

[creat-react-app](https://github.com/facebookincubator/create-react-app) is a new tool open-sourced by Facebook for fast react application development, which allows you to easily start React applications without complex setups.
You can easily install our project `react-tesla-range-calculator` and start the application right away with the following command:

>- npm install -g create-react-Application
>- create-react-app react-tesla-range-calculator
>- cd react-tesla-range-calculator
>- npm start

![create-react-app](https://lh3.googleusercontent.com/v7jnACqzmtuslHgKZ5DlohPUkxqX6RHOYg8CIE3f0vF-sSMWa0wqjt7dWsZJehCf5k-_gDZNMg=s944 "create-react-application.jpg")

Create a new application through `creat-react-app` and open `http://localhost:3000/` to check the generated application. If you see the screen below, the project has been successfully set up.

![](https://lh3.googleusercontent.com/EeKbF6zn5lnouSSdulf4uvRPqxxCEo75P-shtCF5Fh4aOb3A3Xsu7sbSsiitLfd-UggKlz3D5Q=s944 "Screen Shot 2017-02-01 at 3.21.15 pm.png")

Before we start the project, we need to touch the project source structure.
Just leave the files we need for the project and delete the rest. (deleted App.test.js , logo.svg)
Now our src directory should look like this:

```
src
 - App.css
 - App.js
 - index.css
 - index.js
```

## Project Entry Point
First we need to set the entry point to start our Tesla app. Thankfully it's already created by `create-react-app`.

`src/App.js` is the entry point for our app.
First up, change your `App.js` to this.

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

When you save the file, it will be automatically compiled and you can see the updated screen.

## Project images/assets

All images required for this project can be downloaded from:

* images [Download](https://toddmotto.com/static/assets.zip)  
* favicon.ico [Download](https://toddmotto.com/static/favicon.ico)

Unpack `assets.zip` and place all images in the `src/assets` directory and place the downloaded `favicon.ico` in the source root.

```
react-tesla-range-calculator/src/asstets
``` 

> Any time you feel like if you've missed something or unsure if you're doing right, you can refer to the [source code](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial) as a reference.

## Data service
The data you can get from Tesla site is hard-coded and very large, so I'll use Todd's new version of the data to make it easier to use. [link](https://github.com/toddmotto/angular-tesla-range-calculator/blob/master/src/app/tesla-battery/tesla-battery.service.ts)
We do not use the `Injectable` decorator used in Angular2, so we will copy only the `export` part, save it in `src/services/BatteryService.js`, and `import` it in the `TeslaBattery` container.
We will revisit this data service later.

Breaking Down the UI
-------------
Almost all React application UIs consist of a <span class="bg-dark-gray white">combination of components</span>.
For example, a weather app consists of a component that displays a local name, a component that displays the current temperature, and a graph component that represents a five-day forecast.
For this reason, it is a good idea to decompose the UI into component units before developing the React app.

> See [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html) for an approach to looking at an application as a combination of components.

The layout of this application is shown below.
![layout](https://lh3.googleusercontent.com/JS_S82Te-_vx4OhM71m8Qn6qV5d7d6tFeCULfKBsGHTnqZFPx1s39LvTm29X8PoZCH6UGcumSA=s944 "layout.jpg")

The UI is represented by a component tree as follows.

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
In the above mentioned component tree, we can see that it is classified as `Container` and` Presenataional Component`.
This is a useful pattern that can be used when developing an application with React. 
It is easier to reuse by dividing components into two categories.

```
* Container Component (statful component):
 - Are concerned with how things work.
 - In general, except for some wrapping divs, they do not have their own DOM markup and have no style.
 - Provide data and actions to presentational or other container components.
 - Are often stateful, as they tend to serve as data sources.

* Presentational Component (statless component):
 - Are concerned with how things look.
 - Usually have some DOM markup and styles of their own.
 - Receive data and callbacks exclusively via props.
 - Rarely have their own state (when they do, it’s UI state rather than data).
```

What are the benefits of using these patterns?

* Better separation of concerns
* Better reusability
* Extract layout components to prevent duplication 

>  For more details, see [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.mbwo09sds)

## Header component
Let's create our first React component, `Header`.
The `Header` component is simply a black bar with the Tesla logo and text.
Create the `src/components/Header` directory, create a `Header.js` file in it, and enter the following code:

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

> Here, the component is in the form of a function (ES6 Arrow Function). A component declared in this form is called a functional component. If there is no `state` and the` Lifecycle` method is not needed, it is a good pattern to declare it as a function type. Functional components are suitable for `Presentational Component` because they have no state and they depend only on the `props` that is received from higher components.

### Header Component Style
Create a `Header.css` file in the `src/components/Header` directory and type the following style:

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

> There are a number of ways to apply styles to components, but here we will create each component directory in the `components` directory and pair `JS` and `CSS` files each time we create a component.

### Import Header component in App Container
Now that you've created the `Header` component, let's use `import` in the entry point `App.js`.

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

When you save all the modified files, they will be updated automatically and you should see the Tesla logo as follows:

![header](https://lh3.googleusercontent.com/gkOjRzStwc0JB9ITpDQL7Mx4R8A2UsIbig5ZRDVLkTHHzo_GSv3KfHdJPBmTNjluXz9ZlbO-QA=s944 "header")

## TeslaBattery Container
In our app, the `TeslaBattery` component is responsible for creating and managing data and state as `Container Component`, passing it to other `Presentational Components`, performing a callback function and changing its state.
By inheriting `React.Component`, `TeslaBattery` must have a `render` method, optionally it can initialize its state through the `constructor`, and implement other methods such as [Lifecycle](https://facebook.github.io/react/docs/react-component.html) callbacks.
`Lifecycle callbacks` are useful when you want to render or update components, or to receive notifications at different stages of `Lifecycle`.

Create the `src/containers` directory, create a `TeslaBattery.js` file in it, and enter the following code:

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

`TeslaBattery.css` only holds a minimal style because the container's interest is how it works.
The components to be created in the future will be configured in the `TesalBattery` container sequentially.

## TeslaNotice Component
Let's first create a static text part with a `TeslaNotice` component.
Create the `src/components/TeslaNotice` directory, create a `TeslaNotice.js` file in it, and enter the following code:

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

### TeslaNotice Component Style
Next up, create `src/components/TeslaNotice` directory, create `TeslaNotice.css` in it and add these styles to your `TeslaNotice.css` file:

```
.tesla-battery__notice {
    margin: 20px 0;
    font-size: 15px;
    color: #666;
    line-height: 20px;
} 
```

### Import TeslaNotice component in TeslaBattery Container
Next, import `TeslaNotice` component in `TeslaBattery.js` :

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

> We will continue in such a way that components are created in this pattern and imported from the `TeslaBattery` container.

## TeslaCar Component
Now let's render a nice `TeslaCar`. 
Create the `src/components/TeslaCar` directory, create a `TeslaCar.js` file in it, and inside your `TeslaCar.js` file :

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

Here we specify `propTypes` using the React built-in typechecking. 
In development mode, React checks `props` passed to the component. (Only in development mode for performance reasons)
For each `props` attribute, React attempts to find it in the component's `propType` object to determine whether (1) prop is expected (2) prop is the correct type. In this case, the `TeslaCar` component expects the `props` attribute `wheelsize` and specifies that it is a `number` type. If the wrong value is provided, a warning appears in the JavaScript console, which is useful for fixing potential bugs in early stage.

> More information on React.PropTypes can be found [here](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)

### TeslaCar Component Style
Next, create a `TeslaCar.css` file in the `src/components/TeslaCar` directory and give it the following style. 
Since the code is long and omitted here, let's check the [source code](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaCar/TeslaCar.css).

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

This gives us our animations and the component base for the car, which is displayed as background images.

### Import TeslaCar component in TeslaBattery Container
Next, we need to add these components to our container again. Import `TeslaNotice` component in `TeslaBattery.js` :

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

Here’s what you should be seeing:

![enter image description here](https://lh3.googleusercontent.com/_l3ezUs5AA8V50X8FAFX-_cHDujEddpR0iv5Z1NQvrVeTuvKj-_WHlocKoESg3EMgEJ5hO4xXg=s944 "TeslaCar.png")

## Props and React Developer Tools 
Wow! It's nice but something is missing. The wheels are not shown.
Let's look for the cause. According to the source code, `TeslaCar` should be passed to `props` and class name changed based on `props.wheelsize`.
In other words, you need to receive some data (in this case, wheelsize) from the parent component and render it properly, and there must be a communication method that can receive the data. 
React is composed of a component tree, which consists of a container for delivering data and status, and a component for passively receiving data and status from a container.
The tool that delivers this state to the subcomponents is a single object, `props`.

You can easily understand this by checking the component tree using [React Developer Tools](https://www.google.com.au/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwi10rn7soTSAhVJp5QKHYPcC5YQFggbMAA&url=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Freact-developer-tools%2Ffmkadmapgofadopljbjfkapdkoienihi%3Fhl%3Den&usg=AFQjCNEv0udXgBoaukzJa59I_vufhScUbQ&sig2=wTA5bB3JG2ZQ6wbSiDgq8g) in Chrome.

![enter image description here](https://lh3.googleusercontent.com/zaaotVwcHReWQP5wl4odZXvW_wPCRHtPAHuK5aESdCMpSn3EaB96LYvPk3pGEb8DvN6UFy_dcA=s944 "props.jpg")

`props` is a javascript single object, in this case an empty object. This is because we did not pass `props` in the parent component `TeslaBattery`.

## State of Application
We need to think about what `state` is required to be managed in our app.
If you look at the final app GIF image at the top of this article, the state values ​​are:

* carstats (object array) : An array of battery numerical value objects ​​by car model according to the currently selected condition value (speed, temperature, climate, wheel)
* config (object): Currently selected conditions object (speed: 55, temperature: 20, climate: aricon on, wheel: 19)

![enter image description here](https://lh3.googleusercontent.com/LZ4rlA_E8f5_Qt-dKNtBICK-R7zcZdtlcuLFM74IiZzT5Zmh_BOJLDTxz1uzsGtgA7i58Nvfaw=s944 "state.jpg")

That is the <span class="bg-dark-gray white">single source of truth</span> for our app.
Now we will add the constructor method to the `TeslaBattery` container and set the initial value so that we can manage this state value and pass it to the subcomponent. The `TeslaCar` component accepts the `wheelsize` input through `props` and renders the Teslar car image and spins the wheels.

> Both the parent component and the child component do not know whether a particular component is stateful or stateless and do not care whether it is defined as a `function` or a `class`. This is why the state is often called local or encapsulated.
This state can not be accessed by components other than the component that owns and sets the state. 
So this state value can be passed to the sub-component as `props`. This is commonly referred to as a "top-down" or "unidirectional" data flow. Every state is always owned by a particular component, and any data or UI derived from that state only affects the "downward" component of the tree.

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
  	// ES6 Object destructuring Syntax,
    // takes out required values and create references to them
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

In `render()`, the code in the form `const {a, b} = c` is `ES6 Object Destructuring`. It takes the required value out of the object and makes a reference to it.

> Conceptually, the React component is like a JavaScript function and receives an arbitrary input called 'props' and returns a React element that describes what should be shown.


If you save files, you can see that the rendered Tesla car and wheel animation work well on the updated screen.
You can also see that `props` is passed well in the component tree.

![enter image description here](https://lh3.googleusercontent.com/6PBG4kbLiVk1QfkXo8pYnfYW33_7yH_ULxq_8uBvxYFloDxacD8WPXLYlduOa0AT-cG9_8qTqQ=s944 "teslacar props.jpg")

> Some functions are called "pure" in the sense that they always return the same output value if they have the same input value without changing the input value. (`Pure function`) One important React strict rule here is that all React components should behave like pure functions with respect to ` props`. `props` must be read-only.

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

### TeslaStats Component Style
다음으로 `src/components/TeslaStats ` 디렉토리안에 `TeslaStats.css` 파일을 만들고 다음 스타일을 지정한다. 코드가 길어 여기서는 생략하였으므로 [소스코드](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaStats/TeslaStats.css)를 확인해서 작업하도록 하자.

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
이 컴포넌트가 수행하는 작업은 `props.carstats` 배열을 반복하면서 특정 클래스를 `stat.model`을 기반으로 요소에 바인딩한다. 그러면 테슬라 모델을 표시하기 위해 배경 이미지를 교체 할 수 있게된다.

### Import TeslaStats component in TeslaBattery Container
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

### CalculateStats and setState
먼저 `getModelData`를 `import` 한다. 
`componentDidMount()` 를 통해 컴포넌트가 마운트 된후 `statsUpdate()` 함수를 호출하고
입력값으로 `carModels` 와 현재 상태값을 받는 `calculateStats()` 가 실행되면 `model`과 `miles` 값이 매칭된 오브젝트가 리턴되고, 이 리턴값이 `setState()` 를 통해 애플리케이션의 `source of truth`인 `state`오브젝트를 업데이트 하게된다.

```
...
import { getModelData } from '../services/BatteryService';
...

calculateStats = (models, value) => {
  const dataModels = getModelData();
  return models.map(model => {
    // ES6 Object destructuring Syntax,
    // takes out required values and create references to them
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

### Add Additional Style
여기서 보기좋은 레이아웃을 위해 추가적인 스타일링이 필요하다.
먼저 `src/index.css` 파일을 열고 기존의 모든 코드를 삭제하고 다음을 추가하자.

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

다음으로 `src/App.css` 파일을 열고 기존의 모든 코드를 삭제하고 다음을 추가하자.

```
.wrapper {
  margin: 100px 0 150px;
}
```


지금까지의 작업 결과 화면은 다음과 같다.

![enter image description here](https://lh3.googleusercontent.com/R6ajaVgTej3zFcUfOn3kb5PsMecYDLMui6C84Leeqy5jt4G-C2qAwtFoxyZW44iMss_HtzA56A=s944 "carstat")

## Reusable TeslaCounter Component
테슬라의 속도 및 외부 온도 컨트롤은 재사용 가능한 컴포넌트이어야 하므로 단계, 최소값, 최대 값 및 제목 및 단위 (mph / degrees)와 같은 기타 메타 데이터를 허용하는 일반 Counter 컴포넌트로 만들어 보겠다. 또한 지금까지 만들어본 컴포넌트와는 달리 사용자 입력(버튼 클릭, 체크박스 선택 등)에 반응하여 상태값을 변경하는 액션이 필요한데, 어떻게 하위 컴포넌트에서 발생하는 이벤트를 핸들링하는지 알아보도록 하겠다. 

이전에 했던것처럼 `src/components/TeslaCounter` 디렉토리를 생성하고 그 안에 `TeslaCounter.js` 파일을 만들고 다음의 코드를 입력하자.

```
import React from 'react';
import './TeslaCounter.css';

const TeslaCounter = (props) => (
  <div className="tesla-counter">
    <p className="tesla-counter__title">{props.initValues.title}</p>
    <div className="tesla-counter__container cf">
      <div className="tesla-counter__item">
        <p className="tesla-counter__number">
          { props.currentValue }
          <span>{ props.initValues.unit }</span>
        </p>
        <div className="tesla-counter__controls">
          <button 
            onClick={(e) => props.increment(e, props.initValues.title)} 
            disabled={props.currentValue >= props.initValues.max} 
          >
          </button>
          <button 
            onClick={(e) => props.decrement(e, props.initValues.title)} 
            disabled={props.currentValue <= props.initValues.min} 
          >
          </button>
        </div>
      </div>
    </div>
  </div>  
);

TeslaCounter.propTypes = {
  currentValue: React.PropTypes.number,
  increment: React.PropTypes.func,
  decrement: React.PropTypes.func,
  initValues: React.PropTypes.object
}

export default TeslaCounter;
```

여기서 우리가 원하는 것을 생각해보자. 사용자가 속도와 온도를 클릭하여 변경할 때마다 수치가 최대값과 최소값 사이에서 반영되어 렌더링 되도록 상태를 업데이트해야한다. 컴포넌트는 자체 상태만 업데이트해야하므로 TeslaBattery은 상태를 업데이트해야 할 때마다 실행되는 callback(increment, decrement)을 TeslaCounter에 전달한다. 버튼에 onClick 이벤트를 사용하여 이벤트를 알릴수 있다. TeslaBattery에 의해 전달된 callback은 setState()를 호출하고 앱이 업데이트되는것이다. 조금 있다 TeslaBattery에 의해 전달될 callback을 구현해볼것이다.

### TeslaCounter Component Style
먼저 스타일을 구현해보자.
`src/components/TeslaCounter` 디렉토리안에 `TeslaCounter.css` 파일을 만들고 다음 스타일을 지정한다. 코드가 길어 여기서는 생략하였으므로 [소스코드](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaCounter/TeslaCounter.css)를 확인해서 작업하도록 하자.

```
.tesla-counter {
  float: left;
  width: 230px; 
}
.tesla-counter__title {
  letter-spacing: 2px;
  font-size: 16px; 
}
...  
```

### Import TeslaStats component in TeslaBattery Container
자, 이제 우리는 `TeslaBattery`에 `callback`을 구현해 `TeslaCar` 컴포넌트로 전달해 보겠다.
먼저 `TeslaBattery.js`에서 `TeslaCounter` 컴포넌트를 사용할 수 있도록 `import`한다. 그리고 callback 함수인 increment() 와 decrement(), 내부함수인 updateCounterState()를 구현하고 constructor() 내에 바인딩한다. 그 후 `callback` 함수를 `TeslaCounter ` 컴포넌트에 `props`로 전달한다.


```
...
	constructor(props) {
    super(props);

    this.calculateStats = this.calculateStats.bind(this);
    this.statsUpdate = this.statsUpdate.bind(this);
    this.increment = this.increment.bind(this);
    this.decrement = this.decrement.bind(this);
    this.updateCounterState = this.updateCounterState.bind(this);

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
...
	updateCounterState(title, newValue) {
    const config = { ...this.state.config };
    // update config state with new value
    title === 'Speed' ? config['speed'] = newValue : config['temperature'] = newValue;
    // update our state
    this.setState({ config });
  }

  increment(e, title) {
    e.preventDefault();
    let currentValue, maxValue, step;
    const { speed, temperature } = this.props.counterDefaultVal;
    if (title === 'Speed') {
      currentValue = this.state.config.speed;
      maxValue = speed.max;
      step = speed.step;
    } else {
      currentValue = this.state.config.temperature;
      maxValue = temperature.max;
      step = temperature.step;
    }

    if (currentValue < maxValue) {
      const newValue = currentValue + step;
      this.updateCounterState(title, newValue);
    }
  }

  decrement(e, title) {
    e.preventDefault();
    let currentValue, minValue, step;
    const { speed, temperature } = this.props.counterDefaultVal;
    if (title === 'Speed') {
      currentValue = this.state.config.speed;
      minValue = speed.min;
      step = speed.step;
    } else {
      currentValue = this.state.config.temperature;
      minValue = temperature.min;
      step = temperature.step;
    }

    if (currentValue > minValue) {
      const newValue = currentValue - step;
      this.updateCounterState(title, newValue);
    }
  }  
...
render() {	
	return (
      <form className="tesla-battery">
        <h1>Range Per Charge</h1>
        <TeslaCar wheelsize={config.wheels} />
        <TeslaStats carstats={carstats} />
        <div className="tesla-controls cf">
          <TeslaCounter
            currentValue={this.state.config.speed}
            initValues={this.props.counterDefaultVal.speed}
            increment={this.increment}
            decrement={this.decrement}
          />
          <div className="tesla-climate-container cf">
            <TeslaCounter
              currentValue={this.state.config.temperature}
              initValues={this.props.counterDefaultVal.temperature}
              increment={this.increment}
              decrement={this.decrement}
            />
          </div>
        </div>
        <TeslaNotice />
    </form>
  )
}    
```

여기서 `TeslaCounter`에 전달되는 `initValues`는 상수값으로 `TeslaBattery`의 상위 컴포넌트인 `App`으로 부터 전달된다.
`App.js`를 열고 다음과 같이 `counterDefaultVal` 오브젝트를  `TeslaBatter` 컴포넌트에 전달하도록 한다.

```
import React, { Component } from 'react';
import './App.css';
import Header from './components/Header/Header';
import TeslaBattery from './containers/TeslaBattery';

const counterDefaultVal = {
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
};

class App extends Component {
  render() {
    return (
      <div className="App">
        <Header />
        <TeslaBattery counterDefaultVal={counterDefaultVal}/>
      </div>
    );
  }
}

export default App;
```

이제 React Developer Tool을 통해 Speed 와 Temperature를 클릭하면 변경된 수치가 상태 오브젝트에 업데이트되고 리렌더링 되는 것을 확인할 수 있다.

![TeslaCounter](https://lh3.googleusercontent.com/qe5PfBiZqso7MTGmv2FJX4O1u_PyJwybhpJCeuVsFgV7yfUXB3qxWXrZGrYw-bxxZaR9XfNTmA=s944 "counter.gif")

### Virtual DOM
`Single-Page Application (SPA)`이  우리에게 줄 수 있는 것은 매끄러운 사용자 경험과 상호 작용일것이다.
사용자가 속도나 온도를 바꿀때마다 전체 페이지를 다시 로드할 필요없이 카모델 수치는 업데이트된다. 데이타를 가져오기위해 서버에 접속할 필요가 있다하더라도 말이다.
이러한 사용자 경험을 제공하려면 변경 또는 상호 작용이 발생할 때 `DOM`의 어느 부분을 업데이트해야하는지 파악해야할 필요가 있다. 자바스크립트 프레임웍 마다 다른 전략을 사용하는데, `Ember`는 `data-binding`을 사용하고, `Angular 1`은 [`dirty checking`](https://docs.angularjs.org/guide/scope) 그리고 `React`는 [`Virtual DOM`](https://facebook.github.io/react/docs/rendering-elements.html)을 사용한다.

React에서는 컴포넌트의 렌더링 메서드가 처음 호출되면 실제 `DOM` 요소 자체가 아닌 가상 `DOM`이라는 `DOM` 모델을 출력한다. 가상 `DOM`은 `DOM`의 모습을 나타내는 자바스크립트 데이터 구조이다. 그런다음 React는 이 모델을 가져 와서 실제 `DOM` 요소를 생성하는것이다. 
그 다음부터는 컴포넌트의 상태가 변경 될 때마다 (예 : `setState`가 호출 됨) 컴포넌트의 렌더링 메소드가 호출되고 새 가상 `DOM`이 만들어지고, 이 새로운 가상 `DOM`은 이전 가상 `DOM`과 비교된다. 이 비교작업의 결과는 실제 `DOM` 변경 사항을 나타내게되고 그러면 `DOM`이 변경된 내용으로 '패치'되어 화면이 변경되게된다.

> 아직 속도와 온도 변경에 따라 차 모델 정보가 변경되지 않는다. 이는 나중에 최종적으로 구현 할 것이다.

## Aircon and Heating Controls
우리는 온도 값을 모니터하면서 20도 이상이 되면 'heating'을 'aircon'으로 변경하고, 20도 이하가 되면 다시 'heating'으로 바꿔줘야 한다.

먼저 `src/components/TeslaClimate` 디렉토리를 생성하고 그 안에 `TeslaClimate.js` 파일을 만들고 다음의 코드를 입력하자.

```
import React from 'react';
import './TeslaClimate.css';

const TeslaClimate = (props) => (
  <div className="tesla-climate">
    <label
      className={`tesla-climate__item ${props.value ? 'tesla-climate__item--active' : '' }  ${!props.limit ? 'tesla-heat':''}`}
    >
      <p>{props.limit ? 'ac' : 'heat'} {props.value ? 'on' : 'off'}</p>
      <i className="tesla-climate__icon"></i>
      <input
        type="checkbox"
        name="climate"
        checked={props.value}
        onChange={() => {props.handleChangeClimate()}}
      />
    </label>
  </div>
);

TeslaClimate.propTypes = {
  value: React.PropTypes.bool,
  limit: React.PropTypes.bool,
  handleChangeClimate: React.PropTypes.func
}

export default TeslaClimate;
```
이 컴포넌트에서는 전달받은 `props.value` 에 따라 스타일 클래스를 바꿔주고, `props.limit`에 따라 텍스트를 변경해준다.
`TeslaBattery`는 상태를 업데이트해야 할 때마다 실행되는 `callback`(이 경우엔 `handleChangeClimate`)을 `TeslaClimate`에 전달한다. `input` `onChange` 이벤트를 사용하여 이벤트를 알릴수 있다. `TeslaBattery`에 의해 전달된 `callback`은 `setState()`를 호출하여 상태를 업데이트하고 리렌더링된다.

### TeslaClimate Component Style
`src/components/TeslaClimate ` 디렉토리안에 `TeslaClimate.css` 파일을 만들고 다음 스타일을 지정한다. 코드가 길어 여기서는 생략하였으므로 [소스코드](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaClimate/TeslaClimate.css)를 확인해서 작업하도록 하자.

```
  .tesla-climate {
	  float: left; 
  }
  .tesla-climate__item {
    cursor: pointer;
    display: block;
    width: 100px;
    height: 100px;
    border: 6px solid #f7f7f7;
    border-radius: 50%;
    box-shadow: 0px 1px 3px rgba(0, 0, 0, 0.3);
    color: #666;
    background: #fff; 
  }
  ...
```

### Import TeslaClimate component in TeslaBattery Container
이제 우리는 `TeslaBattery`에 `callback`을 구현해 `TeslaClimate` 컴포넌트로 전달해 보겠다.
먼저 `TeslaBattery.js`에서 `TeslaClimate ` 컴포넌트를 사용할 수 있도록 `import`한다. 그리고 `callback` 함수인 `handleChangeClimate()`를 구현하고 `constructor()`내에 바인딩한다. 그 후 `callback` 함수를 `TeslaClimate` 컴포넌트에 `props`로 전달한다.

```
...
import TeslaClimate from '../components/TeslaClimate';
...
constructor(props) {
  super(props);
  ...
  this.handleChangeClimate = this.handleChangeClimate.bind(this);
  ...
}
// handle aircon & heating click event handler
handleChangeClimate() {
  const config = {...this.state.config};
  config['climate'] = !this.state.config.climate;
  this.setState({ config });
}

...
<TeslaClimate
  value={this.state.config.climate}
  limit={this.state.config.temperature > 10}
  handleChangeClimate={this.handleChangeClimate}
/>  
...
```
이제 온도 변화에 따라 상태값이 변하게 되고 이 변경된 값이 TeslaClimate 컴포넌트로 전달되면 그 값에 따라 스타일 클래스와 텍스트가 변경되어진다. 

![](https://lh3.googleusercontent.com/Bgbxy2TwAnqqX6itMN0HrWTRt6g93eJWUuCNJWiBBFgSMogW1AumIfBecMQdr9MZ0CMqiG6Gog=s944 "climate.gif")

##TeslaWheels Component
드디어 마지막 컴포넌트인 TeslaWheels를 만들어 보겠다.
늘하던데로 `src/components/TeslaWheels` 디렉토리를 생성하고 그 안에 `TeslaWheels ` 파일을 만들고 다음의 코드를 입력하자.

```
import React from 'react';
import './TeslaWheels.css';

const LabelLists = (props) => {
  const value = props.wheels.value;
  const changeHandler = props.wheels.handleChangeWheels;
  const sizes = [19, 21];
  const LabelItems = sizes.map(size => (
    <label key={size} className={`tesla-wheels__item tesla-wheels__item--${size} ${value === size ? 'tesla-wheels__item--active' : '' }`}>
      <input
        type="radio"
        name="wheelsize"
        value={size}
        checked={value === size} 
        onChange={() => {changeHandler(size)}} />
      <p>
        {size}"
      </p>
    </label> 
    )
  );
  return (
    <div>
      {LabelItems}
    </div>
  );
}
const TeslaWheels = (props) => (
  <div className="tesla-wheels__component">
    <p className="tesla-wheels__title">Wheels</p>
    <div className="tesla-wheels__container cf">
      <LabelLists wheels={props}/>
    </div>
  </div>
);
TeslaWheels.propTypes = {
  value: React.PropTypes.number,
  handleChangeWheels: React.PropTypes.func
}
export default TeslaWheels;
```
여기서 우리가 구현한것은 `TeslaStats`컴포넌트에서 `props` 배열 오브젝트를 리스트로 변환했던 것과 유사하다. 
`JavaScript map()` 함수를 사용하여 `props.sizes` 배열을 반복한다. 매 반복마다 `size`가 담긴 `<label>` 엘리먼트들을 리턴한다. 최종적으로 `LabelItems` 리스트가  `TeslaWheels` 컴포넌트에 포함되어 렌더링 되는 구조이다. `<label>` 엘리먼트내에서는 전달된 wheel size에 따라 해당 클래스를 변경함으로서 wheel animation 효과를 나타낸다. 

### TeslaWheels Component Style
`src/components/TeslaWheels ` 디렉토리안에 `TeslaWheels.css` 파일을 만들고 다음 스타일을 지정한다. 코드가 길어 여기서는 생략하였으므로 [소스코드](https://github.com/gyver98/react-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaWheels/TeslaWheels.css)를 확인해서 작업하도록 하자.

```
.tesla-wheels__component {
  float: left;
  width: 355px;
}
.tesla-wheels__title {
  letter-spacing: 2px;
  font-size: 16px;
}
...
```

### Import TeslaWheels component in TeslaBattery Container
마지막으로 `TeslaBattery`에 `callback`을 구현해 `TeslaWheels` 컴포넌트로 전달한다.
먼저 `TeslaBattery.js`에서 `TeslaWheels` 컴포넌트를 사용할 수 있도록 `import`한다. 그리고 `callback`함수인 `handleChangeWheels()`를 구현하고 `constructor()`내에 바인딩한다. 그 후 `callback`함수를 `TeslaWheels `컴포넌트에 `props`로 전달한다.


```
...
import TeslaWheels from '../components/TeslaWheels';
...
constructor(props) {
    super(props);
    this.calculateStats = this.calculateStats.bind(this);
    this.increment = this.increment.bind(this);
    this.decrement = this.decrement.bind(this);
    this.handleChangeClimate = this.handleChangeClimate.bind(this);
    this.handleChangeWheels = this.handleChangeWheels.bind(this);
    this.statsUpdate = this.statsUpdate.bind(this);
...
handleChangeWheels(size) {
  const config = {...this.state.config};
  config['wheels'] = size;
  this.setState({ config });
}
...
<TeslaWheels
  value={this.state.config.wheels}
  handleChangeWheels={this.handleChangeWheels}
/>
...
```

wheels animation이 완성된 결과 화면은 다음과 같다.
![](http://g.recordit.co/ZEz2AupcIm.gif)

## State Update

드디어 완성? 
사용자가 여러 조건값들을 변경해도 아직 차 모델 값이 적절하게 바뀌지 않는다.
지금까지 우리는 이벤트가 발생할 때마다  우리 앱 상태의 한 부분만을 업데이트해왔다.

```
this.setState({ config });
```

이제 config 상태 값이 변경될때마다 carstats 상태 값도 변경되도록 해보자.

```
statsUpdate() {
  const carModels = ['60', '60D', '75', '75D', '90D', 'P100D'];
  // Fetch model info from BatteryService and calculate then update state
  this.setState({
  carstats: this.calculateStats(carModels, this.state.config)
  })
}
```

carModels와 현재 변경된 상태값을 입력으로 받아 변경된 carStats을 앱 state에 반영하는 함수를 만들고 이를 callback으로 this.setState()에 전달한다. 이렇게 함으로서 asyncronus 방식으로 동작하는 setState() 에서 cofig 오브젝트를 먼저 업데이트 하고 이를 기반으로 변경된 stats를 화면에 렌더링하는 것이 가능해진다.

```
this.setState({ config }, () => {this.statsUpdate()});
``` 

이로써 모든 퍼즐이 완성되었다. TeslaBattery의 전체 코드는 다음과 같다.

```
import React from 'react';
import './TeslaBattery.css';
import TeslaNotice from '../components/TeslaNotice/TeslaNotice';
import TeslaCar from '../components/TeslaCar/TeslaCar';
import TeslaStats from '../components/TeslaStats/TeslaStats';
import TeslaCounter from '../components/TeslaCounter/TeslaCounter';
import TeslaClimate from '../components/TeslaClimate/TeslaClimate';
import TeslaWheels from '../components/TeslaWheels/TeslaWheels';
import { getModelData } from '../services/BatteryService';

class TeslaBattery extends React.Component {
  constructor(props) {
    super(props);

    this.calculateStats = this.calculateStats.bind(this);
    this.statsUpdate = this.statsUpdate.bind(this);
    this.increment = this.increment.bind(this);
    this.decrement = this.decrement.bind(this);
    this.updateCounterState = this.updateCounterState.bind(this);
    this.handleChangeClimate = this.handleChangeClimate.bind(this);
    this.handleChangeWheels = this.handleChangeWheels.bind(this);

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

  updateCounterState(title, newValue) {
    const config = { ...this.state.config };
    // update config state with new value
    title === 'Speed' ? config['speed'] = newValue : config['temperature'] = newValue;
    // update our state
    this.setState({ config }, () => {this.statsUpdate()});
  }

  increment(e, title) {
    e.preventDefault();
    let currentValue, maxValue, step;
    const { speed, temperature } = this.props.counterDefaultVal;
    if (title === 'Speed') {
      currentValue = this.state.config.speed;
      maxValue = speed.max;
      step = speed.step;
    } else {
      currentValue = this.state.config.temperature;
      maxValue = temperature.max;
      step = temperature.step;
    }

    if (currentValue < maxValue) {
      const newValue = currentValue + step;
      this.updateCounterState(title, newValue);
    }
  }

  decrement(e, title) {
    e.preventDefault();
    let currentValue, minValue, step;
    const { speed, temperature } = this.props.counterDefaultVal;
    if (title === 'Speed') {
      currentValue = this.state.config.speed;
      minValue = speed.min;
      step = speed.step;
    } else {
      currentValue = this.state.config.temperature;
      minValue = temperature.min;
      step = temperature.step;
    }

    if (currentValue > minValue) {
      const newValue = currentValue - step;
      this.updateCounterState(title, newValue);
    }
  }

  // handle aircon & heating click event handler
  handleChangeClimate() {
    const config = {...this.state.config};
    config['climate'] = !this.state.config.climate;
    this.setState({ config }, () => {this.statsUpdate()});
  }

  // handle Wheels click event handler
  handleChangeWheels(size) {
    const config = {...this.state.config};
    config['wheels'] = size;
    this.setState({ config }, () => {this.statsUpdate()});
  }  

  render() {    
    const { config, carstats } = this.state;
    return (
      <form className="tesla-battery">
        <h1>Range Per Charge</h1>
        <TeslaCar wheelsize={config.wheels} />
        <TeslaStats carstats={carstats} />
        <div className="tesla-controls cf">
          <TeslaCounter
            currentValue={this.state.config.speed}
            initValues={this.props.counterDefaultVal.speed}
            increment={this.increment}
            decrement={this.decrement}
          />
          <div className="tesla-climate-container cf">
            <TeslaCounter
              currentValue={this.state.config.temperature}
              initValues={this.props.counterDefaultVal.temperature}
              increment={this.increment}
              decrement={this.decrement}
            />
            <TeslaClimate
              value={this.state.config.climate}
              limit={this.state.config.temperature > 10}
              handleChangeClimate={this.handleChangeClimate}
            />
          </div>
          <TeslaWheels
            value={this.state.config.wheels}
            handleChangeWheels={this.handleChangeWheels}
          />
        </div>
        <TeslaNotice />
      </form>
    )
  }
}

export default TeslaBattery;
```


와우! 완성된 Tesla Batter Range Calculator는 다음과 같다.


![finalcar](https://lh3.googleusercontent.com/ADOBXOthirfSi9f9j-f2giwZc_9Gtlb6qcNAmnR0y1rLVBKvRRyG4Zf5oPkvtlXE2dsKKFy0Bw=s944 "final.gif")




