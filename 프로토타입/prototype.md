# 프로토타입

자바스크립트는 명령형, 함수형, 프로토타입 기반 객체지향 프로그래밍을 지원하는 멀티 패러다임 프로그래밍 언어다.

자바스크립트는 객체 기반의 프로그래밍 언어이며 **자바스크립트를 이루고 있는 거의 "모든 것"이 객체**다. 원시 타입의 값을 제외한 나머지 값들(함수, 배열, 정규 표현식 등)은 모두 객체다. 먼저 객체지향 프로그래밍에 대해 간단히 살펴보자.

<br>

## 객체지향 프로그래밍

객체지향 프로그래밍은 프로그램을 명령어 또는 함수의 목록으로 보는 전통적인 명령형 프로그래밍의 절차지향적 관점에서 벗어나 여러 개의 독립적 단위, 즉 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 말한다.

객체지향 프로그래밍은 실세계의 실체를 인식하는 철학적 사고를 프로그래밍에 접목하려는 시도에서 시작한다. 실체는 특징이나 성질을 나타내는 **속성**을 가지고 있고, 이를 통해 실체를 인식하거나 구별할 수 있다.

다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 **추상화**라 한다.

"이름"과 "주소"라는 속성을 가진 `person`이라는 객체를 자바스크립트로 표현하면 다음과 같다.

```js
// 이름과 주소 속성을 갖는 객체
const person = {
  name: 'Lee',
  address: 'Seoul',
};

console.log(person); // {name: "Lee", address: "Seoul"}
```

이처럼 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 객체라 한다.

다음은 원이라는 개념을 객체로 만들어보자. 원에는 반지름이라는 속성이 있다. 이 반지름을 가지고 원의 지름, 둘레, 넓이를 구할 수 있다. 이때 반지름은 원의 **상태를 나타내는 데이터**이며 원의 지름, 둘레, 넓이를 구하는 것은 **동작**이다.

```js
const circle = {
  radius: 5, // 반지름

  // 원의 지름: 2r
  getDiameter() {
    return 2 * this.radius;
  },

  // 원의 둘레: 2πr
  getPerimeter() {
    return 2 * Math.PI * this.radius;
  },

  // 원의 넓이: πrr
  getArea() {
    return Math.PI * this.radius ** 2;
  },
};

console.log(circle);
// {radius: 5, getDiameter: f, getPerimeter: f, getArea: f}

console.log(circle.getDiameter()); // 10
console.log(circle.getPerimeter()); // 31.41592653589793
console.log(circle.getArea()); // 78.53981633974483
```

이처럼 객체지향 프로그래밍은 객체의 **상태**를 나타내는 데이터와 상태 데이터를 조작할 수 있는 **동작**을 하나의 논리적인 단위로 묶어 생각한다. 따라서 객체는 **상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조**라고 할 수 있따. 이때 객체의 상태 데이터를 프로퍼티, 동작을 메서드라 부른다.

<br>

## 상속과 프로토타입

상속은 객체지향 프로그래밍의 핵심 개념으로, 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.

자바스크립트는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다. 중복을 제거하는 방법은 기존의 코드를 적극적으로 재사용하는 것이다. 코드 재사용은 개발 비용을 현저히 줄일 수 있는 잠재력이 있으므로 매우 중요하다. 다음 예제를 살펴보자.

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    // Math.PI는 원주율을 나타내는 상수
    return Math.PI * this.radius ** 2;
  };
}

// 반지름이 1인 인스턴스 생성
const circle1 = new Circle(1);
// 반지름이 2인 인스턴스 생성
const circle2 = new Circle(2);

// Circle 생성자 함수는 인스턴스를 생성할 때마다 동일한 동작을 하는
// getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.
// getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); // false

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

`Circle` 생성자 함수는 인스턴스를 생성할 때마다 `getArea` 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.

![메서드 중복 생성](/프로토타입/images/메서드%20중복%20생성.png)

> 메서드 중복 생성

이처럼 동일한 생성자 함수에 의해 생성된 모든 인스턴스가 동일한 메서드를 중복 소유하는 것은 메모리를 불필요하게 낭비한다. 또한 인스턴스를 생성할 때마다 메서드를 생성하므로 퍼포먼스에도 악영향을 준다. 만약 10개의 인스턴스를 생성하면 내용이 동일한 메서드도 10개 생성된다.

