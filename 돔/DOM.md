# DOM

[HTML 파싱과 DOM 생성](https://github.com/siggu/modern-javascript-study/blob/main/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%9D%98%20%EB%A0%8C%EB%8D%94%EB%A7%81%20%EA%B3%BC%EC%A0%95/browser-rendring.md#html-%ED%8C%8C%EC%8B%B1%EA%B3%BC-dom-%EC%83%9D%EC%84%B1)에서 살펴본 바와 같이 브라우저의 렌더링 엔진은 HTML 문서를 파싱하여 브라우저가 이해할 수 있는 자료구조인 DOM을 생성한다. **DOM(Document Object Model)은 HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조다.** DOM에 대해 자세히 알아보자.

## 노드

### HTML 요소와 노드 객체

HTML 요소는 HTML 문서를 구성하는 개별적인 요소를 의미한다.

![HTML 요소의 구조](/돔/images/HTML%20요소의%20구조.png)

> HTML 요소의 구조

HTML 요소는 렌더링 엔진에 의해 파싱되어 DOM을 구성하는 요소 노드 객체로 변환된다. 이때 HTML 요소의 어트리뷰트는 어트리뷰트 노드로, HTML 요소의 텍스트 콘텐츠는 텍스트 노드로 변환된다.

![HTML 요소와 노드 객체](/돔/images/HTML%20요소와%20노드%20객체.png)

> HTML 요소와 노드 객체

HTML 문서를 HTML 요소들의 집합으로 이뤄지며, HTML 요소는 중첩 관계를 갖는다. 즉, HTML 요소의 콘텐츠 영역(시작 태그와 종료 태그 사이)에는 텍스트뿐만 아니라 다른 HTML 요소도 포함될 수 있다.

이때 HTML 요소 간에는 중첩 관계에 의해 계층적인 부자 관계가 형성된다. 이러한 HTML 요소 간의 부자 관계를 반영하여 HTML 문서의 구성 요소인 HTML 요소를 객체화한 모든 노드 객체들을 트리 자료 구조로 구성한다.

**트리 자료구조**

트리 자료구조는 노드들의 계층 구조로 이뤄진다. 즉, 트리 자료구조는 부모 노드와 자식 노드로 구성되어 있는 노드 간의 계층적 구조(부자, 형제 관계)를 표현하는 비선형 자료구조를 말한다. 트리 자료구조는 하나의 최상위 노드에서 시작한다. 최상위 노드는 부모 노드가 없으며, 루트 노드라 한다. 루트 노드는 0개 이상의 자식 노드를 갖는다. 자식 노드가 없는 노드를 리프 노드라 한다.

![트리 자료구조](/돔/images/트리%20자료구조.png)

> 트리 자료구조

**노드 객체들로 구성된 트리 자료구조를 DOM(Document Object Model)이라 한다.** 노드 객체의 트리로 구조화되어 있기 때문에 DOM을 **DOM 트리**라고 부르기도 한다.

### 노드 객체의 타입

예를 들어, 다음 HTML 문서를 렌더링 엔진이 파싱한다고 생각해보자.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <ul>
      <li id="apple">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
    <script src="app.js"></script>
  </body>
</html>
```

렌더링 엔진은 위 HTML 문서를 파싱하여 다음과 같이 DOM을 생성한다.

![DOM](/돔/images/DOM.png)

> DOM

이처럼 DOM은 노드 객체의 계층적인 구조로 구성된다. 노드 객체는 종류가 있고 상속 구조를 갖는다. 노드 객체는 총 12개의 종류(노드 타입)가 있다. 이 중에서 중요한 노드 타입은 다음과 같이 4가지다.

**문서 노드**

문서 노드는 DOM트리의 최상위에 존재하는 루트 노드로서 `document` 객체를 가리킨다. `document` 객체는 브라우저가 렌더링한 HTML 문서 전체를 가리키는 객체로서 전역 객체 `window`의 `document` 프로퍼티에 바인딩되어 있다. 따라서 문서 노드는 `window.document` 또는 `document`로 참조할 수 있다.

브라우저 환경의 모든 자바스크립트 코드는 `script` 태그에 이해 분리되어 있어도 하나의 전역 객체 `window`를 공유한다. 따라서 모든 자바스크립트 코드는 전역 객체 `window`의 `document` 프로퍼티에 바인딩되어 있는 하나의 `document` 객체를 바라본다. 즉, HTML 문서당 `document` 객체는 유일하다.

문서 노드, 즉 `document` 객체는 DOM 트리의 루트 노드이므로 DOM 트리의 노드들에 접근하기 위한 진입점 역할을 한다. 즉, 요소, 어트리뷰트, 텍스트 노드에 접근하려면 문서 노드를 통해야 한다.

**요소 노드**

요소 노드는 HTML 요소를 가리키는 객체다. 요소 노드는 HTML 요소 간의 중첩에 의해 부자 관계를 가지며, 이 부자 관계를 통해 정보를 구조화한다. 따라서 요소 노드는 문서의 구조를 표현한다고 할 수 있다.

**어트리뷰트 노드**

어트리뷰트 노드는 HTML 요소의 어트리뷰트를 가리키는 객체다. 어트리뷰트 노드는 어트리뷰트가 지정된 HTML 요소의 요소 노드와 연결되어 있다. 단, 요소 노드는 부모 노드와 연결되어 있지만 어트리뷰트 노드는 부모 노드와 연결되어 있지 않고 요소 노드에만 연결되어 있다. 즉, 어트리뷰트 노드는 부모 노드가 없으므로 요소 노드의 형제 노드는 아니다. 따라서 어트리뷰트 노드에 접근하여 어트리뷰트를 참조하거나 변경하려면 먼저 요소 노드에 접근해야 한다.

**텍스트 노드**

텍스트 노드는 HTML 요소의 텍스트를 가리키는 객체다. 요소 노드가 문서의 구조를 표현한다면 텍스트 노드는 문서의 정보를 표현한다고 할 수 있다. 텍스트 노드는 요소 노드의 자식 노드이며, 자식 노드를 가질 수 없는 리프 노드다. 즉, 텍스트 노드는 DOM 트리의 최종단이다. 따라서 텍스트 노드에 접근하려면 먼저 부모 노드인 요소 노드에 접근해야 한다.

### 노드 객체의 상속 구조

DOM은 HTML 문서의 계층적 구조와 정보를 표현하며, 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조라고 했다. 즉, DOM을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 DOM API를 사용할 수 있다. 이를 통해 노드 객체는 자신의 부모, 형제, 자식을 탐색할 수 있으며, 자신의 어트리뷰트와 텍스트를 조작할 수도 있다.

DOM을 구성하는 노드 객체는 ECMAScript 사양에 정의된 표준 빌트인 객체가 아니라 브라우저 환경에서 추가적으로 제공하는 호스트 객체다. 하지만 노드 객체도 자바스크립트 객체이므로 프로토타입에 의한 상속 구조를 갖는다. 노드 객체의 상속 구조는 다음과 같다.

![노드 객체의 상속 구조](/돔/images/노드%20객체의%20상속%20구조.png)

> 노드 객체의 상속 구조

위 그림과 같이 모든 노드 객체는 `Object`, `EventTarget`, `Node` 인터페이스를 상속받는다. 추가적으로 문서 노드는 `Document`, `HTMLDocument`, 인터페이스를 상속받고 어트리뷰트 노드는 `Attr`, 텍스트 노드는 `CharacterData` 인터페이스를 각각 상속받는다.

요소 노드는 `Element` 인터페이스를 상속받는다. 또한 요소 노드는 추가적으로 `HTMLElement`와 태그의 종류별로 세분화된 `HTMLHtmlElement`, `HTMLHeadElement`, `HTMLBodyElement`, `HTMLUListElement` 등의 인터페이스를 상속받는다.

이를 프로토타입 체인 관점에서 살펴보자. 예를 들어, `input` 요소를 파싱하여 객체화한 `input` 요소 노드 객체는 `HTMLInputElement`, `HTMLElement`, `Element`, `Node`, `EventTarget`, `Object`의 `prototype`에 바인딩되어 있는 프로토타입 객체를 상속받는다. 즉, `input` 요소 노드 객체는 프로토타입 체인 안에 있는 모든 프로토타입의 프로퍼티나 메서드를 상속받아 사용할 수 있다.

![input 요소 노드 객체의 프로토타입 체인](/돔/images/input%20요소%20노드%20객체의%20프로토타입%20체인.png)

> input 요소 노드 객체의 프로토타입 체인

```html
<!DOCTYPE html>
<html>
  <body>
    <input type="text" />
    <script>
      // input 요소 노드 객체를 선택
      const $input = document.querySelector('input');

      // input 요소 노드 객체의 프로토타입 체인
      console.log(
        Object.getPrototypeOf($input) === HTMLInputElement.prototype,
        Object.getPrototypeOf(HTMLInputElement.prototype) === HTMLElement.prototype,
        Object.getPrototypeOf($HTMLElement.prototype) === Element.prototype,
        Object.getPrototypeOf($Element.prototype) === Node.prototype,
        Object.getPrototypeOf($Node.prototype) === EventTarget.prototype,
        Object.getPrototypeOf($EventTarget.prototype) === Object.prototype
      ); // 모두 true
    </script>
  </body>
</html>
```

배열이 객체인 동시에 배열인 것처럼 `input` 요소 노드 객체도 다양한 특성을 갖는 객체이며, 이러한 특성을 나타내는 기능들을 상속을 통해 제공받는다.

노드 객체에는 노드 객체의 종류, 즉 노드 타입에 상관없이 모든 노드 객체가 공통적으로 갖는 기능도 있고, 노드 타입에 따라 고유한 기능도 있다. 예를 들어, 모든 노드 객체는 공통적으로 이벤트를 발생시킬 수 있다. 이벤트에 관련된 기능(`EventTarget.addEventListener`, `EventTarget.removeEventListener` 등)은 `EventTarget` 인터페이스가 제공한다. 또한 모든 노드 객체는 트리 자료구조의 노드로서 공통적으로 트리 탐색 기능(`Node.parentNode`, `Node.childNodes`, `Node.previousSibling`, `Node.nextSibling` 등)이나 노드 정보 제공 기능(`Node.nodeType`, `Node.nodeName` 등)이 필요하다. 이 같은 노드 관련 기능은 `Node` 인터페이스가 제공한다.

HTML 요소가 객체화된 요소 노드 객체는 HTML 요소가 갖는 공통적인 기능이 있다. 예를 들어 `input` 요소 노드 객체와 `div` 요소 노드 객체는 모두 HTML 요소의 스타일을 나타내는 `style` 프로퍼티가 있다. 이처럼 HTML 요소가 갖는 공통적인 기능은 `HTMLElement` 인터페이스가 제공한다.

하지만 요소 노드 객체는 HTML 요소의 종류에 따라 고유한 기능도 있따. 예를 들어 `input` 요소 노드 객체는 `value` 프로퍼티가 필요하지만 `div` 요소 노드 객체는 `value` 프로퍼티가 필요하지 않다. 따라서 필요한 기능을 제공하는 인터페이스(`HTMLInputElement`, `HTMLDivElement` 등)가 HTML 요소의 종류에 따라 각각 다르다.

이처럼 노드 객체는 공통된 기능일수록 프로토타입 체인의 상위에, 개별적인 고유 기능일수록 프로토타입 체인의 하위에 프로토타입 체인을 구축하여 노드 객체에 필요한 기능, 즉 프로퍼티와 메서드를 제공하는 상속 구조를 갖는다.

지금까지 살펴본 바와 같이 **DOM은 HTML 문서의 계층적 구조와 정보를 표현하는 것은 물론 노드 객체의 종류, 즉 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API(Application Programming Interface)로 제공한다. 이 DOM API를 통해 HTML의 구조나 내용 또는 스타일 등을 동적으로 조작할 수 있다.**

DOM API를 사용하기 위해 지금까지 살펴본 노드 객체의 상속 구조를 자세히 알아야 할 필요는 없다. 상속 구조를 모른다 하더라도 노드 객체는 상속을 통해 마치 자신의 프로퍼티와 메서드처럼 DOM API를 사용할 수 있다.

중요한 것은 DOM API, 즉 DOM이 제공하는 프로퍼티와 메서드를 사용하여 노드에 접근하고 HTML의 구조나 내용 또는 스타일 등을 동적으로 변경하는 방법을 익히는 것이다 프론트엔드 개발자에게 HTML은 단순히 태그와 어트리뷰트를 선언적으로 배치하여 뷰를 구성하는 것 이상의 의미를 가진다. 즉, HTML을 DOM과 연관 지어 바라보아야 한다.
