# ✔ '02.Creating Content with JSX' 이론 정리



## ▶ 10.  Showing Basic Content

- `index.html`이랑 `index.js`만 남기고 다른 파일은 삭제해도 실행됨

### 🔹 `index.js`에 적어야 하는 코드 (5단계)

> 참고: [React.createElement, React.Component, ReactDOM.render의 동작 원리](https://medium.com/crossplatformkorea/react-%EB%A6%AC%EC%95%A1%ED%8A%B8%EB%A5%BC-%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90-02-react-createelement%EC%99%80-react-component-%EA%B7%B8%EB%A6%AC%EA%B3%A0-reactdom-render%EC%9D%98-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-41bf8c6d3764)<br>
> 자료: [001_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/001_-_jsx)

1. React와 ReactDOM 라이브러리 가져오기
   - React 라이브러리: component가 무엇인지 정의하고, 다수의 component를 함께 작동시킴
   - ReactDOM 라이브러리: 브라우저에서 component를 HTML로 전환해 화면에 나타내줌

   ```js
   import React from 'react';
   import ReactDOM from 'react-dom/client';
   ```

2. id가 'root'인 div 태그 가져오기
   
   ```js
   const el = document.getElementById('root');
   ```

3. root 요소가 React에 의해 control되게 하기

   ```js
   const root = ReactDOM.createRoot(el);
   ```

4. component 생성
   
   ```js
   function App() {
    return <h1>Hi there!</h1>
   }
   ```

5. component를 화면에 나타내기
   
   ```js
   root.render(<App />);
   ```



## ▶ 11. What is JSX?

- JSX → `Babel` tool을 통해 JSX를 JS로 변환 → `React.createElement()`는 object 구조로 element를 반환

   ```jsx
   // JSX
   <h1>Hi there!</h1>
   ```

   ```js
   // Babel에 의해 변환된 결과
   React.createElement("h1", null, "Hi there!");
   ```

   ```js
   // object 구조로 element를 반환한 결과
   {
    $$typeof: Symbol(react.element),
    key: null,
    props: {children: 'Hi there!'},
    ref: null,
    type: 'h1'
   }
   ```

- [Babel 공식 사이트](https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.20.14&externalPlugins=&assumptions=%7B%7D)에서 JSX를 입력하면 Babel에 의해 반환된 결과를 한 눈에 볼 수 있음
  
   ```jsx
   // JSX
   <div>
     <h1>Hi there!</h1>
     <ul>
       <li>Red</li>
       <li>Green</li>
       <li>Blue</li>
     </ul>
   </div>
   ```

   ```js
   // Babel에 의해 JS로 반환된 결과
   "use strict";
 
   /*#__PURE__*/React.createElement("div", null, 
   /*#__PURE__*/React.createElement("h1", null, "Hi there!"), 
   /*#__PURE__*/React.createElement("ul", null, 
   /*#__PURE__*/React.createElement("li", null, "Red"), 
   /*#__PURE__*/React.createElement("li", null, "Green"), 
   /*#__PURE__*/React.createElement("li", null, "Blue")));
   ```



## ▶ 12. Printing JavaScript Variables in JSX

> 실습: https://codesandbox.io/embed/react-component-variable-silseub-xplu2c?fontsize=14&hidenavigation=1&theme=dark<br>
> 자료: [003_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/003_-_jsx)

- component 안에서 변수를 선언할 수 있음
- return 안에서 중괄호 `{}`를 이용하면 JS 변수을 참조하거나 expression을 사용할 수 있음
   - 주의) string이나 number 데이터 타입만 참조 가능
   - array, boolean, null 등과 같은 데이터 타입은 화면에 나타나지 않거나 이상하게 나타남
   - object을 참조하면 에러를 발생

   ```js
   function App() {
    let message = 'Bye there!';
    if (Math.random() > 0.5) {
      message = 'Hello there!';
    }

    return <h1>{message}</h1>
   }
   ```



## ▶ 13. Shorthand JS Expressions

> 실습: https://codesandbox.io/s/13-shorthand-js-expressions-silseub-xt7lbm?file=/src/App.js<br>
> 자료: [004_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/004_-_jsx)

