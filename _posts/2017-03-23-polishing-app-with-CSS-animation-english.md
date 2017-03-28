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

_![](https://github.com/gyver98/blog-images/blob/master/2017-03-23-polishing-app-with-CSS-animation-english/animation-pop-in.png?raw=true)_
