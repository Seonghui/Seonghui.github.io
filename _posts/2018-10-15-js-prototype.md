---
layout: post
title: "[Javascript] Prototype 정리"
tags: [javascript]
---
> 프로토타입 링크와 프로토타입 오브젝트

# 프로토타입이란?
자바스크립트는 프로토타입 기반 언어이다. 자바에 클래스가 있다면 자바스크립트에는 프로토타입이 있다. ECMAScript6에 클래스 문법이 생겼지만 그렇다고 자바스크립트가 클래스 기반 언어로 바뀐 건 아니다. 어쨌든 자바스크립트는 이 프로토타입으로 클래스 기반 언어의 상속이라는 것을 비슷하게 흉내낼 수 있다. 지금 당장 클래스니 프로토타입 기반 언어니라는 것은 몰라도 된다. 그냥 그렇구나 하고 넘어가자. 프로토타입을 제대로 이해하고 이러한 개념을 다시 읽으면 저절로 고개를 끄덕끄덕하게 된다.

단, 상속이라는 개념은 기억하고 넘어가자. 상속이란 단어 그대의 "상속", 즉 부모로부터 뭔가를 계승받는다(?) 정도로의 개념으로 이해하고 있으면 된다. 다시 말해 **자바스크립트는 프로토타입을 사용해 뭔가를 계승받을 수 있다.**

# Prototype Link와 Prototype Object
프로토타입은 두 가지로 나뉘어진다. 바로 **프로토타입 링크**와 **프로토타입 오브젝트**이다. 프로토타입이라는 개념은 정말 이해하기 어렵다. 일단 링크와 오브젝트 두 개로 나뉘어져 있고, 개념을 이해한다 하더라도 서로 헷갈리기 때문이다. 둘은 프로토타입이라는 이름을 공유하지만 전혀 비슷하지 않다. 자바스크립트와 자바 같은 느낌이랄까... 계속 읽다보면 프로슈탈트 붕괴를 일으키기 쉽다. 아무튼 차근차근 프로토타입 링크부터 알아보자.

# Prototype Link
자바스크립트의 모든 객체는 Object의 자식이라고들 한다. 일단 이 말만 기억하고... 예제부터 보자.

```javascript
function add(a,b) {
  return a + b;
}
console.log(add.valueOf); //ƒ valueOf() { [native code] }
```

add 함수의 `valueOf()` 메소드를 출력해도 undefined가 출력되지 않는다. 분명 나는 `valueOf()` 라는 것을 만든 적이 없는데, 왜 무언가가 출력이 되는 걸까?

너무 궁금하니까 `console.dir()` 메소드로 add 함수를 출력해보자. 참고로 `console.dir()`는 객체 정보를 트리형태로 출력해주는 기능이다. `console.log()`보다 더 자세하게 객체 정보를 확인할 수 있다.

![example]({{site.url}}/assets/images/js-prototype/prototype-link.png){: width="400" height="auto"}

arguments, caller, length, name은 넘어가겠다. 그냥 add 함수의 기본 프로퍼티 정도로만 알고 있으면 된다.

여기서 눈여겨 봐야할 부분은 바로 **__proto__: f()** 이다. 이 부분을 열어보면 함수의 기본 프로퍼티가 쭉 나오고, 그 아래에 또 **__proto__**라는 것이 또 나온다. 여기서는 눈을 씻고 찾아봐도 `valueOf()`라는 메소드가 나오지 않는다. 그렇다면 **__proto__: Object** 를 열어보자.

![example]({{site.url}}/assets/images/js-prototype/prototype-link2.png){: width="400" height="auto"}

띠용. **__proto__: Object**를 열어보니 비로소 `valueOf()`가 보인다. 아마 이쯤에서 **__proto__**라는 것이 무언가를 이어주는 역할을 한다는 것을 알아챌 것이다. **__proto__**는 무언가를 이어주는 링크인데, 무엇을 이어주는 걸까?

