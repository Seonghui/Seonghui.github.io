---
layout: post
title: "React 작업환경 설정하기"
tags: [react, setting]
---
> 맨날 까먹어서 정리하는 React 작업환경 설정 방법 - React, Sass

# React 설치하기
## create-react-app 설치하기 전에 설치해야 하는 것들
* Node.js 최신 버전
* yarn

만약 yarn을 설치하지 않을 경우 npm을 사용하면 된다.

## 프로젝트 설치하기
* 레알 처음 설치하는 경우

yarn 으로 create-react-app 글로벌 설치하기

```
yarn global add create-react-app
```

* create-react-app 설치하기

터미널에서 설치하고 싶은 폴더까지 이동 후 다음 명령어를 통해 프로젝트 생성. test-app 대신 원하는 디렉토리 이름을 적자.

```
create-react-app test-app
```

## 프로젝트 시작하기
test-app 디렉토리에 들어가서 다음 명령어를 실행하자. 

```
yarn start
```

<http://localhost:3000/> 에 자동으로 접속되는데, 만약 자동으로 접속되지 않을 경우 브라우저에 저 주소를 입력하면 된다.

# Sass
## Sass 설치하기
프로젝트 폴더에 node-sass와 sass-loader 설치하기.

```
yarn add node-sass sass-loader
```

### webpack 설치하기
**config/webpack.config.dev.js** 에서 기존 css 설정 복붙한 다음 이렇게 고쳐주기.
배포를 할 때는 **webpack.config.prod.js**도 같이 고쳐주어야 한다.

```js
{
  test: /\.css$/,
  use: [
    ...
  ],
},
{
  test: /\.scss$/,
  use: [
    ...
  ],
},
```

복붙한 다음 내부 use 값 설정하기.

```js
 {
  test: /\.scss$/,
  use: [
    require.resolve('style-loader'),
    {
      loader: require.resolve('css-loader'),
      options: {
        importLoaders: 1,
      },
    },
    {
      loader: require.resolve('postcss-loader'),
      options: {
        // Necessary for external CSS imports to work
        // https://github.com/facebookincubator/create-react-app/issues/2677
        ident: 'postcss',
        plugins: () => [
          require('postcss-flexbugs-fixes'),
          autoprefixer({
            browsers: [
              '>1%',
              'last 4 versions',
              'Firefox ESR',
              'not ie < 9', // React doesn't support IE8 anyway
            ],
            flexbox: 'no-2009',
          }),
        ],
      },
    },
    // sass-loader 추가.
    {
      loader: require.resolve('sass-loader'),
      options: {
        // 경로 간소화(includePaths)
        includePaths: [paths.styles]
      }
    }
  ],
},
```

경로 간소화를 위해 config/paths.js 파일 수정하기.

```js
// config after eject: we're in ./config/
module.exports = {
  (...),
  styles: resolveApp('src/styles')
};
```

전부 적용하면 아래와 같이 사용 가능하다.
```scss
// includePaths 적용 전
@import '../../../styles/utils';

// includePaths 적용 후
@import 'utils';
```