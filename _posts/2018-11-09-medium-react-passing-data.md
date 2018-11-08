---
layout: post
title: "[번역] React 컴포넌트 간의 데이터 전달 방법"
tags: [react, medium, translation]
---

> 해당 포스트는 medium 포스트의 [Passing Data Between React Components](https://medium.com/@ruthmpardee/passing-data-between-react-components-103ad82ebd17)를 번역한 문서입니다.

React의 데이터 흐름은 단방향이라, 컴포넌트들 간의 데이터 전달이 조금 까다로울 때가 있습니다. 데이터는 가끔 자식 컴포넌트에서 부모 컴포넌트나, 부모 컴포넌트에서 자식 컴포넌트로 아니면 자식 컴포넌트에서 다른 자식 컴포넌트(Siblings)로 전달되어야 할 필요가 있습니다.

이건 제 애플리케이션을 구성하는 리액트 컴포넌트들의 구조입니다.

```
App
 |
 |__ InputBar
 |
 |__ ToDoList
       |
       |__ToDoItem
```

# 부모에서 자식으로 - Prop을 사용하세요
이건 React에서 데이터를 전달하는 가장 쉬운 방향입니다. 부모 컴포넌트에서 접근할 수 있는 데이터를 자식 컴포넌트도 접근해야 하는 경우, 부모 안에서 인스턴스화 시킬 때 이 데이터를 prop이란 형태로 자식 데이터에게 전달할 수 있습니다.

App 에서 ToDoList 컴포넌트에 데이터를 전달했을 경우의 예시입니다.
```
class App extends React.Component {
    render() {
    [... 여기 어딘가에 ToDoList 컴포넌트에 필요한 데이터인 변수 listName을 정의했습니다...]
        
        return (
            <div>
                 <InputBar/>
                 <ToDoList listNameFromParent={listName}/>
            </div>
        );
    }
}
```

이제 ToDoList 컴포넌트에서 `this.props.listNameFromParent`를 사용하면 해당 데이터에 접근할 수 있습니다.

# 자식에서 부모로 - 콜백과 states를 사용하세요
이건 조금 까다롭습니다. 자식 컴포넌트가 데이터를 가지고 있고, 그걸 부모 컴포넌트가 접근해야 한다면 다음과 같은 방식을 시도할 수 있습니다.

1. 부모 컴포넌트에 콜백을 정의합니다. 이 콜백은 필요한 데이터를 매개 변수로 가져옵니다.
2. 그 콜백을 자식 컴포넌트에게 props로 전달합니다.
3. 자식 컴포넌트에서 `this.props.[callback]`를 사용해 콜백을 불러옵니다. ([callback]에는 직접 만든 콜백 이름을 넣어야겠죠.) 그리고 파라메터로 데이터를 전달합니다.

만약 ToDoItem 컴포넌트가 가지고 있는 데이터를 ToDoList에서 접근해야 하는 경우 이러한 모습이 되겠죠.

ToDoList 컴포넌트.
```
class ToDoList extends React.Component {
    myCallback = (dataFromChild) => {
        [...여기서 dataFromChild를 사용합니다....]
    },
    render() {
        return (
            <div>
                 <ToDoItem callbackFromParent={this.myCallback}/>
            </div>
        );
    }
}
```

이제 ToDoItem에서 callbackFromParent로 데이터를 전달할 수 있습니다.
```
class ToDoItem extends React.Component{
    someFn = () => {
        [... 여기 어딘가에 ToDoList 컴포넌트에 필요한 데이터인 변수 listName을 정의했습니다...]
        this.props.callbackFromParent(listInfo);
    },
    render() {
        [...]
    }
};
```

ToDoList는 이제 myCallback 함수 내에서 listInfo를 사용할 수 있습니다.

그런데 myCallback 함수 뿐만 아니라 ToDoList 내의 다른 함수에서 listInfo를 접근하고 싶으면 어떻게 될까요? 이 구현 방법으로는 오직 하나의 특정한 메소드에 전달된 매개 변수로만 접근할 수 있습니다.

쉬운 방법: ToDoList 내에서 이 파라메터를 state로 정의하세요. 당신은 ToDoList의 스코프 내에서 변수를 생성하여 해당 컴포넌트 내의 모든 메소드에 접근할 수 있다고 생각할 수 있습니다. 제 경우에는, ToDoList를 다음과 같이 정의하고 있습니다.

```
class ToDoList extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            listDataFromChild: null
        };    
    },
    myCallback = (dataFromChild) => {
        this.setState({ listDataFromChild: dataFromChild });
    },
    otherFn = () => {
        [...이 함수 내에서는 여전히 this.state.listDataFromChild에 접근할 수 있습니다....]
    }
    render() {
        return (
            <div>
                 <ToDoItem callbackFromParent={this.myCallback}/>
                 [...여기서 this.state.listDataFromChild를 다른 자식 컴포넌트에 prop 로 전달할 수 있습니다....]  
     
            </div>
        );
    }
});
```

myCallback을 정의할 때 화살표 함수를 사용했습니다. 화살표 함수를 사용하면, 호출할 때 호출된 곳의 컨텍스트를 유지하기 때문에 .bind를 사용하지 않아도 됩니다.

# 자식에서 자식으로 (Siblings) - Combine the above
당연히, 자식들 사이에서 데이터를 전달하게 될 경우에는 부모 컴포넌트를 중간 다리로 활용해야 합니다. 먼저 부모 컴포넌트의 콜백 인수로서, 자식 컴포넌트에서 부모 컴포넌트로 데이터를 전달합니다. 이 파라메터를 부모 컴포넌트의 state로 정의한 다음, 다른 자식 컴포넌트에게 prop으로 전달합니다. 그럼 그 자식 컴포넌트는 그 데이터를 prop으로 사용할 수 있습니다.

React 컴포넌트 간의 데이터 전달 방법은 처음에 엄청 복잡해보일지 모르나(Redux를 사용하지 않을 경우), 만약 이 세 가지 테크닉을 연습한다면 원하는 구성 요소들간의 데이터 전달을 할 수 있게 될 것입니다.