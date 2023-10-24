---
layout: single
title: '[JS] 실행 컨텍스트(Execution Context)'
author_profile: true
date: 2023-09-01 00:00:00 +0900
categories: JavaScript
published: true
toc: true
toc_label: 'Table of Contents'
toc_icon: 'list-ul'
toc_sticky: true
sidebar:
  nav: 'docs'
# sidebar:
#   - title: 'Title'
#     image: http://placehold.it/350x250
#     image_alt: 'image'
#     text: 'Some text here.'
#   - title: 'Another Title'
#     text: 'More text here.'
---

<br>

실행 컨텍스트(Execution Context, EC)는 **실행할 코드에 제공할 환경 정보들을 모아놓은 객체**로, JS의 동적 언어로서의 성격을 잘 드러내는 개념입니다. 이름에서 유추할 수 있는 것처럼 **코드가 실행되기 위해 필요한 환경**이라고 생각할 수도 있습니다. 실행 컨텍스트에 대해 잘 이해한다면 scope, hoisting, this, closure 같은 JS의 필수적인 개념들을 이해하는데 도움이 됩니다.

<br>

---

<br>

#  **1. 실행 컨텍스트(Execution Context)** 

JS 코드를 실행하기 위해서 자바스크립트 엔진은 **코드에 대한 정보들(변수, arguments 객체, 함수 선언, scope, this 등)**을 알고 있어야 합니다. JS 코드가 실행되면서 실행 컨텍스트가 생성되는데, 자바스크립트 엔진은 코드에 대한 정보들을 실행 컨텍스트 객체 안에 저장합니다. 새로운 실행 컨텍스트는 다음과 같은 경우에 생성됩니다.

- **전역 공간**: JS 코드가 처음으로 실행될 때
- eval() 함수가 실행될 때 (eval() 함수는 보안상 취약점이 있고, 인터프리터를 사용하여 느리기 때문에 사용하지 않는 것이 좋습니다. \[[eval() MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/eval#eval%EC%9D%84%20%EC%A0%88%EB%8C%80%20%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80%20%EB%A7%90%20%EA%B2%83!)\])
- **함수가 실행될 때**

<br>

---

<br>

#  **2. 실행 컨텍스트 스택(Execution Context Stack)** 

실행 컨텍스트는 자바스크립트 엔진에 의해 생성된 실행 컨텍스트 스택에서 생성되고 소멸합니다. 스택은 LIFO(Last In First Out, 후입선출) 구조의 자료 구조입니다.

실행 컨텍스트는 다음과 같은 순서로 형성됩니다.

1.  JS 코드로 control flow가 이동하면, 빈 실행 컨텍스트 스택이 생성됩니다.
2.  브라우저에서 JS 코드를 실행하면 전역 실행 컨텍스트(Global Execution Context, Global EC)가 생성됩니다.
3.  그 후 코드가 실행되면서 함수가 호출될 때마다 새로운 함수 실행 컨텍스트(Function Execution Context)가 스택에 쌓이고(push), control flow가 전역 실행 컨텍스트에서 함수 실행 컨텍스트로 이동합니다. (Control flow를 갖고 있는 컨텍스트를 running(active) execution context라고 부릅니다.)
4.  함수가 실행되고, 새로운 컨텍스트 안에서 또 다른 함수가 호출되면 또다시 새로운 실행 컨텍스트를 생성합니다. (control flow 이동)
5.  함수의 실행이 종료되면 실행 컨텍스트도 스택에서 제거(pop)됩니다. 스택에서 컨텍스트가 제거될 떄, control flow도 다시 이전의 컨텍스트로 이동합니다.

> ※ 참고) 어떤 컨텍스트가 다른 컨텍스트를 생성하는 역할을 하는 경우 "_caller_"라고 부르고, 이때 생성된 컨텍스트를 "_callee_"라고 부릅니다.

이렇게 스택 구조로 실행 컨텍스트를 활용하여 같은 컨텍스트에 있는 코드들이 같은 환경을 공유하도록 할 수 있고, 전체 코드의 순서를 제어할 수 있습니다.

예시)

```javascript
// ---------------------------------- (1) 전역 컨텍스트 생성
let name = 'hangeoreum';

function foo() {
  function bar() {
    console.log(name);
  }
  bar(); // -----------------------  (3) bar 컨텍스트 생성
}

foo(); // -------------------------- (2) foo 컨텍스트 생성
```

위와 같은 코드를 실행했을 때, 실행 컨텍스트 스택은 아래와 같이 변하게 됩니다.

![실행 컨텍스트 스택](https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/aae01ee9-31b4-4de9-baa1-c8ae4d9296c4)

<br>

---

<br>

#  **3. 실행 컨텍스트의 구성** 

실행 컨텍스트가 생성될 때, 자바스크립트 엔진은 해당 실행 컨텍스크와 관련된 코드를 실행하는데 필요한 환경 정보들을 수집해서 실행 컨텍스트 객체에 저장합니다. 실행 컨텍스트 개체는 2가지 프로퍼티를 소유합니다.

- **Variable Environment**
- **Lexical Environment**

ES5 전에는 ES3의 개념인 변수 객체(Variable Object), 활성 객체(Activation Object), 스코프 체인(Scope Chain)을 사용했었습니다. 이 개념들이 ES5에서 Lexical Enivironment로 변경되었습니다.

※ 참고) 실행 컨텍스트 객체는 자바스크립트 엔진이 활용할 목적으로 생성한 객체입니다. 개발자가 코드를 통해서 실행 컨텍스트에 접근할 수는 없습니다.

