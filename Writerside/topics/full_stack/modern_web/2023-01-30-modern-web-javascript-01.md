# 04. ES6(JavaScript) 기본

## JavaScript Basic

### Function(함수) & Method(메소드)

Javascript 에서는 Method는 Function 이지만, 모든 Function이 Method는 아니다.

##### Function

Javascript 내장객체인 Fucntion 생성자로 생성된 객체

##### Method

Function이 객체의 Property 일때 Method

```Javascript
var work = function(){};
console.log(work instanceof Function) // true 출력

```

Function은 호출할 수 있으나, 객체는 호출할 수 없다,  
Function은 Prototype Property를 가지지만 다른 객체는 그렇지 않다.

객체를 선언할 때는 new 지시자로 생성한다.  
생성자를 선언할 때에는 대문자로 시작한다.

```Javascript
function User(){
}
var uer new User();
```

### Object(객체) & Class(클래스)

#### 객체 생성

#### Object 생성자

```Javascript
var user = new Object();
user.name = 'Sunny'
user.interests = ['Traveling', 'Swimming']
user.greeting = function(){
		console.log('HI ' + this.name);
};
```

#### 객체 리터럴

```Javascript
var user = {
		name : 'Sunny',
		interests : ['Traveling', 'Swimming'],
		greeting : function(){
				console.log('HI ' + this.name);
		},
		role : 'Engineer',
		get getRole() {
    		return this.role
    }
}
```

#### 생성자 함수

```Javascript
function User(){
		this.name : 'Sunny',
		this.interests : ['Traveling', 'Swimming'],
  	this.greeting = fucntion(){
    		console.log('HI ' + this.name);
}
var user = new User();
```

#### Object.create()

```Javascript
function User(){}
  
var user = Object.create(User.prototype, {
  	name: {value: 'Sunny'},
  	interests : {value: ['Traveling', 'Swimming']}
});

User.prototype.greeting = function() {
    		console.log('HI ' + this.name);
}
```

#### 생성함수

```Javascript
function createUser(){
  	var user = {};
		user.name = 'Sunny';
		user.interests = ['Traveling', 'Swimming'];
  	user.greeting = fucntion(){
    		console.log('HI ' + this.name);
		};
  	return user;
}
var user = createUser();
```

#### ES6 Class

```Javascript
class User{
		constructor(){
    		this.name = 'Sunny';
				this.interests = ['Traveling', 'Swimming'];
    }
  	greeting() {
    		console.log('HI ' + this.name);
		}
}
let user = new User();
```

```Javascript
let User = class {
		constructor(){
    		this.name = 'Sunny';
				this.interests = ['Traveling', 'Swimming'];
    }
  	greeting() {
    		console.log('HI ' + this.name);
		}
}
```

### Object(객체) & Property(프로퍼티) & Property Attribute

Object 는 논리적으로 Property의 집합  
Attribute는 Property의 상태를 정의하고 설명

- Data Property

| Attribute      | Description                                                           | Type     |
|----------------|-----------------------------------------------------------------------|----------|
| `value`        | Javascrip의 모든 자료형 가능                                                  | Any Type |
| `writable`     | Data Property의 변경 가능 여부 정의                                            | Boolean  |
| `emumerable`   | For-in 구문을 이용해 열거 가능 여부 정의                                            | Boolean  |
| `configurable` | 제거 가능 여부, Access Property 변경 여부, 쓰기 불가 여부, emumerable 속성의 수정 가능 여부 정의 | Boolean  |

- Access Property

| Attribute      | Description                                                           | Type                   |
|----------------|-----------------------------------------------------------------------|------------------------|
| `get accessor` | Function 객체 혹은 underfined 지정                                          | Function \| Underfined |
| `set accessor` | Function 객체 혹은 underfined 지정                                          | Function \| Underfined |
| `emumerable`   | For-in 구문을 이용해 열거 가능 여부 정의                                            | Boolean                |
| `configurable` | 제거 가능 여부, Access Property 변경 여부, 쓰기 불가 여부, emumerable 속성의 수정 가능 여부 정의 | Boolean                |

