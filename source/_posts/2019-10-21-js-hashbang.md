---
title: "[Javascript] 해시뱅 (HashBang)"
tags: [javascript]
---

## 해시뱅?

얼마 전 신기한 URL을 봤다.

```text
www.test.com/#/
www.test.com/#/settings
www.test.com/#/register
```

이런 식으로 URL에 #이 붙는 것이다. 처음에는 뭔지 몰라 그냥 지나갔는데 오늘 이것의 정체가 해시뱅이라는 것을 알게 되었다. 샵 다음에 느낌표(#!)를 붙이기도 하나 보다. 이름부터가 해시뱅(#!)인 걸 보면... 아무튼 해시뱅은 [이 블로그](https://blog.outsider.ne.kr/698)에 엄청나게 자세히 설명이 되어 있다.

블로그 글을 요약하자면, 해시뱅을 사용하는 이유는 **단일 페이지 애플리케이션(SPA)** 구현을 위해서다. 해시뱅을 사용하면 페이지 갱신 없이 URL을 변경할 수 있다. 정확히는 변경되는 것처럼 보이는 거지만. 해시뱅에서 # 다음 부분을 `fragment identifier`라고 하는데, 위 예제에서는 settings, register이 `fragment identifier`가 된다. 해시뱅의 원리는 html의 네임 앵커를 이용한 것이다.

꽤 오래 전에 사용했던 기술이고 지금은 deprecated 되었다. (하지만 내가 사용하게 되는데...)

## React에 해시뱅 적용하기

적용 방법은 단순하다. [HashRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/docs/api/HashRouter.md)를 사용하면 된다.

`BrowserRouter` 에서

```text
import React from 'react';
import { BrowserRouter } from 'react-router-dom';
import App from './components/App';


const Routes = () => (
  <BrowserRouter>
    <App />
  </BrowserRouter>
);

export default Routes;
```

`HashRouter`로 바꿔주었다.

```text
import React from 'react';
import { HashRouter } from 'react-router-dom';
import App from './components/App';


const Routes = () => (
  <HashRouter>
    <App />
  </HashRouter>
);

export default Routes;
```

HashRouter를 굳이 사용해야 할 이유를 하나 꼽자면 레거시 브라우저(IE9 이하) 지원 정도...? 그 이외에는 사용해야 할 이유를 잘 모르겠다.

## References

* [해시뱅(#!)에 대해서...](https://blog.outsider.ne.kr/698)
* [What's the shebang/hashbang (#!) in Facebook and new Twitter URLs for?](https://stackoverflow.com/questions/3009380/whats-the-shebang-hashbang-in-facebook-and-new-twitter-urls-for)