상속을 통해 불필요한 중복을 제거해 보자. **자바스크립트는 프로토타입을 기반으로 상속을 구현한다.**

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를
// 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

![상속에 의한 메서드 공유](/프로토타입/images/상속에%20의한%20메서드%20공유.png)

> 상속에 의한 메서드 공유

<br>

## 프로토타입 객체

프로토타입 객체(또는 프로토타입)란 객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위해 사용된다. 프로토타입은 어떤 객체의 상위(부모) 객체의 역할을 하는 객체로서 다른 객체에 공유 프로퍼티(메서드 포함)를 제공한다 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용할 수 있다.

모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조다. `[[Prototype]]`에 저장되는 프로토타입은 **객체 생성 방식에 의해 결정**된다. 즉, 객체가 생성될 때 객체 생성 방식에 따라 프로토타입이 결정되고 `[[Prototype]]`에 저장된다.

모든 객체는 하나의 프로토타입을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 연결되어 있다. 즉, 객체와 프로토타입과 생성자 함수는 다음 그림과 같이 서로 연결되어 있다.

![객체와 프로토타입과 생성자 함수는 서로 연결되어 있다](/프로토타입/images/객체와%20프로토타입과%20생성자%20함수는%20서로%20연결되어%20있다.png)

> 객체와 프로토타입과 생성자 함수는 서로 연결되어 있다.

`[[Prototype]]` 내부 슬롯에는 직접 접근할 수 없지만, 위 그림처럼 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 자신의 `[[Prototype]]` 내부 슬롯이 가리키는 프로토타입에 간접적으로 접근할 수 있다. 그리고 프로토타입은 자신의 `constructor` 프로퍼티를 통해 생성자 함수에 접근할 수 있고, 생성자 함수는 자신의 `prototype` 프로퍼티를 통해 프로토타입에 접근할 수 있다.

### 함수 객체의 `prototype` 프로퍼티

**함수 객체만이 소유하는 `prototype` 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.**

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty('prototype'); // true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty('prototype'); // false
```

`prototype` 프로퍼티는 생성자 함수가 생성할 객체(인스턴스)의 프로토타입을 가리킨다. 따라서 생성자 함수로서 호출할 수 없는 함수, 즉 `non-constructor`인 화살표 함수와 ES6 메서드 축약 표현으로 정의한 메서드는 `prototype` 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.

```js
// 화살표 함수는 non-constructor이다.
const Person = (name) => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor이다.
const obj = {
  foo() {},
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```

생성자 함수로 호출하기 전에 정의하지 않은 일반 함수(함수 선언문, 함수 표현식)도 `prototype` 프로퍼티를 소유하지만 객체를 생성하지 않는 일반 함수의 `prototype` 프로퍼티는 아무런 의미가 없다.

**모든 객체가 가지고 있는(엄밀히 말하면 `Object.prototype`으로부터 상속받은) `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 `prototype` 프로퍼티는 결국 동일한 프로토타입을 가리킨다.** 하지만 이들 프로퍼티를 사용하는 주체가 다르다.

| 구분                        | 소유          | 값                | 사용 주체   | 사용 목적                                                                    |
| --------------------------- | ------------- | ----------------- | ----------- | ---------------------------------------------------------------------------- |
| `__proto__` 접근자 프로퍼티 | 모든 객체     | 프로토타입의 참조 | 모든 객체   | 객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용                      |
| `prototype` 프로퍼티        | `constructor` | 프로토타입의 참조 | 생성자 함수 | 생성자 함수가 자신이 생성할 객체(인스턴스)의 프로토타입을 할당하기 위해 사용 |

예를 들어, 생성자 함수로 객체를 생성한 후 `__proto__` 접근자 프로퍼티와 `prototype` 프로퍼티로 프로토타입 객체에 접근해보자.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 결국 Person.prototype과 me.__proto__는 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__); // true
```

![객체의 __proto__ 접근자 프로퍼티와
함수 객체의 prototype 프로퍼티는
결국 동일한 프로토타입을 가리킨다.](/프로토타입/images/객체의%20__proto__%20접근자%20프로퍼티와%20함수%20객체의%20prototype%20프로퍼티는%20결국%20동일한%20프로토타입을%20가리킨다.png)

### 프로토타입의 `constructor` 프로퍼티와 생성자 함수

모든 프로토타입은 `constructor` 프로퍼티를 갖는다. 이 `constructor` 프로퍼티는 `prototype` 프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킨다. 이 연결은 생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 이뤄진다. 다음 예제를 살펴보자.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// me 객체의 생성자 함수는 Person이다.
console.log(me.constructor === Person); // true
```