여기서 **__proto__**는 **프로토타입 링크**라고 한다. 프로토타입 링크는 모든 객체가 빠짐없이 가지고 있는 속성으로, 자신을 만들어낸 원형인 프로토타입 객체를 참조하는 링크이다. 여기서 add 의 원형은 Function이고, Function의 원형은 바로 Object, 즉 객체이다. (자바스크립트는 함수도 객체이다)

다음 그림을 보자.

![example]({{site.url}}/assets/images/js-prototype/prototype-link3.png){: width="200" height="auto"}

함수 A의 **프로토타입 링크(__proto__)**를 따라가면 **__proto__: f()**이 나오고, 그 Function의 링크를 따라가면 **__proto__: Object**가 나온다.

아까 모든 객체가 Object의 자식이라고 했었다.  예를 들어, 함수의 경우 **함수->Function->Object**로 이어진다면 배열의 경우에는 **배열-> Array ->Object**로 이어지게 되는데, 이렇게 모든 객체의 끝은 Object로 귀결이 된다.

즉, 프로토타입 링크를 쭉 타고 상위로 올라가다보면 최상위에는 Object가 존재해서 이런 말이 나온 것이다. 이런 식으로 상위 프로토타입과 연결이 되어있는 형태를 프로토타입 체인이라고 부른다.

이렇게 연결이 되어있는 상태에서는 원형인 프로토타입의 메소드들을 사용할 수 있다. **Object에는 valueOf()라는 메소드가 존재하고, 함수가 valueOf()라는 메소드를 탐색했지만 존재하지를 않으니 프로토타입 링크를 타고 올라가 Object 의 메소드를 탐색해서 결과를 return하니까 undefined를 출력하지 않는 것이다.** 만약 상위 프로토타입을 탐색했는데도 메소드를 찾지 못하면 undefined를 return하게 된다. 아까 나왔었던 상속을 떠올려보자. 프로토타입을 사용해서 뭔가를 계승한다고 했는데, 즉 상위 프로토타입의 메소드들을 계승한다는 뜻이다.

![example]({{site.url}}/assets/images/js-prototype/prototype-link4.png){: width="500" height="auto"}
따라서 각각의 프로토타입 링크를 출력해보면 이러한 결과가 나온다.

# Prototype Object

```javascript
function Person() { }

`Person.prototype`.eyes = 2;
`Person.prototype`.nose = 1;

var jake = new Person();
var mike = new Person();

console.log(jake.eyes); //2
console.log(mike.nose); //1
```

일단 Person 이라는 생성자 함수를 만들고, `Person.prototype`에 eyes와 nose를 프로퍼티를 추가했다. 지금 당장 `Person.prototype`은 몰라도 된다. 그냥 그렇구나 하고 넘어가자. 그리고 jake와 mike라는 객체를 만들고, console.log 로 eyes와 nose라는 값을 찍어보면 정상적으로 2와 1이 출력되는 것을 알 수 있다.

Person 생성자 함수는 비었는데, 어떻게 eyes와 nose 프로퍼티를 제대로 출력할 수 있을까? 여기서 아마 jake와 mike 객체가 같은 prototype 공간을 공유하고 있어서
이러한 결과가 나왔다고 대애애애애충 짐작할 수 있을 것이다. 다시 말해 같은 Person 생성자 함수에서 만들어진 jake와 mike는 `Person.prototype`을 서로 공유하고 있는 것이다.

여기까지 이해했다면, 프로토타입 오브젝트(Prototype Object)라는 개념을 대략적으로 이해한 것이다. 다음 그림을 보자.

![example]({{site.url}}/assets/images/js-prototype/prototype-object.png){: width="700" height="auto"}
일단 A 부분부터 보자. 함수를 정의하면 함수만 생성되는 것이 아니라, 그 함수의 이름을 딴 프로토타입도 같이 생겨나게 된다. 즉 Person() 생성자 함수를 정의하니 `Person.prototype`까지 자동으로 생성이 된 것이다. 이 `Person.prototype`을 바로 프로토타입 오브젝트라고 부른다. 생성된 함수는 prototype 속성을 통해 자동으로 생성된 프로토타입 오브젝트에 접근이 가능하다.

