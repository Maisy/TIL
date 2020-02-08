# Hoisting

> https://dev.to/lydiahallie/javascript-visualized-hoisting-478h

Hoisting은 JS 개발자라면 한번쯤 들어봤을거야. 왜냐면 에러때문에 구글링해서 Stack Overflow에 들어갔을거고, hoisting 때문에 에러가났다고 누가 말해줬을거니깐..ㅋㅋ 그래서 hoisting이 뭐냐고? (참고로, scope는 다른 글에서 쓸거야. 길이 글어지면 안되니깐~)

만약에 니가 JavaScript 뉴비라면, 아마 변수들이 랜덤하게 `undefined`, `ReferenceError`가 뜨는걸 보고 이상하다고 생각했을거야. 그래서 Hoisting을 _파일 최상단에서 변수랑 함수를 할당하는것_ 이라고 설명하기도 해. 하지만 그런일은 일어나지않아:stuck_out_tongue_winking_eye: 비록 그렇게 보일진몰라도~

JS engine이 우리 스크립트를 받았을 때, 먼저 하는일은 우리 코드안에 있는 데이터를 위해서 **메모리를 셋업**하는거야. 이 시점에는 어떤 코드도 실행되지 안혹, 단지 실행을 위한 모든것을 준비하지. 함수가 서언되는거랑 변수가 저장되는 방법은 조금달라. 함수는 **함수 전체를 참조로** 저장하지. 


변수는 약간달라. ES6는 변수를 선언하는 방법이 두개가 있어. `let`이랑 `const`. `let`또는 `const`로 선언된 변수들은 _uninitialized_로 저장돼.

`var` 키워드로 선언된 변수들은 undefined라는 default value로 저장되지.

생성하는 단계가 끝났으니, 코드를 실행해볼 차례야. 
함수나 다른 변수들이 선언되기 전에, 파일 젤 위에 3개의 console.log를 호출하면 무슨일이 일어나는지 보자. 

```js
console.log(sum(2,3))
console.log(city)
console.log(name)

function sum(x, y){
  return x * y;
}

const name = "Maisy"

let info = {
  age: 23,
  nationality: "Dutch"
}

var city = "San Francisco"
```

함수들은 함수 전체코드를 참조로 저장하고 있기 때문에, 우리는 함수를 선언하기 전에 호출할수가 있어! 🔥

`var` 로 호출된 변수를 선언하기전에 호출하면, default value인 `undefined`를 호출해! 그런데 이건 가끔씩 '예상치못한..' 행동을 하기도해. 대부분 이건 의도치않게 뭔가를 참조하는거니깐. (아마도 직접 정의하지않은 값을 갖고있는걸 원하지않을거야)

이렇게 우연히 `undefined`를 참조하는걸 막기위해서 _uninitialized_ 변수에 접근하려고 할때는 `ReferenceError`가 던져져. 실제로 선언되기 전의 "zone"은 **temporal dead zone** 이라고 불려. 변수가 초기화되기 전에 변수를 호출할수 없지!(ES6의 class도 마찬가지야)

그리고 나서 engine이 실제로 선언한 변수를 실행할 때, 메모리에 있는 값이 우리가 선언한걸로 덮어써지는거지. 

끝!🎉 빠르게 복습해보자
- 함수와 변수는 코드를 실행하기 전에 execution context의 메모리에 저장돼. 이게 hoisting 이라고 불리는거야. 
- 함수는 함수전체가 참조로 저장되고, `var` 키워드로 선언된 변수들은 `undefined`를, `let`과 `const` 키워드로 선언된 변수들은 _uninitialized_ 로 저장돼. 

이제 우리가 코드를 실행시켰을때 무슨일이 일어나는지 살펴봤으니깐 hoisting 이라는 단어가 좀 덜 모호해졌으면 좋겠어. 항상 그렇듯 아직 이해가 많이 안되더라도 너무 걱정하진마~ 점점 더 할수록 편안해질거야 ㅎㅎ