- return 안에서 중괄호 `{}`를 이용하면 **JS 변수**을 참조하거나 **expression**을 사용할 수 있음

   ```js
   // 방법1) JS 변수를 이용
   function App() {
    const date = new Date();
    const time = date.toLocaleTimeString();

    return <h1>{time}</h1>
   }
   ```

   ```js
   // 방법2) expression 사용
   function App() {
    return <h1>{new Date().toLocaleTimeString()}</h1>
   }
   ```



## ▶ 16. Typical Components Layouts

> 자료: [007_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/007_-_jsx)

- 아래 component처럼 전형적으로 상단에는 JSX에 넘겨줄 compute values가 존재하고, 하단에는 component가 반환할 content가 존재함
  
    ```js
    function App() {
      const name = 'Samantha';
      const age = 23;
   
      return (
         <h1>
            Hi, my name is {name} and my age is {age}
         </h1>
      );
    }
    ```



## ▶ 17. Customizing Elements with Props

> 실습: https://codesandbox.io/s/17-customizing-elements-with-props-silseub-lb36ez<br>
> 자료: [008_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/008_-_jsx)

- HTML의 요소에 속성을 넣는 것처럼 JSX의 요소에 속성을 넣을 수 있음
    - props: element를 customize하는 것
    - props는 `property명=property값` 형태로 작성 가능

### 🔹 props 작성하는 법

1. 중괄호`{}`를 사용해 변수를 참조하거나 expression 사용
  
    ```js
    function App() {
      const inputType = "number";
      const minValue = 5;

      return <input type={inputType} min={minValue}/>;
    }
    ```

2. 변수 정의 없이 바로 값 할당
  
    ```js
    function App() {
      return <input type="number" min={5}/>;
    }
    ```

### 🔹 Data Type 별로 props 값 나타내는 법

- `strings`: 쌍따옴표 `""`로 감싸야 함
- `numbers`, `arrays`, `objects`, `boolean`: 중괄호 `{}`로 감싸야 함
- 변수: 중괄호 `{}`로 감싸야 함

    ```js
    function App() {
      return (
         <input 
            type="number"             // string
            min={5}                   // number
            list={[1,2,3]}            // array
            style={{ color: 'red' }}  // object(바깥쪽 중괄호:JSX, 안쪽 중괄호:객체)
            alt={message}             // 변수
         />
      );
    }
    ```

### 🔹 JSX에서 Object 사용

- 태그 사이에 object 사용 불가
  
    ```js
    function App() {
      const config = { color: 'red' } 
      return <h1>{config}</h1>;        // Error 발생
    }
    ```

- Props의 value로 object 사용 가능 
  
    ```js
    function App() {
      const config = { color: 'red' } 
      return <input abc={config} />;
    }
    ```


## ▶ 18. Converting HTML to JSX

> 자료: [009_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/009_-_jsx)

- HTML 요소의 속성(attribute name/value)은 JSX와 같지 않음
- HTML을 JSX로 전환하는 방법
    - 규칙1) 모든 prop명은 camelCase를 따라야 함 
    - 규칙2) Number는 중괄호 `{}`로 감싸야 함
    - 규칙3) Boolean은 중괄호 `{}`로 감싸야 함
       - `true`인 경우, 생략 가능 (prop명만 작성 해도 됨)
       - `false`인 경우, 중괄호 `{}`로 감싸서 값을 명시해 줘야 함
    - 규칙4) `class` 속성은 `className`으로 작성해야 함
    - 규칙5) In-line style은 objects로 작성해야 함
- 위 규칙이 지켜지지 않으면 Error 발생

### 🔹 규칙1) 모든 prop명은 camelCase

```html
<!-- HTML -->
<input maxlength='5'/>
<form autocapitalize />
<form novalidate />
```

```jsx
// JSX
<input maxLength={5} />
<form autoCapitalize />
<form noValidate />
```



## ▶ 19. Applying Styling in JSX

### 🔹 규칙2) Number는 중괄호 `{}`로 감싸야 함

```html
<!-- HTML -->
<input maxlength='5'/>
<meter optimum='50' />
```

```jsx
// JSX
<input maxLength={5} />
<meter optimum={50} />
```

### 🔹 규칙3) Boolean은 중괄호 `{}`로 감싸야 함