프로토타입 오브젝트에는 `constructor`라는 프로퍼티와 **__proto__** 링크가 존재한다. 또한 아까 예시에서 추가했던 eyes와 nose또한 보인다.

* **constructor**: 프로토타입 오브젝트가 생성되었을 때 같이 생성되었던 함수를 가리키고 있다. 따라서 `Person.prototype`은 Person() 생성자 함수를 가리키고 있는 것이다.
* **__proto__**: `Person.prototype`은 프로토타입 오브젝트이다. 따라서 프로토타입 링크는 Object를 가리키게 된다.
* **eyes, nose**: 예시를 보면, `Person.prototype` 오브젝트에 eyes 와 nose를 동적으로 생성했기 때문에 `Person.prototype`에 eyes:2와 nose:1이라는 프로퍼티가 추가로 생겼다는 것을 알 수 있다.

다음으로는 B 부분이다. 코드상으로는 new 키워드를 통해 mike와 jake 객체를 생성해냈다. 이 객체들은 당연하게도 각각 프로토타입 링크가 존재한다. 물론 객체기때문에 가리키는 상위 프로토타입이 Object가 된다.

마지막으로 C부분이다. mike 객체와 jake객체의 프로토타입 링크는 object 를 가리킨다고 했다. 과연 어떤 오브젝트를 가리키는 걸까. 바로... **Person의 프로토타입 오브젝트를 가리킨다.**

따라서 Person() 생성자 함수가 비었지만, Person() 생성자 함수로 만든 객체들은 `Person.prototype`, 즉 같은 프로토타입 오브젝트를 공유하고 있기 때문에 위의 코드와 같은 결과가 나오게 된 것이다. 이제 콘솔로 직접 찍어가면서 확인해보자.

![example]({{site.url}}/assets/images/js-prototype/prototype-object2.png){: width="600" height="auto"}

(C) jake의 프로토타입 링크를 확인해보면 Object인 걸 확인할 수 있다. 여기서 Object는 Person() 생성자 함수의 프로토타입 오브젝트이다.

![example]({{site.url}}/assets/images/js-prototype/prototype-object3.png){: width="400" height="auto"}
저 프로토타입 링크를 열어보니까 `Person.prototype`의 프로퍼티들이 나온다. 여기서 `constructor`는 생성자 함수인 Person() 를 가리키고, 프로토타입 링크는 Object를 가리키고 있다는 것을 확인할 수 있다.

![example]({{site.url}}/assets/images/js-prototype/prototype-object4.png){: width="500" height="auto"}

다음은 생성자 함수 Person()을 열어본 것이다. prototype 프로퍼티를 보니 어디서 많이 보던 게 있다.. 바로 직전에 보았던 `Person.prototype` 오브젝트이다. (구성이 완전 똑같다)

(A)이런식으로 Person() 생성자 함수의 prototype 프로퍼티는 `Person.prototype`을 가리키고 `Person.prototype`의 `constructor`가 Person() 생성자 함수를 가리키고 있다는 것까지 확인했다. 참고로 저렇게 계속 Person() 생성자 함수의 내부와 `Person.prototype` 내부를 열어봤자 끝이 나지 않는다. 서로가 서로를 가리키고 있기 때문에... 저런 형태로 무한반복이 된다.

질문이나 피드백은 환영합니다. 댓글로 달아 주세요.

# Reference
- 인사이드 자바스크립트 (송형주, 고현준 저)
- [Medium 포스트](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67)
- [insanehong 님 블로그](http://insanehong.kr/post/javascript-prototype/)
- [Nextree 포스트](http://www.nextree.co.kr/p7323/)