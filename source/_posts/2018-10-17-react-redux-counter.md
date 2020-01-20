---
title: "[React] 초심플 카운터 만들기 with Redux"
tags: [react, study]
categories: React
---

> Github에서 코드 확인하기 - [클릭](https://github.com/Seonghui/redux-counter)

# Redux

Redux는 페이스북에서 만든 자바스크립트 라이브러리이다. [앞서 말한 Flux]({{site.url}}/react-flux/)의 아이디어를 발전시키되 복잡성을 줄였다. React만 사용해도 되지만 대규모 애플리케이션의 경우 상태 관리하기가 힘들어진다. 그래서 Redux를 사용해 상태 관리를 하는 것이다.

# Redux의 개념

Redux의 기본 개념은 Flux의 기본 개념과 동일하다. 언급하지 않은 게 있는데, 바로 **구독(Subscribe)**과 **리듀서(Reducer)**라는 개념이다.

## 구독(Subscribe)

구독은 원하는 스토어를 설정해서 그 스토어에서 값을 받아온다고 생각하면 된다. 개념이 유튜브의 구독과 거의 똑같다. 원하는 채널(스토어)을 구독하고 그 채널의 동영상(데이터)를 본다는 것에 비유하면 이해하기가 쉽다. Redux의 내장함수에는 구독 함수가 존재하지만, react-redux를 사용하면 `connect` 함수가 대신 해준다. 따라서 직접 구독 함수를 사용할 일은 거의 없다.

## 리듀서(Reducer)

액션은 무언가가 일어난다는 사실만을 기술한다. 애플리케이션의 상태가 어떻게 바뀌는지 정해주는 것은 리듀서가 할 일이다.

# Redux로 카운터 만들기

## 결과 미리보기

![result](/images/react-redux-counter/result.png)
플러스, 마이너스는 표시된 수를 더하고 뺀다. 랜덤 버튼을 누르면 랜덤한 숫자가 표시된다. 정말 심플한 카운터다.

## 시작하기

React 프로젝트를 생성하고 redux, react-redux를 전부 설치한다. **redux**는 리덕스 모듈이고, **react-redux**는 리액트에서 리덕스를 사용하기 위한 보조 패키지이다.

```
# React 프로젝트 생성하기
$ create-react-app redux-counter

# 폴더 이동
$ cd redux-counter

# redux, react-redux 전부 설치하기
$ yarn add redux react-redux
```

## 디렉토리 구조

주요 디렉토리 구조는 다음과 같다.

```
src
├── App.js
├── index.js
├── actions
│   └── index.js
├── reducers
│   └── index.js
└── components
    └── Counter.js
```

actions 폴더에서는 프로젝트의 모든 action을, reducers 폴더에는 프로젝트의 모든 reducer를 관리한다. 이건 아주 간단한 애플리케이션이기 때문에 index.js 파일 하나만 있으면 된다.

# 컴포넌트 만들기

일단 뷰를 먼저 만들어준다. Counter 컴포넌트를 만들고 App.js에서 불러와주면 된다.

**App.js**

```js
import React, { Component } from "react";
import "./App.css";
import Counter from "./components/Counter.js";

class App extends Component {
  render() {
    return <Counter />;
  }
}

export default App;
```

**/components/Counter.js**

```js
import React, { Component } from "react";

class Counter extends Component {
  render() {
    return (
      <div>
        <p>0</p>
        <button>+</button>
        <button>-</button>
        <button>random</button>
      </div>
    );
  }
}

export default Counter;
```

# 액션 만들기

액션 타입과 액션 생성 함수를 정의한다. 액션은 무언가가 일어난다는 사실만을 적어준다. 액션 생성 함수를 정의할 때는 export를 해주어야 나중에 컴포넌트에서 불러올 수 있다.

**/actions/index.js**

```js
// 액션 타입 정의
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";
export const RANDOM_NUMBER = "RANDOM_NUMBER";

// 액션 생성 함수 정의
export function increment() {
  return {
    type: INCREMENT
  };
}

export function decrement() {
  return {
    type: DECREMENT
  };
}

export function random_number() {
  return {
    type: RANDOM_NUMBER
  };
}
```

# 리듀서 만들기

리듀서는 애플리케이션의 상태가 어떻게 바뀌는지를 알려준다. 리듀서가 여러 개일 때는 redux의 내장함수인 `combineReducers`를 사용해서 리듀서를 하나로 합치는 작업을 한다. 지금 만드는 카운터는 정말 심플해서 리듀서가 하나밖에는 없지만 한번 사용해보았다.

**/reducers/index.js**

```js
// 액션과 내장함수 combineReducers 불러오기
import { INCREMENT, DECREMENT, RANDOM_NUMBER } from "../actions";
import { combineReducers } from "redux";

// 초기 상태(값) 정의
const initialState = {
  number: 0
};

// 리듀서 작성
const counter = (state = initialState, action) => {
  switch (action.type) {
    case RANDOM_NUMBER:
      return {
        ...state,
        number: Math.floor(Math.random() * 10)
      };
    case INCREMENT:
      return {
        ...state,
        number: state.number + 1
      };
    case DECREMENT:
      return {
        ...state,
        number: state.number - 1
      };
    default:
      return state;
  }
};

const counterApp = combineReducers({
  counter
  // 다른 리듀서를 만들면 이 아래에 적어준다
});

export default counterApp;
```

switch-case 문으로 각 액션마다 어떻게 상태가 바뀌는지를 정해준다. 이것도 마찬가지로 export를 해주어야 한다. export한 리듀서는 스토어를 만들 때 사용한다.

## 스토어를 만들고 연동하기

Redux 라이브러리 안에 있는 `createStore` 함수를 사용해서 스토어를 만들어준다. 스토어는 아까 export한 리듀서로 만들 수 있다. 그리고 react-redux 라이브러리안에 있는 `Provider` 함수를 사용해서 `createStore` 함수로 만든 스토어를 연동해주면 된다.

```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";

// createStore와 리듀서 불러오기
import { createStore } from "redux";
import counterApp from "./reducers";

// Provider 불러오기
import { Provider } from "react-redux";

// 스토어 만들기
const store = createStore(counterApp);

// Provider 사용해서 기존의 App 감싸주기
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);

serviceWorker.unregister();
```

## connect 함수를 사용해 스토어 연동하기

마지막으로 컴포넌트에 리덕스 스토어 안에 있는 값과 액션 생성 함수를 연결해주면 된다. 이건 react-redux의 내장함수 `connect` 함수를 사용하면 된다. `connect` 함수로 연동한 스토어에서 받아오는 값과 액션 생성 함수는 this.props로 접근이 가능하다.

스토어를 연동하기 전에 `mapStateToProps`와 `mapDispatchProps`의 개념을 알아야 한다. `mapStateToProps`는 스토어 안에 있는 **값**을 props로 전달해주고, `mapDispatchProps`는 **액션 생성 함수**를 props로 전달해준다.

지금 만든 스토어에는 값이 **number**, 액션 생성 함수는 **increment, decrement, random_number**가 전부이다. number는 `mapStateToProps`를 사용해 props로 전달하고, increment, decrement, random_number는 `mapDispatchProps`를 사용해 props로 전달한다.

**/components/Counter.js**

```js
// 액션 생성 함수와 react-redux의 내장함수 connect 불러오기
import React, { Component } from "react";
import { connect } from "react-redux";
import { increment, decrement, random_number } from "../actions";

class Counter extends Component {
  render() {
    return (
      <div>
        <p>{this.props.number}</p>
        <button onClick={this.props.handleIncrement}>+</button>
        <button onClick={this.props.handledecrement}>-</button>
        <button onClick={this.props.handleRandom}>random</button>
      </div>
    );
  }
}

// 스토어 안에 있는 값을 props로 전달
const mapStateToProps = state => {
  return {
    number: state.counter.number
  };
};

// 액션 생성 함수를 props로 전달
// 모든 액션은 dispatch를 통해 store에 전달된다는 것을 잊지 말자
const mapDispatchProps = dispatch => {
  return {
    handleIncrement: () => {
      dispatch(increment());
    },
    handledecrement: () => {
      dispatch(decrement());
    },
    handleRandom: () => {
      dispatch(random_number());
    }
  };
};

// connect 함수 사용해서 컴포넌트에 리덕스 스토어 연동
export default connect(mapStateToProps, mapDispatchProps)(Counter);
```

# Reference

- [Redux 공식 문서(한글)](https://lunit.gitbook.io/redux-in-korean/)
- [Velopert님 블로그](https://velopert.com/3346)