```html
<!-- HTML -->
<input spellcheck='true'/>
<input spellcheck='false'/>
```

```jsx
// JSX
<input spellCheck />
<input spellCheck={false} />
```

### 🔹 규칙4) `class` 속성은 `className`으로 작성해야 함

```html
<!-- HTML -->
<div class='divider' />
```

```jsx
// JSX
<div className="divider" />
```

### 🔹 규칙5) In-line style은 objects로 작성해야 함

```html
<!-- HTML -->
<a style="text-decoration: none; padding-top: 5px;" />
<input style="border: 1px solid blue;" />
```

```jsx
// JSX
<a style={{ textDecoration: 'none', paddingTop: '5px' }} />
<input style={{ border: '1px solid blue' }} />
```



## ▶ 21. Extracting Components

> 자료: [012_-_jsx](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/2.Creating_Content_with_JSX/012_-_jsx)

- index.js 파일 하나에 component 여러개를 넣으면 관리하기 힘들기 때문에 분리하는 것이 좋음
- `index.js` 파일에서 App component를 분리해 `App.js` 파일에 넣음

### 🔹 Component를 만드는 법

1. 새 파일을 만든다
    - 파일명의 첫 글자는 대문자로 시작해야 한다 (by convention)

2. 새로운 파일 안에 component를 적는다
    - JSX를 반환하는 함수 형태

    ```js
    function App() {
      return <h1>Bye there!</h1>;
    }
    ```

3. 파일 하단에 component를 `export` 한다

    ```js
    export default App;
    ```

4. 이 component가 필요한 다른 파일에 해당 component를 `import` 한다 

    ```js
    import App from './App';
    ```

5. component를 사용한다

    ```js
    <App />
    ```



## ▶ 22. Module Systems Overview

- 2종류의 Export Statements 존재
   - Default Export Statements
   - Named Export Statements

### 🔹 Import Statements (Behind the Scenes)

1. App이란 이름의 변수 선언
2. App.js에서 default export를 찾음
3. App 변수에 default export를 할당

    ```js
    // App.js
    function App() {
      return <h1>Hi</h1>
    }

    export default App
    ```

    ```js
    // index.js
    import App from './App'
    ```

### 🔹 `Default` Export Statements

- 한 파일은 오로지 하나의 default export만 있어야 함
- default export를 사용하는 두 가지 방식 존재

    ```js
    // App.js
    // default export 방법 1 - 선호!
    function App() {
      return <h1>Hi</h1>
    }

    export default App
    ```

    ```js
    // App.js
    // default export 방법 2
    export default function App() {
      return <h1>Hi</h1>
    }
    ```

- default export를 import 하는 경우, 변수명을 자유롭게 변경 가능

    ```js
    // index.js
    import MyApp from './App'
    ```

### 🔹 `Named` Export Statements

- 여러 개의 변수를 export할 때 사용
- 한 파일 안에 여러 개의 named export가 존재할 수 있음
- named export를 사용하는 두 가지 방식 존재

    ```js
    // App.js
    // named export 방법 1
    function App() {
      return <h1>Hi</h1>
    }

    export default App

    const message = 'hi';
    export { message }
    ```

    ```js
    // App.js
    // named export 방법 2
    export default function App() {
      return <h1>Hi</h1>
    }

    export const message = 'hi'
    ```

- named export를 import 하는 경우
  - 중괄호 `{}`와 함께 변수명을 적어야 함
  - import statement 한 줄에 default export와 named export를 모두 적어도 됨 
  - 절대 변수명을 변경 불가

    ```js
    // index.js
    import App, { message } from './App'
    ```

### 🔹 import 파일 경로

- `./`와 `../`은 우리가 직접 생성한 파일을 import할 때 사용
  - `./`: 현재 폴더
  - `../`: 상위 폴더

   ```js
   import App from './App';
   ```

- `./`와 `../`가 없는 경우, 패키지를 import함을 의미
  - create-react-app 설치하면 생성되는 node-modules 폴더 안에 있는 패키지
  
   ```js
   import React from 'react';
   import ReactDOM from 'react-dom/client';
   ```



## ▶ 23. Cheatsheet for JSX

- [Cheatsheet for JSX](https://jsx-notes.vercel.app/)