![프로토타입의 constructor 프로퍼티](/프로토타입/images/프로토타입의%20constructor%20프로퍼티.png)

> 프로토타입의 constructor 프로퍼티

<br>

## 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

| 리터럴 표기법      | 생성자 함수 | 프로토타입           |
| ------------------ | ----------- | -------------------- |
| 객체 리터럴        | `Object`    | `Object.prototype`   |
| 함수 리터럴        | `Function`  | `Function.prototype` |
| 배열 리터럴        | `Array`     | `Array.prototype`    |
| 정규 표현식 리터럴 | `RegExp`    | `RegExp.prototype`   |

<br>

## 프로토타입 생성 시점

리터럴 표기법에 의해 생성된 객체도 생성자 함수와 연결된다. 객체는 리터럴 표기법 또는 생성자 함수에 의해 생성되므로 결국 모든 객체는 생성자 함수와 연결되어 있다.

**프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.** 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.

생성자 함수는 사용자가 직접 정의한 사용자 정의 생성자 함수와 자바스크립트가 기본 제공하는 빌트인 생성자 함수로 구분할 수 있다. 사용자 정의 생성자 함수와 빌트인 생성자 함수를 구분하여 프로토타입 생성 시점에 대해 살펴보자.

### 사용자 정의 생성자 함수와 프로토타입 생성 시점

**생성자 함수로서 호출할 수 있는 함수, 즉 `constructor`는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.**

```js
// 함수 정의(constructor)가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
console.log(Person.prototype); // {constructor: f}

// 생성자 함수
function Person(name) {
  this.name = name;
}
```

생성자 함수로서 호출할 수 없는 함수, 즉 `non-constructor`는 프로토타입이 생성되지 않는다.

```js
// 화살표 함수는 non-constructor다.
const Person = (name) => {
  this.name = name;
};

// non-constructor는 프로토타입이 생성되지 않는다.
console.log(Person.prototype); // undefined
```

함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다. 따라서 함수 선언문으로 정의된 `Person` 생성자 함수는 어떤 코드보다 먼저 평가되어 함수 객체가 된다. 이때 프로토타입도 더불어 생성된다. 생성된 프로토타입은 `Person` 생성자 함수의 `prototype` 프로퍼티에 바인딩된다.

생성된 프로토타입은 오직 `constructor` 프로퍼티만을 갖는 객체다. 프로토타입도 객체이고 모든 객체는 프로토타입을 가지므로 프로토타입도 자신의 프로토타입을 갖는다. 생성된 프로토타입의 프로토타입은 `Object.prototype`이다.

![Person.prototype의 프로토타입](/프로토타입/images/Person.prototype의%20프로토타입.png)

> Person.prototype의 프로토타입

이처럼 빌트인 생성자 함수가 아닌 사용자 정의 생성자 함수는 자신이 평가되어 함수 객체로 생성되는 시점에 프로토타입도 더불어 생성되며, 생성된 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.

### 빌트인 생성자 함수와 프로토타입 생성 시점

`Object`, `String`, `Number`, `Function`, `Array`, `RegExp`, `Date`, `Promise` 등과 같은 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다. 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성된다. 생성된 프로토타입은 빌트인 생성자 함수의 `prototype` 프로퍼티에 바인딩된다.

![Object 생성자 함수와 프로토타입](/프로토타입/images/Object%20생성자%20함수와%20프로토타입.png)

이처럼 객체가 생성되기 이전에 생성자 함수와 프로토타입은 이미 객체화되어 존재한다. **이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 `[[Prototype]]` 내부 슬롯에 할당된다.** 이로써 생성된 객체는 프로토타입을 상속받는다.

