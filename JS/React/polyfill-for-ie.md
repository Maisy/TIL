
# IE(11) polyfill

### src/index.js

- 또는 제일 먼저 호출되는 컴포넌트를 구현한 파일 최상단에 아래 코드를 추가한다.

1. create-react-app으로 생성한 react앱의 경우 아래 polyfill을 일단 추가해야함

- 테스트는 안해봤는데 import 'react-app-polyfill/ie11'는 안해도 된다고 해서 import 'react-app-polyfill/ie9만 했는데 11에서도 잘됐다.

```
npm i react-app-polyfill
```

```js
import 'react-app-polyfill/ie9'; // For IE 9-11 support

import 'react-app-polyfill/stable';
```

2. immer 모듈을 쓰는 경우 `enableES5()` 추가 (이것땜에 몇시간 삽질함 ㅜㅜ)

- hint: IE dev console에 아래와 같이 에러가 나는 경우

```
"Error: [Immer] minified error nr: 19 ES5. Find the full error at: https://bit.ly/3cXEKWf
```

```js
import { enableES5 } from 'immer';

enableES5();
```

### public/index.html

ie default 버전이 7로 가는데, 기본값을 ie11를 설정하는 메타테그를 추가

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" charset="UTF-8" />
```

--- 
### 굳이 안해도됐던 삽질

redux-saga를 쓰면서 generator(+ await async) 로직도 들어가서 babel polyfill 추가해봄.

- .babelrc 등도 설정해봤는데 삽질이 어따. immer때문에 잘 안도는거였음 ㅜㅜ

```
npm i @babel/polyfill
```

```js
import 'core-js/stable';
import 'regenerator-runtime/runtime';
```
