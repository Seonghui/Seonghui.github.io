---
title: "[React] index.html에는 index.js를 불러오는 코드가 없는데, index.js는 어떻게 index.html 파일 내에 있는 root div를 참조하는 것일까?"
tags: [react]
categories: React
---

create-react-app으로 리액트 프로젝트를 생성해 보자. create-react-app은 리액트의 보일러 플레이트 코드를 생성하기 때문에 직접 개발 환경을 구성하지 않더라도 별도의 설정 없이 프로젝트를 바로 시작할 수 있다.

package.json 내의 script 명령을 보면 이런 게 있다.

    "start": "react-scripts start",

이 코드는 node_modules./bin 폴더에 있는 react-scripts.js 파일을 실행시킨다.

react-scripts는 create-react-app의 스크립트를 모아놓은 곳이다. react-scripts start는 개발 환경을 셋업하고, 핫 리로딩이 적용된 서버를 실행시킨다.

react-scripts.js 파일을 열어보면 대략 다음과 같이 되어있다.

    ...생략
    if (['build', 'eject', 'start', 'test'].includes(script)) {
      const result = spawn.sync(
        'node',
        nodeArgs
          .concat(require.resolve('../scripts/' + script))
          .concat(args.slice(scriptIndex + 1)),
        { stdio: 'inherit' }
      );
      ...생략
      process.exit(result.status);
    }
    ...생략

build, eject, start, test 키워드가 있을 경우, `../scripts/` 폴더 내의 파일을 찾게 된다.

![example](/images/react-index/index-1.png)

react-scripts start 명령어의 경우, 키워드는 start니까 `../scripts/` 폴더 내에 있는 start.js 파일을 열어보자.

    ...생략
    const paths = require('../config/paths');

    // Warn and crash if required files are missing
    if (!checkRequiredFiles([paths.appHtml, paths.appIndexJs])) {
      process.exit(1);
    }
    ...생략

`checkRequiredFiles()` 로 배열 내의 파일 경로가 유효한 경로인지 확인한다. `checkRequiredFiles()` 는 react-dev-utill 내에 있다. 만약 파일이 없거나 경로가 유효하지 확인하고, 전부 존재한다면 if문 아래에 있는 개발 서버 실행 코드를 실행시킨다.

`../config/paths` 내에 있는 paths.appHtml, paths.appIndexJs을 확인해보자.

paths.js 파일은 eject 이전의 경로, eject 이후의 경로 그리고 publish 이전의 경로를 나눠서 관리한다.

    const moduleFileExtensions = [
      'web.mjs',
      'mjs',
      'web.js',
      'js',
      'web.ts',
      'ts',
      'web.tsx',
      'tsx',
      'json',
      'web.jsx',
      'jsx',
    ];

    const resolveModule = (resolveFn, filePath) => {
      const extension = moduleFileExtensions.find(extension =>
        fs.existsSync(resolveFn(`${filePath}.${extension}`))
      );

      if (extension) {
        return resolveFn(`${filePath}.${extension}`);
      }

      return resolveFn(`${filePath}.js`);
    };

    const resolveOwn = relativePath => path.resolve(__dirname, '..', relativePath);

    module.exports = {
        appHtml: resolveOwn('template/public/index.html'),
        appIndexJs: resolveModule(resolveOwn, 'template/src/index'),
    };

`resolveModule(resolveOwn, 'template/src/index')` 인 이유는 appIndexJs가 자바스크립트 파일이 아닐 수도 있기 때문이다. (ts, jsx 등등...)

resolveOwn()을 호출하면 현재 모듈의 디렉토리 이름을 포함한 경로를 반환한다.

resolveModule()에 resolveOwn()함수와 `template/src/index` 를 넘기면 현재 모듈의 디렉토리 + 파일 경로 + 확장자를 합쳐 경로를 만들고 resolveOwn()를 호출하게 된다.

결론적으로는 현재 프로젝트에 있는 index.js와 index.html가 존재하는지 파악해, 경로에 파일이 존재하는 경우 개발 서버를 실행시키는 거라 할 수 있겠다.

# References

- [https://stackoverflow.com/questions/41738421/how-react-js-index-js-file-contacting-index-html-for-id-references](https://stackoverflow.com/questions/41738421/how-react-js-index-js-file-contacting-index-html-for-id-references)
- [https://stackoverflow.com/questions/50722133/what-exactly-is-the-react-scripts-start-command](https://stackoverflow.com/questions/50722133/what-exactly-is-the-react-scripts-start-command)