<br>

## 객체 생성 방식과 프로토타입의 결정

객체는 다음과 같이 다양한 생성 방식이 있다.

- 객체 리터럴
- `Object` 생성자 함수
- 생성자 함수
- `Object.create` 메서드
- 클래스(ES6)

위처럼 다양한 방식으로 생성된 모든 객체는 추상 연산 `OrdinaryObjectCreate`에 의해 생성된다는 공통점이 있다.

추상 연산 `OrdinaryObjectCreate`는 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달받는다. 그리고 자신이 생성할 객체에 추가할 프로퍼티 목록을 옵션으로 전달할 수 있다. 추상 연산 `OrdinaryObjectCreate`는 빈 객체를 생성한 후, 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 프로퍼티를 객체에 추가한다. 그리고 인수로 전달받은 프로토타입을 자신이 생성한 객체의 [[Prototype]] 내부 슬롯에 할당한 다음, 생성한 객체를 반환한다.

즉, 프로토타입은 추상 연산 `OrdinaryObjectCreate`에 전달되는 인수에 의해 결정된다. 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

### 객체 리터럴에 의해 생성된 객체의 프로토타입

자바스크립트 엔진은 객체 리터럴을 평가하여 객체를 생성할 때 추상 연산 `OrdinaryObjectCreate`를 호출한다. 이때 추상 연산 `OrdinaryObjectCreate`에 전달되는 프로토타입은 `Object.prototype`이다. 즉, 객체 리터럴에 의해 생성되는 객체의 프로토타입은 `Object.prototype`이다. 다음 예제를 살펴보자.

```js
const obj = { x: 1 };
```

위 객체 리터럴이 평가되면 추상 연산 `OrdinaryObjectCreate`에 의해 다음과 같이 생성자 함수와 `Object.prototype`과 생성된 객체 사이에 연결이 만들어진다.

![객체 리터럴에 의해 생성된 객체의 프로토타입](/프로토타입/images/객체%20리터럴에%20의해%20생성된%20객체의%20프로토타입.png)

> 객체 리터럴에 의해 생성된 객체의 프로토타입

이처럼 객체 리터럴에 의해 생성된 `obj` 객체는 `Object.prototype`을 프로토타입으로 갖게 되며, 이로써 `Object.prototype`을 상속받는다.

```js
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x')); // true
```

### `Object` 생성자 함수에 의해 생성된 객체의 프로토타입

`Object` 생성자 함수를 인수 없이 호출하면 빈 객체가 생성된다. `Object` 생성자 함수를 호출하면 객체 리터럴과 마찬가지로 추상 연산 `OrdinaryObjectCreate`가 호출된다. 이때 추상 연산 `OrdinaryObjectCreate`에 전달되는 프로토타입은 `Object.prototype`이다. 즉, `Object` 생성자 함수에 의해 생성되는 객체의 프로토타입은 `Object.prototype`이다. 다음 예제를 살펴보자.

```js
const obj = new Object();
obj.x = 1;
```

위 코드가 실행되면 추상 연산 `OrdinaryObjectCreate`에 의해 다음과 같이 `Object` 생성자 함수와 `Object.prototype`과 생성된 객체 사이에 연결이 만들어진다 객체 리터럴에 의해 생성된 객체와 동일한 구조를 갖는 것을 알 수 있다.

![Object 생성자 함수에 의해 생성된 객체의 프로토타입](/프로토타입/images/Object%20생성자%20함수에%20의해%20생성된%20객체의%20프로토타입.png)

이처럼 `Object` 생성자 함수에 의해 생성된 `obj` 객체는 `Object.prototype`을 프로토타입으로 갖게 되며, 이로써 `Object.prototype`을 상속받는다.

```js
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x')); // true
```

객체 리터럴과 `Object` 생성자 함수에 의한 객체 생성 방식의 차이는 프로퍼티를 추가하는 방식에 있다. 객체 리터럴 방식은 객체 리터럴 내부에 프로퍼티를 추가하지만 `Object` 생성자 함수 방식은 일단 빈 객체를 생성한 이후 프로퍼티를 추가해야 한다.

