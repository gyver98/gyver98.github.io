---
layout:     post
title:      Polishing App with CSS Animation
date:       2017-03-23 15:25:43
summary:    Polishing App with CSS Animation
categories: blog development react
---

이 글은 Building Tesla's Battery Range Calculator with React 시리즈의 3번째 파트이다.

파트 1에서는 React core만으로 애플리케이션을 완성하였고, 파트 2에서는 상태 관리 솔루션인 Redux를 도입하여 애플리케이션을 변환하였다.

This is the third part of Building Tesla's Battery Range Calculator with React series.

In part 1, we've created the application with only the React core, and in part 2 we've transformed the application by introducing Redux, a state management solution.

> Check out [Building Tesla's Battery Range Calculator with React Part 1]()

> Check out [Building Tesla's Battery Range Calculator with React Part 2 (Redux version)]()

지금까지 작업한 애플리케이션을 보면 뭔가 좀 평이하고 심심하다. 
약간 쇼룸같은 우리 앱의 특성상 뭔가 좀 다이나믹한 효과를 주면 더 근사해 보일것 같다.
앱이 로딩될때 우리의 멋진 테슬라 카 이미지가 어디선가 날아오고 그 다음에 바퀴가 어디선가 떨어지면서 휠 부분에 장착되는 느낌이면 좋을거 같다.
카 이미지 뿐 아니라 Tesla 헤더도 fade-in 효과로 보여지고 나머지 컴포넌트들도 좀 뭔가 멋지게 등장하면 좋을거 같다. 
로딩되고 나서 사용자 이벤트가 일어날때도 그냥 수치 값만 변경될게 아니라 눈에 확 띄는 효과를 주면 더 효과적일거 같다.

두둥! 바로 다음과 같이 말이다.

If you look at an application we've worked on so far, it's a bit plain and bored.
It looks a little cooler if we give it a bit more dynamic effect due to the nature of our app, like a car showroom.

When the app is loaded, it's a good idea that our stunning Tesla car images are coming from somewhere and then the wheels are falling somewhere and mounted on the wheel.
With a little transformer feel. 
The Tesla header is also shown as a fade-in effect, and the rest of the components should look a little nicer.

Even when the user event occurs after loading, it will be more effective to give a noticeable effect instead of just changing the numerical value

Just like this:

![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/tesla-animation.gif?raw=true)

> Check out [live demo]() 

이번 파트 3에서는 CSS animation을 이용하여 우리의 완성된 앱을 좀 더 멋지게 만드는 과정을 단계별로 살펴보고자 한다.

In Part 3, we'll take a step-by-step look at the process of making our finished apps more stylish using CSS animation.

## 1. Warming up

본격적으로 시작하기에 앞서 준비 운동부터 해보자.

### 1-1. Basic example CSS animation

CSS 애니메이션은 element가 한 스타일에서 다른 스타일로 점진적으로 변경되도록 한다.
CSS animation lets an element gradually change from one style to another.

CSS animation consist of two components :
* A style describing the CSS animation
* A set of keyframes that indicate the start and end states of animation's style

여기 간단한 예제가 있다.
이 예제에서는 한 element의 background color를 5초간에 걸쳐 red 에서 yellow로 점진적으로 바꾼다.

Here is a simple example.
In this example, the background color of one element is gradually changed from red to yellow over 5 seconds.

<p data-height="365" data-theme-id="0" data-slug-hash="KWeNwe" data-default-tab="css,result" data-user="mattchoi" data-embed-version="2" data-pen-title="animation example" class="codepen">See the Pen <a href="http://codepen.io/mattchoi/pen/KWeNwe/">animation example</a> by mattchoi (<a href="http://codepen.io/mattchoi">@mattchoi</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

To create a CSS animation, you need two steps :

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/creating-ani.png?raw=true)_

> Check out [Using CSS animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) for more details

### 1-2. Another example CSS animation

Here’s what we’re starting with.

It’s got a Tesla header, some title and a nice Tesla car image.

Codepen sample
<p data-height="460" data-theme-id="0" data-slug-hash="zZjEbK" data-default-tab="css,result" data-user="mattchoi" data-embed-version="2" data-pen-title="TeslaCar" class="codepen">See the Pen <a href="http://codepen.io/mattchoi/pen/zZjEbK/">TeslaCar</a> by mattchoi (<a href="http://codepen.io/mattchoi">@mattchoi</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

### 1-3 Making it bounce

여기 세 개의 elements가 얌전히 놓여 있다. 아무것도 없는 흰색 배경의 무대에 하나 둘 배우들이 춤을 추며 등장하듯이 이 세 개의 elememnt 들이 흰색의 스크린에 서서히 나타나도록 해보자.

먼저 keyframes를 이용하여 애니메이션 시퀀스를 정의하도록 한다. 그 다음에 animation 속성이 정의된 animate-pop-in 클래스를 div 그리고 h1 element에 적용하도록 하자.

Here are three elements nicely placed. Let's see these three elememnts slowly appear on a white screen, as one or two actors dancing on a stage with nothing on the white background.

First, define the animation sequence using keyframes. Then apply the animate-pop-in class with the animation property defined to the div and h1 elements.

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/animation-pop-in.png?raw=true)_

이제 3개의 elements 들이 6초에 걸쳐 천천히 사이즈가 커지면서 나타나는 것을 볼 수 있다.
Now you can see that the three elements appear slowly increasing in size over 6 seconds.

<p data-height="430" data-theme-id="0" data-slug-hash="QpxpWK" data-default-tab="css,result" data-user="mattchoi" data-embed-version="2" data-pen-title="QpxpWK" class="codepen">See the Pen <a href="http://codepen.io/mattchoi/pen/QpxpWK/">QpxpWK</a> by mattchoi (<a href="http://codepen.io/mattchoi">@mattchoi</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>


### 1-4 Cubic Bezier

우리의 예제에서 animation-timing-function으로 ease-out을 사용하였다.
Easing functions는 시간 경과에 따른 매개 변수의 변경 비율을 지정하는데, 다음과 같은 5가지 함수들이 있다 : ease, ease-in, ease-out, ease-in-out and linear

In our example we used ease-out with an animation-timing-function
Easing functions specify the rate of change of a parameter over time.
There are four predefined timing functions we can use, they are ease, ease-in, ease-out, ease-in-out and linear.

* ease - speeds up a little through the middle, and then slows down towards the end
* ease-in - starts of slowly, and accelerates through to the end
* ease-out - starts of quickly, and decelerates through to the end 
* ease-in-out - starts slowly, accelerates through to the middle, then decelerates through to the end
* linear - constant animation speed throughout

It's important that they are essentially based on the Bezier curve.

![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/cubic-bezier%20function.png?raw=true)

### 1-5 Creating Custom Speed

우리 실생활에서 모든 물체는 일정한 속도로 움직이지 않는것처럼, 스크린에서 움직이는 elements에게도 이러한 변동적인 속도를 부여한다면 더 현실감있는 움직임을 줄 수 있을것이다. 

As in our real life, not all objects move at a constant speed, giving these variable speeds to the elements on the screen will give a more realistic movement.
Using Cubic-bezier function, we are able to create custom speed.

그러나 cubic-bezier함수 포맷으로 애니메이션 스피드를 정의하는 것은 직관적이지 않다. 여기 cubic-bezier가 어떻게 동작하는지 시각화해서 보여주는 툴을 소개한다 [cubic-bezier.com](http://cubic-bezier.com/)

However, defining the animation speed in the cubic-bezier function format is not intuitive. Here is a tool to visualize how cubic-bezier works.[cubic-bezier.com](http://cubic-bezier.com/)

![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/cubic-bezier%20site.png?raw=true)

여기 ease-out 대신 커스텀 스피드를 적용한 예제를 확인해 보자.
Here's an example of applying custom speed instead of ease-out.

<p data-height="374" data-theme-id="0" data-slug-hash="NpBZQQ" data-default-tab="css,result" data-user="mattchoi" data-embed-version="2" data-pen-title="NpBZQQ" class="codepen">See the Pen <a href="http://codepen.io/mattchoi/pen/NpBZQQ/">NpBZQQ</a> by mattchoi (<a href="http://codepen.io/mattchoi">@mattchoi</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>


Now that the warm-up is over, let's get started.

## 2. Excersize

데모에서 보았듯이 우리는 앞으로 크게 두 가지 시점에 애니메이션을 적용할 것이다.
* 애플리케이션이 로딩될때
* 사용자 액션이 발생될때

As we saw in the demo, we will apply animation to two points in our application. 
* when an application is loaded 
* When a user action occurs 

### 2.1 when an application is loaded

우리 앱의 전체 레이아웃을 살펴보자. 파트1, 파트2에서 만들었던 앱의 컴포넌트 구조는 전혀 바뀌지않는다. 단지 해당 css 파일에 animation 속성과 @keyframes를 정의하면된다. 수정되어야 할 css 파일은 녹색으로 표시하였다.

Let's look at the entire layout of our app. The component structure of the app we created in Part 1, Part 2 does not change at all. Just it needs to define animation attribute and @keyframes in the corresponding css file.

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/app-layout.png?raw=true)_

### 2.1.1 Introducing Animista

애니메이션을 적용하기에 앞서 어떠한 효과의 애니메이션들이 있는지, 여러 애니메이션 속성들을 조합했을때 어떤 효과가 적용되는지 한 눈에 살펴보고 적용해볼 수 있는 툴이 있다면 좋지 않겠는가? 여기 바로 그러한 툴이 있어 소개한다. animista.net

Would not it be nice to have a tool that lets us see what effect animations are possible before applying animations, and how to see and apply what effects are applied when you combine multiple animation properties in a single place? Check out such a great tool. [Animista!](http://animista.net/)

이 툴을 통해 우리는 마음껏 애니메이션 효과들을 만져보고 테스트해볼 수 있다. 
마음에 드는 애니메이션을 찾았다면 Generate code 버튼을 클릭하여 해당 애니메이션 속성 값을 카피한 후 우리 앱의 해당 css에 적용하면된다. 이때 Add to favouriates 버튼을 클릭 후 다운로드 페이지에서 keyframes 코드를 카피하여 사용하도록 한다.

With this tool, we can touch and test animated effects at will. Once you find the animation you like, click on the Generate code button to copy the animation property value and apply it to the corresponding css in our app. Don't forget to click the Add to Favourities button and grab the keyframes code from the download page.

![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/animista.gif?raw=true)

### 2.1.2 Set up part2 project code base

If you want to go directly to part 3 without looking at part 2, you need to build the codebase first by cloning the part 2 code.

* git clone https://github.com/gyver98/redux-tesla-battery-range-calculator-tutorial

* npm install

* npm start

After the npm start, let’s make sure the application works.

### 2.1.3 Tesla Header

Tesla Header 컴포넌트에는 서서히 로고가 나타나는 fade-in 효과를 주도록 한다.

먼저 Header.css 파일을 열고 Animista에서 카피한 animation 속성과 keyframes 값을 붙여넣는다.
그 후 Header.js 파일에서 이 클래스를 참조하도록 수정한다.

The Tesla Header component should have a fade-in effect that gradually displays the logo.
First, open the Header.css file and paste the animation properties and keyframes values copied from Animista. Then modify it to refer to this class in the Header.js file.


_![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/Header.png?raw=true)_

* Check out [Header.css](https://gist.github.com/gyver98/6f13ec5d1fbf5aa5659b50b3a8d88c09#file-header-css)
* Check out [Header.js](https://gist.github.com/gyver98/d1d4eb07a86f61a46b09ff83982c6cf2#file-header-js)

### 2.1.4 <h1> title

타이틀에는 focus-in-contract-bck 효과가 어울릴것 같다.
Tesla Header에서 한것과 동일한 방식으로 애니메이션 효과를 주도록 한다.
다만 타이틀 <h1> 엘리먼트는 App.js/App.css에 정의되어있다는데 유의하자.

The focus-in-contract-bck effect is likely to work for Tesla title. Give the animation effect the same way you did in Tesla Header. Note that the title <h1> element is defined in App.js / App.css.

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/h1.png?raw=true)_

* Check out [App.js](https://github.com/gyver98/part3-animation-tesla-battery-range-calculator-tutorial/blob/master/src/App.js)
* Check out [App.css](https://github.com/gyver98/part3-animation-tesla-battery-range-calculator-tutorial/blob/master/src/App.css)

### 2.1.5 TeslaCar

가장 역동적인 효과를 줄 수 있는 TeslaCar 컴포넌트에는 다음과 같이 두 가지 효과를 적용하도록 하자.
* Tesla Car : slide-in-elliptic-bottom-fwd
* Tesla Wheels : bounce-in-top

/src/Components/TeslaCar/TeslaCar.css

```
.tesla-car-animation {
  -webkit-animation: slide-in-elliptic-bottom-fwd 0.7s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
	-moz-animation: slide-in-elliptic-bottom-fwd 0.7s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
	animation: slide-in-elliptic-bottom-fwd 0.7s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
  }  

.tesla-wheels-animation {
  -webkit-animation: bounce-in-top 2.3s both;
	-moz-animation: bounce-in-top 2.3s both;
	animation: bounce-in-top 2.3s both;
}

@-webkit-keyframes slide-in-elliptic-bottom-fwd{0%{-webkit-transform:translateY(600px) rotateX(30deg) scale(0);transform:translateY(600px) rotateX(30deg) scale(0);-webkit-transform-origin:50% 100%;transform-origin:50% 100%;opacity:0}100%{-webkit-transform:translateY(0) rotateX(0) scale(1);transform:translateY(0) rotateX(0) scale(1);-webkit-transform-origin:50% -1400px;transform-origin:50% -1400px;opacity:1}}
@keyframes slide-in-elliptic-bottom-fwd{0%{-webkit-transform:translateY(600px) rotateX(30deg) scale(0);transform:translateY(600px) rotateX(30deg) scale(0);-webkit-transform-origin:50% 100%;transform-origin:50% 100%;opacity:0}100%{-webkit-transform:translateY(0) rotateX(0) scale(1);transform:translateY(0) rotateX(0) scale(1);-webkit-transform-origin:50% -1400px;transform-origin:50% -1400px;opacity:1}}


@-webkit-keyframes bounce-in-top{0%{-webkit-transform:translateY(-500px);transform:translateY(-500px);-webkit-animation-timing-function:ease-in;animation-timing-function:ease-in;opacity:0}38%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out;opacity:1}55%{-webkit-transform:translateY(-65px);transform:translateY(-65px);-webkit-animation-timing-function:ease-in;animation-timing-function:ease-in}72%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out}81%{-webkit-transform:translateY(-28px);transform:translateY(-28px);-webkit-animation-timing-function:ease-in}90%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out}95%{-webkit-transform:translateY(-8px);transform:translateY(-8px);-webkit-animation-timing-function:ease-in;animation-timing-function:ease-in}100%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out}}

@keyframes bounce-in-top{0%{-webkit-transform:translateY(-500px);transform:translateY(-500px);-webkit-animation-timing-function:ease-in;animation-timing-function:ease-in;opacity:0}38%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out;opacity:1}55%{-webkit-transform:translateY(-65px);transform:translateY(-65px);-webkit-animation-timing-function:ease-in;animation-timing-function:ease-in}72%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out}81%{-webkit-transform:translateY(-28px);transform:translateY(-28px);-webkit-animation-timing-function:ease-in}90%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out}95%{-webkit-transform:translateY(-8px);transform:translateY(-8px);-webkit-animation-timing-function:ease-in;animation-timing-function:ease-in}100%{-webkit-transform:translateY(0);transform:translateY(0);-webkit-animation-timing-function:ease-out;animation-timing-function:ease-out}}

```

/src/Components/TeslaCar/TeslaCar.js

```
const TeslaCar = (props) => (
  <div className="tesla-car tesla-car-animation">
    <div className="tesla-wheels tesla-wheels-animation">
      
    </div>
  </div>
);
```

* Check out [TeslaCar.js](https://github.com/gyver98/part3-animation-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaCar/TeslaCar.js)
* Check out [TeslaCar.css](https://github.com/gyver98/part3-animation-tesla-battery-range-calculator-tutorial/blob/master/src/components/TeslaCar/TeslaCar.css)

지금까지 적용한 애니메이션은 다음과 같다.

![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/animation1.gif?raw=true)