```Javascript 
// Define properties using Object.defineProperty()
function User (name, department) {
  var _department = department;
  var _name = name;
  Object.defineProperty(this, 'name', {
    value: _name,
    writable: true,
    enumerable: true,
    configurable: false
  }); 
  Object.defineProperty(this, 'department', {
    get: function () {
      console.log('Retrieving department');
      return _department;
    },
    set: function (newValue) {
      console.log('Updating department value to "' + newValue + '"');
      _department = newValue;
    },
    enumerable: true,
    configurable: true
  });
  Object.defineProperty(this, 'greeting', {
    value: function () {
      console.log('Hi, I\'m ' + _name + '.');
    },
    enumerable: false,
    configurable: false
  }); 
} 
```

### Prototype(프로토타입) & Inheritance(상속)

#### prototype

객체의 명세  
다른 객체에 공유 프로퍼티(shared properties) 를 제공하는 객체  
함수 객체만 프로토타입을 가진다.  
Arrow Function은 프로토타입을 가지지 않는다.

```Javascript
function User (name, interests) {
  this.name = name;
  this.interests = interests;
}
User.prototype.greeting = function () {
   console.log('Hi, I\'m ' + this.name + '.');
}
function TeamMember (name, interests, tasks) {
   User.call(this, name, interests);
   this.tasks = tasks;
}
TeamMember.prototype = Object.create(User.prototype);
TeamMember.prototype.greeting = function () {
  console.log('I\'m ' + this.name + '. Welcome to the team!');
};
TeamMember.prototype.work = function () {
  console.log('I\'m working on ' + this.tasks.length + ' tasks');
};
var member = new TeamMember('Sunny', ['Traveling'],
                            ['Buy three tickets','Book a hotel']);
member.greeting();  // I'm Sunny. Welcome to the team!
member.work();      // I'm working on 2 tasks

console.log(member instanceof TeamMember); // true
console.log(member instanceof User);       // true
console.log(member instanceof Object);     // true

User.prototype.eat = function () {
  console.log('What will I have for lunch?');
};
member.eat();     // What will I have for lunch?         

// Add a method to the top
Object.prototype.move = function () {
  console.log('Every object can move now');
};
member.move();    // Every object can move now
var alien = {};
alien.move();     // Every object can move now
User.move();      // Even the constructor function
```

### Scope(스코프) & Closure(클로저)

#### Scope(스코프)

변수의 접근성

- Golbal Scope(전역 스코프)
- Function Scope(함수 스코프)
- Block Scope(블록 스코프) : let 과 const 키워드 사용

#### Colsure(클로저)

함수가 또다른 함수 내부에 중첩되는 형태  
함수가 아무리 깊게 중첩이 되어도 부모 함수 스코프에 접근 가능하다.  
반대로 부모 혹은 전역 스코프에서 중첩함수의 객체레 접근은 불가능하다.  
클로저는 자신이 생성될 때의 환경(Lexical environment)을 기억하는 함수다.

1. 현재 상태를 유지하고 변경된 최신 상태를 유지한다.
2. 전역 변수의 사용을 억제

```Javascript
// Scope and Closure JavaScript Example
function bookHotel (city) {
  var availableHotel = 'None';
  for (var i=0; i<hotels.length; i++) {
    var hotel = hotels[i];
    if (hotel.city === city && hotel.hasRoom) {
      availableHotel = hotel.name;
      break;
    }
  }
  // i and hotel still accessible here
  console.log('Checked ' + (i+1) + ' record(s)'); // Checked 2 record(s)
  console.log('Last checked ' + hotel.name);      // Last checked Hotel B
  {
    function placeOrder() {
      var totalAmount = 200;
      console.log('Order placed to ' + availableHotel);
    }
  }  
  placeOrder();
  // Not accessible
  // console.log(totalAmount);
  return availableHotel;
}

var hotels = [{name: 'Hotel A', hasRoom: false, city: 'Sanya'},
              {name: 'Hotel B', hasRoom: true, city: 'Sanya'}];
console.log(bookHotel('Sanya')); // Hotel B
// Not accessible
// console.log(availableHotel);
```

### This 키워드

this는 현재 실행 컨텍스트(current execution context)에 참조하며 이 실행 컨텍스트도 하나의 객체이다.  
Javascript 에서는 스코프는 접근성에 대한것이지만, 실행 컨텍스트는 동작 중인 실행 가능한 소유권

- 전역 코드  
  javascript 프로그램이 시작되는 곳부터 수행되는 코드이다.  
  브라우저의 경우 window 객체

- Eval 코드  
  내장된 eval() 함수로 전달되는 문자열의 값

- 함수 코드  
  함수의 본문 코드

