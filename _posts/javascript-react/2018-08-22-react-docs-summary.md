---
layout: single
title: "[정리] React Main Concept"
tags: ['javascript', 'react']
date: 2018-08-22 10:00:00
---

[리액트 Docs](https://reactjs.org/docs/getting-started.html)에서 새로 알게된 정보들을 정리

## Hello World

컴포넌트를 렌더링하기

```javascript
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

## JSX란

JSX는 리액트 *element*를 생성한다.

변수에 대입하는 것도 가능

```javascript
const name = 'Josh Perez';
/** JSX 표현 안에서 외부의 변수를 사용하려면 {}으로 감싼다. 
  * Injection Attack을 방지해준다.  */
const element = <h1>Hello, {name}</h1>;
```

Attribute를 넘겨주는 것도 가능하다.
```javascript
const element = <div tabIndex="0"></div>;
```

**주의** 리액트의 JSX 표현의 attribute는 일반적인 HTML의 naming convention이 아닌 camelCase 형태로 표현한다. 예를 들어 `class`는 JSX에서 `className`이 되고, `tabindex`는 JSX에서 `tabIndex`가 된다.
{: .notice--warning}

JSX는 객체를 나타낸다. 바벨은 JSX를 컴파일할 때 `React.createElement()`함수를 호출한다.

```javascript
/* 아래 두 변수는 동일하다. */
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);

const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

## Element 렌더링

DOM안에 Element를 렌더링할 때는 어떤 DOM 객체 내부에 Element를 렌더링할 것인지 알려줘야 한다.

```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(
    element, 
    document.getElementById('root')
);
```

React Element는 *immutable*하다. 일단 생성하면 속성이나 children을 바꿀 수 없다. 대신 업데이트를 해야되는 경우가 생기면 변경 이전의 객체와 후의 객체를 비교한 후 Element는 필요한 부분만 업데이트한다.

## 컴포넌트와 props(property)

컴포넌트는 2가지 방식으로 선언 가능하다.
```javascript
/* Functional */
function Welcom(props) {
    return(
        <h1>Hello, {props.name}</h1>
    )
}
/* [ES6] class */
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

위와 같이 컴포넌트가 선언되면 다음과 같은 표현으로도 렌더링이 가능하다.

```javascript
ReactDOM.render(
  <Welcome 
    name="jade"
  />,
  document.getElementById('root')
);
```

`props`은 read-only다. 다시 말해 모든 React 컴포넌트들은 `props`에 대해 반드시 pure function처럼 작용해야 한다.

**pure function**: 입력값 변경을 하지 않으며 항상 같은 입력값에 같은 결과값을 반환하는 함수, side-effect가 없는 함수
{: .notice--primary}

## State와 라이프사이클

```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
  componentDidMount() {

  }

  componentWillUnmount() {

  }
}
```

* `constructor`는 반드시 `props`를 인수로 받아야 하고 생성자 내부에서 `super(props)`를 반드시 호출해야 한다. 생성자 내부에서만 `this.state`에 직접 assign이 가능하며 그 외에 state 변경이 필요한 경우에는 `this.setState` 메소드를 호출해야 한다.
* `<Clock />`라고 하면 알아서 `render()` 함수로 렌더링이 된다.
* `componentDidMount`는 객체가 생성되고 DOM에 랜더링이 완료된 즉시 실행되는 메소드
* `componentWillUnmount`는 객체가 DOM에서 제거될 때 실행되는 메소드

### Async state update

State 업데이트는 Async하다.

```javascript
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
// Correct, regular function also works
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

child 컴포넌트가 parent 컴포넌트의 state를 알 수 있는 방법은 없으며 parent 컴포넌트가 child 컴포넌트의 `props`를 통해 state를 전달하는 것은 가능하다.

## 이벤트 핸들링

