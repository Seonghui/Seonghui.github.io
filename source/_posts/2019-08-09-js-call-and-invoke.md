---
title: "[Javascript] Function - Call과 Invoke의 차이"
tags: [javascript]
categories: 자바스크립트
---

자바스크립트 관련 자료를 원문으로 보다 보면 함수 호출을 **call a function** 대신 **invoke a function** 이라고 하는 경우가 있다. **call**이나 **invoke** 둘 다 함수 호출을 뜻하지만, 둘은 미묘한 차이가 있다.

보통 함수 호출은 **call**이라는 단어를 사용하는데 **invoke**를 사용하는 이유는, 자바스크립트는 직접적으로 호출이 될 수도 있지만 `call()` 와 `apply()` 메소드를 사용해 간접적으로 호출이 될 수도 있기 때문이다.

```js
fn();
fn.call();
fn.apply();
```

셋 다 함수를 호출하기는 하는데, `fn()`처럼 직접적으로 호출할 때는 **call**이 어울리고, `fn.call()`나 `fn.apply()`로 호출할 때는 **invoke**가 어울린다. (그래봤자 미묘한 어감의 차이일 뿐이지만...)

아래의 그림을 보자.

![Call and Invoke](https://fg4tjq.bn.files.1drv.com/y4mD2kJhpzVhh--1SgEKpH2z_Yr-HNvyEFBqLBlroGVH8_IwFsXzpR7EqKTYgq_TKhTOA_6GxcNdEp24AZjyXK6DI8agA1iFBOv2y3ye7SN8pj4w1PRFOBOvpm4peHDW-9nlgZgY7zzaqyxtZ4t2GkQE7Rc8GcMMN78oRff7sORjNP2E3yySbM-fNQgMbnVz5mdfRHofdYo6jC7LwcBRgS3zA?width=1563&height=723&cropmode=none)

- **call**: 실제로 함수를 호출하는 명령문을 참조
- **invoke**: 함수를 호출/실행하기 위해 호출문을 간접적으로 참조

## reference

- [Calling vs invoking a function](https://stackoverflow.com/questions/50884893/calling-vs-invoking-a-function)