Variable Environment가 Lexical Environment를 상속하기 때문에 실행 컨텍스트가 생성될 때 Variable Environment와 Lexical Environment에 들어가는 내용은 동일합니다.

<br>

## **3 - 1. Variable Environment와 Lexical Environment의 구성**

**Environment Record**

해당 Lexical Environment/Variable Environment의 scope 안에서 생성된 변수, 매개변수, 함수(함수 표현식은 제외) 등의 식별자(Identifier) 정보들을 저장합니다(함수 컨텍스트인 경우 arguments 객체 포함). 컨텍스트가 생성될 때 자바스크립트 엔진은 해당 컨텍스트와 관련된 코드를 처음부터 끝까지 순서대로 읽으며 식별자 정보를 수집합니다. 전역 실행 컨텍스트의 경우 전역 객체(Global Object: window or global) 안에 위 정보들을 저장합니다.

Environment Record 정보를 수집하는 것을 통해서, 자바스크립트 엔진은 해당 컨텍스트의 코드가 실행되기 전에도 코드의 식별자들을 모두 다 알 수 있게 됩니다. 마치 자바스크립트 엔진이 식별자 정보를 컨텍스트 최상단으로 끌어올린 후에 코드를 실행하는 것처럼 보입니다(물론, 실제로 그렇지는 않습니다). 여기서 Hoisting이라는 개념이 등장합니다. Hoisting에 대해서는 잠시 후에 다뤄보도록 하겠습니다.

Environment Record는 ThisBingding 값을 포함합니다. ThisBinding에는 this가 참조하는 객체가 저장되고, 실행 컨텍스트가 생성될 때 this를 따로 지정하지 않는다면 ThisBinding은 전역 객체(strict mode에서는 undefined)를 참조하게 됩니다. 실행 컨텍스트는 함수를 호출할 때 생기고 실행 컨텍스트가 생길 때 this 값이 결정되기 때문에, this 값은 함수를 호출할 때 결정됩니다. 즉, 함수 호출 방식에 따라 this 값이 달라지게 됩니다.

전역 실행 컨텍스트의 경우 Environment Record에는 전역 객체의 property들이 포함됩니다.

<br>

**Outer Environment Reference**

Lexical scope(or static scope)를 기준으로 상위 scope의 Lexical Environment를 참조합니다. 즉, 호출되면서 현재 컨텍스트를 생성한 함수가 선언될 당시 컨텍스트의 LexicalEnvironment를 참조하는 것입니다. 실행 컨텍스트 스택에서 현재 컨텍스트의 바로 아래 컨텍스트의 Lexical Environment를 참조한다고도 할 수 있습니다.

함수 선언 안에 2개의 함수 선언이 있다면, 중첩된 두 함수의 Lexical Environment는 Outer Environment Reference로 그 둘을 포함하는 함수의 Lexical Environment를 참조합니다.

전역 실행 컨텍스트의 경우 Outer Environment Reference의 값은 null입니다.

예시)

```javascript
let name = 'hangeoreum';

function foo() {
  let age = 710;
  function bar() {
    let a = 10;
  }
  function baz() {
    let b = 20;
  }
}

foo();
```

위 코드에서 foo 함수 안에 bar 함수와 baz 함수를 선언했습니다. 이 경우, 각 컨텍스트의 Outer Environment Reference는 다음과 같습니다.

- bar, baz 함수 실행 컨텍스트: foo 함수 실행 컨텍스트의 Lexical Environment를 참조
- foo 함수 실행 컨텍스트: 전역 실행 컨텍스트의 Lexical Environment를 참조

