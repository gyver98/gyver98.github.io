---
layout: post
title: 'React Music Application :Part1'
date: 2017-01-29 15:45:34
summary: Hello React! 간단한 React 뮤직 플레이어 애플리케이션 만들기 part 1 
categories: blog development react
tags: blog react
---

React application 만들기 Part 1
==================

새롭게 배운 내용을 정리하고 공유하기 위해 블로그를 운영하기로 결심하고 처음으로 작성하는 포스트.
이 글에서는 React를 공부하면서 배운 것들, 인터넷을 통해 참조한 여러 자료들을 바탕으로 어떻게 간단한 음악 앨범을 조회하는 application을 만드는지를 공유하고자 한다.


React란?
-------------

React는 Facebook에서 개발한 유저 인터페이스를 위한 JavaScript library이다.
React는 컴포넌트(Component) 기반 architecture 인데 가장 중요한 구성단위인 컴포넌트는 UI를 구성하는 독립적이고 재사용이 가능한 조각이며 개념적으로 React에서의 컴포넌트는 JavaScript의 functions와 같다.
컴포넌트는 `Props` 라 불리우는 inputs 을 받아들여 화면에 무엇이 보여져야 하는지를 묘사하는 `React elements`를 리턴한다. 
이러한 아이디어는 한 마디로 다음과 같은 공식으로 표현할 수 있다.

> fn(d) = V.


데이타를 입력으로 받고 view를 리턴하는 함수.

Create React App
-------------
`Create React App`은 React application 개발을 위한 새로운 툴로서 복잡한 설정없이 바로 React 프로젝트를 시작할 수 있게 도와준다.
다음의 명령을 통해 쉽게 설치하고 애플리케이션을 바로 시작할 수 있다.

>- npm install -g create-react-Application
>- create-react-app my-app
>- cd my-app/
>- npm start

`Create React App`을 통해 새로운 Application을 생성한 후 `http://localhost:3000/` 을 오픈하여 생성된 application을 확인해보자.
아래의 화면이 보인다면 성공적으로 프로젝트가 설정된 것이다.

![](https://lh3.googleusercontent.com/EeKbF6zn5lnouSSdulf4uvRPqxxCEo75P-shtCF5Fh4aOb3A3Xsu7sbSsiitLfd-UggKlz3D5Q=s944 "Screen Shot 2017-02-01 at 3.21.15 pm.png")

Breaking Down the UI
-------------
거의 모든 React application UI는 여러 <span class="bg-dark-gray white">컴포넌트들의 조합</span>으로 구성되어진다. 
날씨 앱을 예로들자면, 지역명을 보여주는 컴포넌트, 현재 기온을 보여주는 컴포넌트, 5일간의 예측을 나타내는 그래프 컴포넌트들로 구성되어진다.

이러한 이유로 React 앱 개발에 앞서 UI를 컴포넌트 단위로 분해하여 보는것이 좋다.

> 애플리케이션을 컴포넌트들의 조합으로 바라보는 접근 방식에 대해서는 [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)를 참조

이 포스트에서 만들어볼 얘플리케이션의 레이아웃은 다음과 같다.
![](https://lh3.googleusercontent.com/ycAd5tgymot5yzEQ4s2fJP4-Om3z73NYEmRcsvt9JsaMYcLlMl4DCaqtCw8dbB1PFO4YdLFCLA=s944 "music_search")

UI를 컴포넌트 단위로 분해해보면 다음과 같다.
![](https://lh3.googleusercontent.com/JEYvOdX3ww3CAahxq4k3v1XBfA9h_4UXY9NINOrUlJgKL_pOfNORZN1WL9fBOjrFB_-TDu_A5w=s944 "Screen Shot 2017-02-01 at 2.11.14 pm.png")

여기서 두 가지를 짚고 넘어가자.

1. 개별적인 Album 컴포넌트의 인스턴스들이 Album list 컴포넌트에 속해있다는데 주목하자. 보통 컴포넌트 B가 컴포넌트 A에 속해있을때 우리는 컴포넌트 B가 컴포넌트 A에 중첩되어있다고 한다. 또는 컴포넌트 B는 컴포넌트 A의 자식이다라고 할 수 있다. (Parent - Child relationship)
2. 실질적으로 여기에 4개의 컴포넌트가 있다. 4번째 컴포넌트는 전체 앱의 컨테이너 역할을 하는 App 컴포넌트이다. 이렇게 UI 진입점 역할을 하면서 전체 앱의 상태를 초기화 하고 자식 컴포넌트들이 어떻게 구성되어야 하는지 조절하는 가장 최상위 컴포넌트를 두는 것이 좋다.

The App Container
-------------
시작하기 전에 먼저 프로젝트 소스 구조를 정리하자.
우리 프로젝트에 필요한 파일만 남기고 나머지는 삭제하도록 하자. (deleted App.test.js , logo.svg)
이제 우리 src 디렉토리는 다음과 같이 보여야 한다.

```
src
 - App.css
 - App.js
 - index.css
 - index.js
```


{% include disqus.html %}