### 생성자 함수에 의해 생성된 객체의 프로토타입

`new` 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하면 다른 객체 생성 방식과 마찬가지로 추상 연산 `OrdinaryObjectCreate`가 호출된다. 이때 추상 연산 `OrdinaryObjectCreate`에 전달되는 프로토타입은 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체다. 즉, 생성자 함수에 의해 생성되는 객체의 프로토타입은 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체다. 다음 예제를 살펴보자.

```js
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');
```

위 코드가 실행되면 추상 연산 `OrdinaryObjectCreate`에 의해 다음과 같이 생성자 함수와 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체와 생성된 객체 사이에 연결이 만들어진다.

![생성자 함수에 의해 생성된 프로토타입](/프로토타입/images/생성자%20함수에%20의해%20생성된%20프로토타입.png)

> 생성자 함수에 의해 생성된 프로토타입

표준 빌트인 객체인 `Object` 생성자 함수와 더불어 생성된 프로토타입 `Object.prototype`은 다양한 빌트인 메서드(`hasOwnProperty`, `propertyIsEnumerable` 등)를 갖고 있다. 하지만 사용자 정의 생성자 함수 `Person`과 더불어 생성된 프로토타입 `Person.prototype`의 프로퍼티는 `constructor`뿐이다.

<br>

## 프로토타입 체인

다음 예제를 살펴보자.

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty('name')); // true
```

`Person` 생성자 함수에 의해 생성된 `me` 객체는 `Object.prototype`의 메서드인 `hasOwnProperty`를 호출할 수 있다. 이것은 `me` 객체가 `Person.prototype`뿐만 아니라 `Object.prototype`도 상속받았다는 것을 의미한다.

`me` 객체의 프로토타입은 `Person.prototype`이다.

```js
Object.getPrototypeOf(me) === Person.prototype; // true
```

`Person.prototype`의 프로토타입은 `Object.prototype`이다. 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.

```js
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
```

따라서 위 예제를 그림으로 표현하면 다음과 같다.

![프로토타입 체인](/프로토타입/images/프로토타입%20체인.png)

> 프로토타입 체인

**자바스크립트는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 `[[Prototype]]` 내부 슬롯의 참조에 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다. 이를 프로토타입 체인이라고 한다. 프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 메커니즘이다.**

프로토타입 체인의 최상위에 위치하는 객체는 언제나 `Object.prototype`이다. 따라서 모든 객체는 `Object.prototype`을 상속받는다. **`Object.prototype`을 프로토타입 체인의 종점(end of prototype chain)**이라 한다. `Object.prototype`의 프로토타입, 즉 `[[Prototype]]` 내부 슬롯의 값은 `null`이다.

프로토타입 체인의 종점인 `Object.prototype`에서도 프로퍼티를 검색할 수 없는 경우 `undefined`를 반환한다. 이때 에러가 발생하지 않는 것에 주의하자.

```js
console.log(me.foo); // undefined
```

이처럼 자바스크립트 엔진은 프로토타입 체인을 따라 프로퍼티/메서드를 검색한다. 다시 말해, 자바스크립트 엔진은 객체 간의 상속 관계로 이루어진 프로토타입의 계층적인 구조에서 객체의 프로퍼티를 검색한다. 따라서 **프로토타입 체인은 상속과 검색을 위한 메커니즘**이라고 할 수 있다.

이에 반해, 프로퍼티가 아닌 식별자는 스코프 체인에서 검색한다. 다시 말해, 자바스크립트 엔진은 함수의 중첩 관계로 이루어진 스코프의 계층적 구조에서 식별자를 검색한다. 따라서 **스코프 체인은 식별자 검색을 위한 메커니즘**이라고 할 수 있다.

```js
me.hasOwnProperty('name');
```

위 예제의 경우, 먼저 스코프 체인에서 `me` 식별자를 검색한다. `me` 식별자는 전역에서 선언되었으므로 전역 스코프에서 검색된다. `me` 식별자를 검색한 다음, `me` 객체의 프로토타입 체인에서 `hasOwnProperty` 메서드를 검색한다.

이처럼 **스코프 체인과 프로토타입 체인은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는 데 사용된다.**