![Outer Environment Reference](https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/54d65a24-9f60-441f-9bca-b3dc51b61cc3)

위 그림처럼 각각의 outerEnvironmentReference는 단방향 연결 리스트(Linked List) 형태를 갖습니다.

outerEnvironmentReference를 사용하면 중첩된 자바스크립트 코드에서 **Lexical nesting structure**(ES3의 scope chain)을 통해 식별자를 검색할 수 있습니다.

<br>

## **3 - 2.VariableEnvironment와 LexicalEnvironment의 차이점**

**VariableEnvironment**

- var로 선언된 변수가 메모리에 매핑되고 초기값으로 undefined가 할당됩니다. var로 선언한 변수를 위한 Function scope를 단위로 합니다.

**LexicalEnvironment**

- let, const로 선언된 변수가 메모리에 매핑되고 초기값은 할당되지 않습니다. 함수 선언이 메모리에 매핑되고 함수 전체가 할당됩니다. let과 const로 선언한 변수를 위한 Block scope를 단위로 합니다.
- [ECMA-262 6th](https://262.ecma-international.org/6.0/#sec-lexical-environments)에서는 "Usually a Lexical Environment is associated with some specific syntactic structure of ECMAScript code such as a *FunctionDeclaration*, a *BlockStatement*, or a *Catch* clause of a *TryStatement* and a new Lexical Environment is created each time such code is evaluated." 라고 하고 있습니다. 즉, 함수 선언, { } 블록문, try/catch문 같은 코드가 실행될 때마다 새로운 LexicalEnivronment가 생기게 됩니다.

<br>

---

<br>

#  **4. 실행 컨텍스트 생성 과정**

실행 컨텍스트는 creation과 execution 단계를 통해 생성됩니다.

- **Creation Phase**: 이 단계에서는 ThisBinding 값과 outerEnvironmentReference 값을 결정하고, LexicalEnvironment와 VariableEnvironment의 environmentRecord에 식별자 정보 저장 및 초기화가 이루어집니다.
- **Execution Phase**: 자바스크립트 엔진이 코드를 실행시키고, 식별자에 변수를 할당합니다.

<br>

---

<br>

#  **5. Hoisting** 

Hoisting이란 변수나 함수의 선언문 이전에 변수에 접근하고 함수를 호출할 수 있는 현상을 말합니다. 이는 마치 변수와 함수를 해당 컨텍스트의 최상단으로 끌어올린 것 같은 효과를 내게 됩니다.

Hoisting에서 '끌어올린다'는 말은 실제로 그런 것이 아니라, 실행 컨텍스트의 creation phase에서 변수가 메모리에 매핑되는 과정을 말합니다. var로 선언한 변수는 creation phase에서 식별자가 메모리에 매핑되고 undefined로 초기화됩니다. let이나 const로 선언한 변수는 creation phase에서 식별자가 메모리에 매핑되지 않아 초기값이 선언되지 않아서(uninitialized) 접근하려고 하면 ReferenceError가 발생합니다.

Creation phase에서 함수 선언은 함수 전체로, var로 선언된 변수는 undefined로 초기화되기 때문에, 코드에서 선언문 이전에 함수 호출과 변수에 접근이 가능한 것입니다.

앞에서 environmentRecord에 함수 선언만 수집한다고 했었습니다. 함수 표현식은 수집하지 않습니다. 이는 함수 표현식은 함수를 다른 변수에 값으로 할당하는 것이기 때문에 함수 전체에 대한 정보가 저장되지 않고 변수에 대한 정보만 environmentRecord에 저장되기 때문입니다. 그렇기 때문에 함수 표현식은 함수 선언과 달리 hoisting 되지 않습니다.

<br>

# References

[https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)

[https://iamsjy17.github.io/javascript/2019/06/10/js33_execution_context.html](https://iamsjy17.github.io/javascript/2019/06/10/js33_execution_context.html)

[https://dkje.github.io/2020/08/30/ExecutionContext/](https://dkje.github.io/2020/08/30/ExecutionContext/)

[https://262.ecma-international.org/6.0/#sec-lexical-environments](https://262.ecma-international.org/6.0/#sec-lexical-environments)

[https://medium.com/su-s-daily-log/javascript-execution-context-hoisting-and-closures-6d64cbcb6bc8](https://medium.com/su-s-daily-log/javascript-execution-context-hoisting-and-closures-6d64cbcb6bc8)

[https://joontae-kim.github.io/2020/10/18/execution-context-example/](https://joontae-kim.github.io/2020/10/18/execution-context-example/)

[https://poiemaweb.com/js-execution-context](https://poiemaweb.com/js-execution-context)
