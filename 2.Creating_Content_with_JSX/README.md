# ✔ '02.Creating Content with JSX' 이론 정리



## ▶ 10.  Showing Basic Content

- `index.html`이랑 `index.js`만 남기고 다른 파일은 삭제해도 실행됨

### 🔹 `index.js`에 적어야 하는 코드 (5단계)

> 참고: [React.createElement, React.Component, ReactDOM.render의 동작 원리](https://medium.com/crossplatformkorea/react-%EB%A6%AC%EC%95%A1%ED%8A%B8%EB%A5%BC-%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90-02-react-createelement%EC%99%80-react-component-%EA%B7%B8%EB%A6%AC%EA%B3%A0-reactdom-render%EC%9D%98-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-41bf8c6d3764)

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

> 실습: https://codesandbox.io/embed/react-component-variable-silseub-xplu2c?fontsize=14&hidenavigation=1&theme=dark

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

> 실습: https://codesandbox.io/s/13-shorthand-js-expressions-silseub-xt7lbm?file=/src/App.js

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