- 함수를 호출하는 방법  
  함수 본문 내의 this 가 다른 세가지 유형의 함수 호출로 만들어진 인스턴스를 제외하고

1. 생성자 함수 호출 : new User()  
   생성자로 생성된 객체 참조

2. 직접 함수 호출: ask()  
   전역 컨텍스트를 참조

3. 메소드 호출 : user.speak()  
   메소드가 속한 객체를 참조

4. 컨텍스트 변경 호출 : ask.call(this)  
   call 메소드의 첫번째 인자로 전달받은 객체를 참조

### 호이스팅(Hoisting)

Javascript 의 인터프리터가 한수 선언과 변수 선언을 선언들이 속해있는 Scope 최상단으로 끌어올리는 방법

## ES6 Basic

### Block Scope, let, const

let 과 const는 변수 호이스팅이 적용되지 않기 때문에 사전에 선언이 필요하다.  
Block Scope 내에서 let과 const를 사용하여 변수 재정의는 허용하지 않는다.

- let : 변수 정의
- const : 상수 정의

### Class

Class 구문으로 생성자를 생선하고 상위 클래스로부터 확장하고 적정 Method를 생성, Getter, Setter 생성

### 객체 리터럴(Object Literals)

Prototype 설정, Property 축약 표헌(프로퍼티 값으로 변수를 사용하는 경우 Name 지정, 이는 변수의 이름으로 자동으로 생성된다.), Method 축약 표현(Function 키워드를 생략),  
Super 호출, 표현식을 이용한 Prperty 계산(객체 프로퍼티 이름을 표현식으로 지정할 수 있고 대괄호 안에 표현식을 작성)

### Arrow Function(화살표 함수)

'=>' 구문을 이용한 함수 축약 표현

#### 표현 방법

함수 인자로 시작해 화살표(=>), 그다음 함수 본문

```Javascript
const fruits = [{name: 'Apple', price: 100}, {name: 'Orange', price: 80}, {name: 'Banana', price: 120}];

// Variation 1

// When no arguments, an empty set of parentheses is required
var countFruits = () => fruits.length;
// equivalent to ES5
var countFruits = function () {
  return fruits.length;
}; 

// Variation 2

// When there is one argument, parentheses can be omitted.
// The expression value is the return value of the function.
fruits.filter(fruit => fruit.price > 100);
// equivalent to ES5
fruits.filter(function(fruit) {
  return fruit.price > 100;
});

// Variation 3

// The function returns an object literal, it needs to be wrapped
// by parentheses.
var inventory = fruits.map(fruit => ({name: fruit.name, storage: 1}));
// equivalent to ES5
var inventory = fruits.map(function (fruit) {
  return {
    name: fruit.name,
    storage: 1
  };
});

// Variatoin 4

// When the function has statements body and it needs to return a 
// result, the return statement is required
var inventory = fruits.map(fruit => {
  console.log('Checking ' + fruit.name + ' storage');
  return {name: fruit.name, storage: 1};
});
// equivalent to ES5
var inventory = fruits.map(function (fruit) {
  console.log('Checking ' + fruit.name + ' storage');
  return {name: fruit.name, storage: 1};
});
```

#### 특징

- 상위 스코프의 실행컨텍스트를 가져와 실행하므로 자신의 this를 가지지 않는다.
- 프로토타입 객체를 가지지 않기 때문에 생성자 함수도 가지지 않는다.

### 매개변수 기본값

```Javascript
function addToCart(item, size = 1) {
  shoppingCart.push({item: item, count: size});
}
```

### 나머지 매개변수

```Javascript
// equivalent to rest parameters in ES6
function workout(exercise1, ...todos) {
  console.log('Start from ' + exercise1);    // Start from Treadmill
  console.log(todos.length + ' more to do'); // 2 more to do
  console.log('Args length: ' + workout.length); // Args length: 1
}
```

### 전개구문

나머지 매개변수를 배열에 할당하면 배열의 요소들을 전개시킨다.

### 비구조화 할당(Destructuring Assignment)

#### 객체 비구조화

```Javascript
// Object destructuring
let user = {name: 'Sunny', interests: ['Traveling', 'Swimming']};
let {name, interests, tasks = []} = user;
console.log(name);       // Sunny
console.log(interests);  // ["Traveling", "Swimming"]
console.log(tasks);      // undefined
```

#### 배열 비구조화

