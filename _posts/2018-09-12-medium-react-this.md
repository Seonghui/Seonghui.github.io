---
layout: post
title: "[미디엄 번역] React의 'this' 알아보기"
tags: [react, this, medium]
---

> 해당 포스트는 medium 포스트의 [What is 'this' in React?](https://medium.com/byte-sized-react/what-is-this-in-react-25c62c31480)를 번역한 문서입니다.

왜 항상 우리는 this를 사용할까요?
---

React를 하다 보면 아마 엄청나게 많은 this를 볼 수 있습니다. 이제부터 자바스크립트와 React가 사용하는 this가 무엇인지 이제부터 알아봅시다.

# this 키워드
this 키워드는 보통 스코프나 문맥에 따라 달라집니다. 몇 가지 예시로 알아봅시다.

## 전역 스코프의 this
브라우저에서 가장 단순한 스코프는 바로 전역 스코프입니다. 만약 브라우저의 콘솔 창을 열고 다음 코드를 실행시키면...

```javascript
this
```
이런 결과를 반환할 것입니다.

```
Window {frames: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, …}
```

윈도우 객체에서 실행하는 명령은 전역 스코프의 this를 사용해서도 실행할 수 있습니다.

```javascript
this.alert('Hey!')
//Is the same as
Window.alert('Hey!')
this.location = 'https://medium.com'
//Is the same as
Window.location = 'https://medium.com'
```

## 함수에서의 this
여기서의 this 는 약간 애매해서 렉시컬 스코프에 대한 개념을 알고 있어야 이해하기 쉽습니다.

자바스크립트 함수 안에서의 this 값은 함수가 어디서 호출되었느냐에 따라 그 값이 결정됩니다. call, apply, 그리고 bind 같은 메소드들을 사용하면 명확하게 this 값을 설정할 수 있습니다. 그런데 this 값이 설정되어있지 않다면, this는 전역 컨텍스트를 기본값으로 설정할 것입니다.

```javascript
const thisFunction = function () {
 return this;
}
//Calling thisFunction() in the browser will return the window object
```

만약 엄격 모드(strict mode)에서, 함수 안에서의 this는 명시적인 값을 참조하며 this가 명시적으로 설정되지 않은 경우 this는 undefined가 됩니다.

```javascript
const strictFunction = function() {
 'use strict';
 return this;
}
//Calling strictFunction() in the browser will return undefined 
//since this is not explicitly set with call, apply, or bind
```

### call, apply, bind와 화살표 함수를 사용해서 this 값을 명시적으로 설정하기
어떻게 함수에서 this를 명시적으로 설정할 수 있을까요? call, apply, 그리고 bind 메소드를 사용하면 됩니다.

함수의 call 메소드는 this가 참조할 객체와 함수에 정의된 다른 매개 변수들을 받아들입니다. 그리고 apply 메소드는 this가 참조할 객체와 함수 정의 시 매개 변수 참조를 포함하는 배열을 받아들입니다.

(+ 쉽게 말하면 call 메소드는 인자를 전달하고, apply 메소드는 배열을 전달합니다. 각 메소드에서 this는 동일한 역할을 합니다.)

```javascript
const object = {
 a: 5,
 b: 7
}
const thisFunction = function(c, d) {
 return this.a + this.b + c + d;
}
thisFunction.call(object, 12, 4);
//will return 28
thisFunction.apply(object, [3, 6]);
//will return 21
```

bind 메소드는 call과 apply와 다릅니다. bind 메소드는 객체를 함수에 연결하므로 함수가 호출될 때마다 바운드된 객체를 참조합니다.

(+ 쉽게 말해서 함수와 객체를 서로 묶어주는 것입니다.)

```javascript
const object = {a: 2, b:3, c:6};
const thisFunction = function() {
 return this.a + this.b + this.c;
}
const bindFunction = thisFunction.bind(object);
bindFunction(); //will return 11
```

## 객체의 메소드 안에서의 this
객체에서 this를 사용할때, 여기서의 this는 객체 자신을 참조합니다. 이것은 객체의 메소드 안에 있는 객체값을 쉽게 참조할 수 있게 해줍니다.

```javascript
const object = {
 a: 2,
 b: 3,
 thisFunction: function(){
 return this.a + this.b;
 }
};
object.thisFunction() //returns 5
```

# 그렇다면 React에서의 this는 무엇을 의미할까요?
우리가 메소드들을 정의한 컴포넌트 클래스들은 props나 state 같은 클래스 속성들을 참조합니다. 그런데 우리의 메소드가 this.state와 this.props에 접근하려면 this를 해당 메소드에 바인드해야 합니다.

```
import React, { Component } from 'react';
class App extends Component {
  constructor(props) {
    super(props);
    this.clickFunction = this.clickFunction.bind(this);
  }
  clickFunction() {
    console.log(this.props.value);
  }
  render() {
    return(
      <div onClick={this.clickFunction}>Click Me!</div>
    );
  }
}
```

this를 클래스 메소드에 바인딩하면, this.props와 this.state를 사용해서 컴포넌트의 props와 state에 접근할 수 있습니다.

## 화살표 함수
클래스 메소드에 this를 바인딩하면 많은 [보일러 플레이트](http://devstella.tistory.com/3)를 생성할 수 있습니다. ES6에서 소개된 화살표 함수는 this가 이미 함수에 바인딩된 함수입니다. 이런 겁나 좋은 기능덕에 public 클래스 필드를 사용해서 자동으로 this를 우리 메소드에 바인딩할 수 있는 것이죠.

```javascript
const myFunction = () => {
 return this.props.a + this.props.b;
}
//The previous stated myFunction is the same as...
const myFunction = function() {
  return this.props.a + this.props.b;
}.bind(this);
```

이런 화살표 함수와 public 클래스 필드의 조합은 React 구성 요소를 정의하는 명확하고 선언적인 방법을 보여줍니다.