일반적인 HTML과 JSX의 차이점은 다음과 같다.
* react 이벤트의 이름은 camelCase이지만 HTML은 lowercase이다.
* react는 이벤트 핸들러로 문자열이 아닌 함수 자체를 전달한다.
* react에서는 default behavior 방지를 위해 `false`를 반환할 수 없다.

```HTML
 <!-- HTML
    - lowercase: onclick
    - string: "activateLasers()" -->
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

```javascript
/* JSX
    - camelCase: onClick
    - function: {activateLasers}  */
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

react에서는 default behavior 방지를 위해 `false`를 반환할 수 없다. 대신 `preventDefault`를 명시적으로 호출해야 한다. 예를 들어 `a`태그의 default behavior인 새로운 페이지를 여는 것을 방지하려고 할 때
```html
<a href="#" 
   onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

반면 자바스크립트는 다음과 같다. `e`는 synthetic 이벤트로 react에서 *W3C* 스펙에 따라서 정의해 놓은 것이다. 자세한 내용은 [SyntheticEvent](https://reactjs.org/docs/events.html) 를 참고하면 된다.
```javascript
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```
### 콜백 함수 바인딩 관련
클래스 메소드를 콜백함수로 사용할 경우 바인딩을 해야 한다.
```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

`bind`함수를 호출하는 것이 귀찮다면 다음 2가지 방법 중 하나를 사용할 수 있다.

* *public class fields syntax*(Create React App에서만 가능)
```javascript
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

* *arrow function`을 이용
```javascript
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

### 인자 넘기기
이벤트 핸들러에 인자 넘기기
```javascript
/* 두 표현 모두 동일 */
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```
바인딩을 하는 경우 자동적으로 `e` 인수가 넘겨진다.

## 조건이 있는 렌더링

if문 쓰면 된다.

```javascript
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```

JSX내부에서 인라인 if 문

```javascript
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

`true && expression`은 항상 `expression`을 반환하고, `false && expression`은 항상 `false`를 반환한다.(자바스크립트 문법)

인라인 if-else문: `(condition) ? (if true) : (if false)`

렌더링 함수가 `null`을 반환하게 함으로써 렌더링 자체를 방지할 수도 있다.

## 리스트와 키

element의 `Array`로 여러개의 컴포넌트를 한번에 렌더링할 수 있다.
```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

리스트(`<li>`)를 반환시 키를 제공해야 한다. *키*는 특별한 문자열 attribute로 element의 리스틀 만들 때 필요하다.

```javascript
<li key={number.toString()}>
      {number}
    </li>