```Javascript
// Array destructuring
let [first, second] = ['Traveling', 'Swimming', 'Shopping'];
console.log(first);   // Traveling
console.log(second);  // Swimming
```

#### 중접 비구조화

```Javascript
// Nested destructuring
let user = {name: 'Sunny', interests: ['Traveling', 'Swimming']};
let {interests: [,second]} = user;
console.log(second);    // Swimming
console.log(interests); // ReferenceError
```

#### 나머지 요소 비구조화

```Javascript
// Rest elements
let [first, ...others] = ['Traveling', 'Swimming', 'Shopping'];
console.log(others);   // ["Swimming", "Shopping"]
```

#### 함수 매개변수 비구조화

```Javascript
// Function parameters destructuring
function workout({gym, todos}) {
  let [first] = todos;
  console.log('Start ' + first + ' in ' + gym);
}
let today = {gym: 'Gym A', todos: ['Treadmill']};
workout(today); // Start Treadmill in Gym A
workout()       // TypeError
```

### 템플릿 리터럴

```Javascript
let user = {
  name: 'Ted',
  greeting () {
    console.log(`Hello, I'm ${this.name}.`);
  }
};
user.greeting();  // Hello, I'm Ted.
```

### Modules(모듈)

모듈을 구정하고 정적 모듈 구조를 만드는데, 컴파일 시점에서 가져오기(import), 내보내기(export)를 결정할 수 있다.  
import와 export 선언은 최상위에 위치하여야 하며, if/else, try/catch 와 같은 블록 구문에는 사용할 수 없다.

### Promise(프로미스)

javascript 에서 비동기 프로그래밍에서 사용하는 Callback, Event 외에 선택하는 기능  
프로미스는 3가지 상태를 가진다.

- 대기(pending) : 프로미스의 초기상태
- 이행(Fulfilled) : 작업을 성공적으로 완료 했을 때의 상태
- 실패(Rejected) : 에러 혹은 기타 이유로 작업이 완료하지 못했을 때의 상태

* Callback 지옥  
  Callback 함수를 선언하고 순차적으로 실행시키고자 하면 계속 중첩하여 함수를 선언하여 써야한다.  
  ex) getProject Call 내부에 getTask 호출 이후에 render 함수 호출  
  이를 Callback 지옥에 빠진다 라고 표현한다.

```Javascript
function getProjects(callback) {
  // Use setTimeout to stimulate calling server API
  setTimeout(() => {
    callback([{id:1, name:'Project A'},{id:2, name:'Project B'}]);
  }, 100);
}

function getTasks(projects, callback) {  
  // Use setTimeout to stimulate calling server API
  setTimeout(() => {
    // Return tasks of specified projects
    callback([{id: 1, projectId: 1, title: 'Task A'}, 
              {id: 2, projectId: 2, title: 'Task B'}]);
  }, 100);    
}

function render({projects, tasks}) {
  console.log(`Render ${projects.length} projects and ${tasks.length} tasks`);
}

getProjects((projects) => {
  getTasks(projects, (tasks) => {
    render({projects, tasks});
  });
});

```

Promise 사용시 이러한 중첩된 Call을 사용할 필요가 없어진다.  
함수 메소드를 Promise 로 선언하고 나서 then 함수로 다음에 이루어질 Method를 호출하는 방식

```Javascript

function getProjects() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve([{id:1, name:'Project A'},{id:2, name:'Project B'}]);
    }, 100);
  });  
}

function getTasks(projects) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({projects, tasks:['Buy three tickets', 'Book a hotel']});
    }, 100);
  });
}

function render({projects, tasks}) {  
  console.log(`Render ${projects.length} projects and ${tasks.length} tasks`);
}

getProjects()
.then(getTasks)
.then(render)
.catch((error) => {
  console.log('Hanlding error', error);
});
```

* .then() : 상태가 이행으로 변경될 때 호출
* .catch() : 상태가 실패로 변경될 때 호출
* .all(iterable) : 다중 병렬 프로미스에서 모든 결과 집계 - 모든 요청이 완료될때 까지 대기
* .race(iterable) : 다중 병렬 프로미스 중에서 하나가 실패가 되면 즉시 이행 또는 거부 프로미스 호출 - 먼저 완료된 요청에 대해서 리턴
* .resolve(value) : 성공 상태의 프로미스 객체 반환
* .reject(reason) : 실패 상태의 프로미스 객체 반환
