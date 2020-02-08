# Event Loop

> https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif?fbclid=IwAR3cjXGvUY91u4_8aXIOUzZ-Qm7tCPGVV104FkfNd9Bi4Gm8S93-QDsE0vQ

자바사크립트는 싱글스레드다: 한 task만 한번에 실행할수있다. 그렇게 큰 문제는 아니지만, 30초가 걸리는 작업을 실행한다고 생각해봐... 이 task를 실행시키는 동안 30초를 기다리는겨. (JavaScript는 default로 브라우저의 메인스레드에서 실행되므로 전체 UI가 중지돼) 지금은 2019년이니까 아무도 느리거나 응답을 안하는 웹사이트는 원하지않것지~~?

Luckily, 브라우저는 (자바스크립트 엔진이 제공하지않는) 몇가지 특징이 있어: Web API. 이건 DOM API, `setTimeout`, HTTP requests 등을 포함해. 이건 우리가 async, non-blocking 할수있게 도와주지!!

우리가 함수를 호출했을때, 그건 **call stack**이라고 불리는 무언가에다 추가해. 그 call stack은 JS engine의 일부분인데, 브라우저마다 다르진않아. 걍 stack이고 first in, last out 이 라는거지. 함수가 값을 return 했을때, stack에서 튀어나오는거야.

```js
function respond() {
  return setTimeout(() => {
    return "Hey!";
  }, 1000);
}
```

저 `respond` 함수는 `setTimeout`을 리턴해. `setTimeout`은 Web API로 우리한테 제공되는디(?): 우리가 main thread를 blocking 하지않고 task를 delaty할수있게 해주는거야. `setTimeout` 함수에 전달한 callback함수, arrow function `() => {return 'Hey'}`는 **Web API**에 추가돼. 그 동안 `setTimeout`함수와 `respond`함수는 stack에서 튀어나오고, 둘다 그 값들을 리턴해!!

Web API에 있는 timer는 우리가 argument로 넘겼던 1000ms 동안 실행돼. 그 callback은 바로 call stack에 추가되진 않고, 대신에 **queue**에 전달돼

자 이제 헷갈리는 부분이야 :persevere:. 이건 1000ms후에 callback함수가 call stack에 추가된다는 말이 아니야! 그냥 1000ms 후에 **queue**에 추가되는거야. 그치만 이건 큐이기 때문에 그 함수는 차례를 기다려야하지~

이제 우리가 기다리고 기다리던 부분이야. event loop가 일을 할시간~ queue를 call stack에다가 연결하는거야! 만약 _call stack이 비어있다면_, 그래서 모든 함수들이 이미 값을 리턴하고나서 stack에서 튀어나갔다면, queue에 있던 첫번째 아이가 call stack에 추가돼. 이건 callback 함수가 queue에서 첫번째 아이이면서 call stack은 비어있고 어떤 함수도 호출이 안됐을때를 말하는거야.

이 callback은 call stack에 추가되고, 호출되고, 값을 리턴하고, stack에서 빠져나가.

--- 

이걸 그냥 읽는것도 재미있겠지만, 실제로 해보면 완전 맘이 편해질거야. 
자, 아래 코드를 실행해보고 콘솔에 찍히는걸 봐.

```
const foo = () => console.log("First");
const bar = () => setTimeout(() => console.log("Second"), 500);
const baz = () => console.log("Third");

bar();
foo();
baz();
```

알겠지? 아주 퀵하게 브라우저에서 무슨 일이 일어나는지 볼수있어~

1. `bar`를 호출해. `bar`는 `setTimeout` 함수를 리턴해. 
2. `setTimeout`에 전달된 callback은 Web API에 추가되고, `setTimeout`함수와 `bar`는 callstack에서 튀어나와.  
3. timer가 돌고, 그사이 `foo`가 호출되어서 `First`가 찍혀. `foo`는 undefined를 리턴하고, `baz`는 호출되고, 아까 그 callback은 queue에 추가돼.
4. `baz`는 `Third`를 찍어. Event loop는 `baz`가 리턴되고나서 call stack이 비어있는지 보고, 그 후 callback을 call stack에다가 저장해. 
5. callback은 `Second`를 찍어.

---

event loop에 대해서 좀 편해졌니? 좀 헷갈려도 걱정하지마. 가장 중요한건 어떤 에러/행동들이 **어디서** 나타나는지 이해해서 구글에서 적당~한 단어로 검색해서 Stack overvlow에서 제대로 찾기만하면돼ㅎㅎ