```

키는 react가 어떤 아이템이 변경되고 추가되고 제거되었는지 확인할 때 사용한다. 다음과 같은 방법으로 Array의 인덱스로 키를 배정하는 방법도 있으나 리스트가 추가/제거 되어 배열 크기가 바뀌는 경우 ID가 겹치는 경우가 발생할 수 있다. 이와 관련된 퍼포먼스 이슈는 [배열 인덱스 키 퍼포먼스 이슈](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)서 확인할 수 있다. 키가 필요한 이유가 궁금하면 [키가 필요한 이유](https://reactjs.org/docs/reconciliation.html#recursing-on-children)에서 확인할 수 있다. 나중에 읽어봐야 겠다.

```javascript
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```

### 키 사용하기

* 키를 가지고 컴포넌트 추출하기

키는 무조건 리스트에만 하는 것이 아니라 Array가 둘러싸고 있는 상황에서만 의미가 있다.


```javascript
/* 잘못된 예시 */
function ListItem(props) {
  const value = props.value;
  return (
    // Wrong! There is no need to specify the key here:
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Wrong! The key should have been specified here:
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
```javascript
/* 잘된 예시 */
function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()}
              value={number} />

  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

당연한 얘기지만 같은 Array내에서의 키는 동일해야 한다.

JSX표현식에서 {}안에 임베딩하는 것도 가능하다.

```javascript
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

## Form

HTML form element는 react의 다른 DOM elemnt와 다르다. form element는 그 자체로 내부적으로 state를 가지고 있기 때문이다. react에서는 **controlled components**라는 테크닉을 통해 편리하게 관리할 수 있다.

### Controlled Components

* HTML의 form element는 자체적인 state를 가지고 있으며 user input에 따라 업데이트한다. 
* react의 컴포넌트의 state 속성에 저장되며 업데이트는 `setState()`를 통해서만 가능하다.
* 위의 두 가지 방법을 적절하게 조합하여 react 컴포넌트의 state만 유일한 상태 저장 방법으로 사용한다. react 컴포넌트가 input form element의 상태까지 관리하는 경우 **controlled components**라고 부른다.

다음 예시를 보자

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
컴포넌트가 렌더링 되면 생성자에 따라 `state.value=""`으로 초기화 된다. 초기화 된 `state.value`값은 다시 text `input` element로 들어간다. 키스트로크가 발생할 때 마다 `this.handleChange`라는 콜백 함수를 실행하여 컴포넌트의 state를 바꾼 후 다시 text `input` element를 업데이트하여 렌더링하다. 이렇게 함으로써 react 컴포넌트가 input 값을 관리할 수 있다.

### textarea tag

대충 예시만 보면 될 것 같다.

```javascript
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

### select tag

이것도 예시로 갈음합니다.

```javascript
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'}; // default selection

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

다중 선택이 필요한 경우 다음과 같이 해결할 수 있다. <br> `<select multiple={true} value={['B', 'C']}>`
{: .notice--primary}

### file input tag
**uncontrolled**이므로 그냥 쓰면 된다. value가 read-only이기 때문이다.

### 여러 input 핸들링
```javascript
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;
    /* input element의 name에 따라 알아서 state 저장 
     * 단 이렇게 하려면 input element의 name과 state의 
     * name이 동일해야 함
     */
    this.setState({
      [name]: value 
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

단 위의 방법은 ES6에서만 가능한 문법이기 때문에 아닌 경우에는 ES5인 경우에는 다른 방법을 사용하면 된다.
```javascript
/* ES6 */
this.setState({
      [name]: value 
});
/* ES5 */
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```

### null값 처리

잘 모르겠다.

Specifying the value prop on a controlled component prevents the user from changing the input unless you desire so. If you’ve specified a value but the input is still editable, you may have accidentally set value to undefined or null.
{: .notice--primary}

```javascript
/* The input is locked at first but becomes 
 * editable after a short delay.  */
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);
```

이미 완성되어있는 코드베이스를 Controlled Components로 바꾸기 힘든 경우에는 Uncontrolled Component를 사용할 수도 있다. 이는 [Uncontrolled Components](https://reactjs.org/docs/uncontrolled-components.html)에서 자세히 다룬다.

## Lifting State Up

> A Single Source of truth

컴포넌트의 상태를 나타내는 데 있어 같은 정보를 저장하는 state가 2개 있으면 안된다는 뜻인 것 같다.
경험을 통해 어떻게 state를 lift up할 지 알아봐야 겠다. 예시는 [Lift State Up](https://reactjs.org/docs/lifting-state-up.html)에서 온도 계산기를 만듦으로써 확인할 수 있다.

## Composition vs Inheritance

리액트한 composition 모델로 컴포넌트간 코드 재사용을 위해 Inheritance 보다는 **composition**을 권합니다.
{: .notice--primary}

### Containment
컴포넌트들은 자신의 자식 컴포넌트를 미리 알 수는 없다. 제러닉한 "box" 형태의 `Sidebar`나 `Dialog` 같은 컴포넌트들이 그렇다. 이런 경우에는 `childeren`이라고 하는 특별한 `props`를 이용하여 child element를 output으로 직접 보내는 것이 좋다.

```javascript
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      {/* 이부분부터 props.children 시작*/}
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
      {/* 이부분부터 props.children 끝*/}
    </FancyBorder>
  );
}
```

공간이 2개 이상으로 나눠져야 하는 경우에는 직접 jsx 객체를 props로 넘긴다.
```javascript
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

