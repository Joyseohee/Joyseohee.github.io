---
layout: distill
title: "리액트에서 만나기 쉬운 에러"
date: 2023-02-02 16:40:16
description: "리액트에서 만나기 쉬운 에러"
tags: react javascript
categories: framework library
giscus_comments: true
authors:
  - name: Seohee Park
    # url: "https://en.wikipedia.org/wiki/Albert_Einstein"
    # affiliations:
    #   name: IAS, Princeton

toc:
    - name: "리액트 기본 원리"
    - subsections:
        - name: "페이지가 바뀌더라도 자동으로 리렌더링하지 않음"
    - name: "문제가 생기기 쉬운 부분"
    - subsections:
        - name: "Uncought in promise 에러"
        - name: "State를 써야하는지 Props를 써야하는지 헷갈린다면?"
        - name: "Props로 받아올 수 있는 값은 State로 쓰지 않습니다!"
        - name: "State 업데이트는 비동기적일 수도 있습니다."
        - name: "함수 바인딩"
---


## 리액트 기본 원리

### 페이지가 바뀌더라도 자동으로 리렌더링하지 않음

- `props.history.push()`로 페이지를 이동할 경우 컴포넌트를 자동으로 리렌더링하지 않습니다!
- 리렌더링되지 않는다는 건 `componentDidMount가` 작동하지 않는 다는 의미!
- 페이지를 새로고침하면 `componentDidMount가` 작동합니다. `componentDidMount로만` 렌더링 되지는 않는지 반드시 확인합니다.
    - 만약 `componentDidMount`로만 렌더링이 발생한다면 `state`가 바뀌더라도 화면에 변화가 없을 수도 있습니다.
    - `componentDidUpdate`로 `state`에 변화가 생길 때 리렌더링하도록 처리합니다.
    - `componentDidUpdate` 안에는 반드시 조건을 달아 무한 렌더링 되지 않도록 제한합니다.
    - `componentDidUpdate`(`preState`, `preProps`) 함수의 파라미터는 렌더링 전의 `props`와 `state`를 기억합니다. `this.state`나 `this.props`와 달라지면 재렌더링하도록 조건을 답니다.
- 컴포넌트 내부의 `state`나 컴포넌트 상위에서 전달되는 `props`가 변해야 렌더링합니다. 이때 값이 변하고 `componentDidUpdate`하는 시점에 유의해야합니다.

<aside>
📌 이 글은 <b>React 16.8.2</b>와 <b>react-dom-router 5.2.0</b> 버전 기준으로 작성됐습니다.<br/>
리액트는 <b>렌더링</b>과 <b>비동기 처리</b> 때문에 개발에 어려움을 겪기 쉽습니다.<br/>
리액트로 개발하다보면 마주치기 쉬운 에러들을 정리해보았습니다.<br/>
</aside>

## 문제가 생기기 쉬운 부분
### Uncought in promise 에러

`async-await` 혹은 `then`으로 `return`한 데이터는 본래 string이든, integer든, 배열이든, Object든 무관하게 `Promise` 객체로 반환됩니다. `Promise`객체는 `await` 혹은 `().then(res ⇒ {})` 안에서만 사용할 수 있습니다.

그렇다고 `async-awat`나 `then`에서 무작정 꺼낸다면 비동기적인 자바스크립트 특성상 원하는 시점에 함수가 호출되지 않습니다. 렌더링이 되고 나서 `state`가 바뀌는 경우가 생기는데요. 이때 `componentDidUpdate`로 업데이트 하지 않으면 하위 컴포넌트에서 `props`를 받더라도 리렌더링되지 않는 경우가 생깁니다. 
이 문제를 해결하기 위해서는 함수들이 순서대로 실행되도록 수를 써둬야 합니다.

- `async - await` 쓰거나
- `().then().then()`를 쓰는데
    - `().then().then(().then().then())`은 먹히지 않으니 따로 분리해서 씁니다.
    - *단!* `().then().then().then()…`로 뒤에 붙이는 건 가능합니다. `then`안에서 `return`으로 결과 도출해서 다음 `then`에서 쓰면 좋습니다.


### State를 써야하는지 Props를 써야하는지 헷갈린다면?

[React로 사고하기 - React](https://ko.reactjs.org/docs/thinking-in-react.html#step-3-identify-the-minimal-but-complete-representation-of-ui-state)

1. 부모로부터 props를 통해 전달됩니까? 그러면 확실히 state가 아닙니다.
2. 시간이 지나도 변하지 않나요? 그러면 확실히 state가 아닙니다.
3. 컴포넌트 안의 다른 state나 props를 가지고 계산 가능한가요? 그렇다면 state가 아닙니다.

### Props로 받아올 수 있는 값은 State로 쓰지 않습니다!

다른 state나 props로 계산할 수 있는 데이터는 변수로 씁니다.

### State 업데이트는 비동기적일 수도 있습니다.

[State and Lifecycle - React](https://ko.reactjs.org/docs/state-and-lifecycle.html#state-updates-may-be-asynchronous)

React는 성능을 위해 여러 `setState()` 호출을 단일 업데이트로 한꺼번에 처리할 수 있습니다.

`this.props`와 `this.state`가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안 됩니다.

예를 들어, 다음 코드는 카운터 업데이트에 실패할 수 있습니다.

```jsx
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

이를 수정하기 위해 객체보다는 함수를 인자로 사용하는 다른 형태의 `setState()`를 사용합니다. 

그 함수는 이전 state를 첫 번째 인자로 받아들일 것이고, 업데이트가 적용된 시점의 props를 두 번째 인자로 받아들일 것입니다.

```jsx
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

### 함수 바인딩
- `onClick={this.handleClick}`으로 사용한다면 `constructor`에 `this.handleClick = this.handleClick.bind(this)`와 같이 별도로 바인딩 해야 합니다.
- `onClick={() => this.handleClick()}`와 같이 사용한다면 `constructor`에 별도의 바인딩이 필요하지 않습니다.