### Specialization

genertic한 컴포넌트를 래핑해서 좀더 specific한 컴포넌트를 만드는 것을 *Specialization*이라고 한다.(느낌상 class instantiation과 비슷한 느낌이다.) 예를 들면
`Dialog`를 Specialize해서 `WelcomeDialog`를 만드는 것이다.

```javascript
/* Genertic Component */
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}
/* Specialization Component */
function WelcomeDialog() {
  return (
    {/* wrapping? with props parameter */}
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```

function이 아니라 class에 해도 완벽하게 작동한다.(너무 당연!)

---

페이스북에서 오랜 기간동안 사용해본 결과 상속이 필요한 경우는 발생하지 않았다고 한다. 
props와 composition 만으도로 명시적(explicit)이고 안전한(safe) 방법으로 충분한 flexibility를 제공한다. props가 어떤 타입이든 잘 받기 떄문이다.

만약의 컴포넌트의 Non-UI한 기능들을 재사용하고 싶다면 재사용할 부분을 자바스크립트 모듈로 따로 빼내면 된다고 한다.

## React 방식으로 사고하기
[예시 코드](https://reactjs.org/docs/thinking-in-react.html)는 여기서 자세히 확인할 수 있습니다.

### 1. 컴포넌트 계층 구조로 UI를 나누기
컴포넌트로 나누고 이름을 부여한다. 각 컴포넌트는 하나의 역할 만을 담당하도록 만드는 것이 중요하다.

![ui-mock-up](https://reactjs.org/static/thinking-in-react-components-eb8bda25806a89ebdc838813bdfa3601-82965.png)

### 2. static 버전을 만들기
*State를 사용하지 않고* intertactivity가 없는 컴포넌트를 구현한다.

### 3. UI state에 대한 최소한의 표현을 확인하기

> Identify The Minimal (but complete) Representation Of UI State

UI를 interactive하게 만들기 위해서는 change를 triggering할 수 있어야 되면 이를 쉽게 할 수 있는 것이 **state**이다.

> DRY: Don't Repeat Yourself

mutable한 state를 최소한으로 해야 한다.

먼저 사용되는 데이터를 확인한다.(예시 코드에서)

* The original list of products
* The search text the user has entered
* The value of the checkbox
* The filtered list of products

각 데이터에 대해 아래 질문을 해보면서 state를 사용해야되는지 아닌지 확인하다.
* props를 통해 parent에서 넘겨지는 데이터인가? 그렇다면 state가 아니다.
* 시간이 지나도 변하지 않는가? 그렇다면 state가 아니다.
* 다른 state나 props를 통해서 계산이 가능한가? 그러면 state가 아니다.

위 질문들의 대한 결론은 아래와 같다.

* The original list of products -> not state
* The search text the user has entered -> state (변할 수 있음)
* The value of the checkbox -> state (변할 수 있음)
* The filtered list of products -> not state(original list를 통해 구할 수 있음)

### 4. state가 업데이트되는 부분을 확인하기

단계 3에서 사용하기로 한 각 state에 대해 아래의 절차들을 생각해본다.
* 이 state를 기반으로 하는 모든 컴포넌트를 확인한다.
* 위에서 찾은 모든 컴포넌트들의 공통적인 owner 컴포넌트(a common owener component)를 확인한다.
  - 여기서 찾은 공통 owerner 컴포넌트나 더 높은 계층의 컴포넌트에 state가 들어가야 한다.
  - 만약 찾을 수 없다면, 새로 컴포넌트를 만든다.

### 5. 반대 방향의 데이터 플로우를 추가하기

콜백 함수들을 호출하면서 더 높은 계층으로 올라가는 경우들을 말하는 듯 하다.
input 관련 element들에 대한 처리도 여기서 이루어지는 듯 하다.

---

다시 차근차근 읽어보면서 봐야겠다. 하루종일 이 글만 썼더니 나중에는 무슨 내용인지도 잘 모르겠다. 