# ✔ '10.Custom Navigation and Routing Systems' 이론 정리

## ▶ 152. Project Overview

- 만들고자하는 Project
  - 화면 왼쪽에 세로로 된 NabBar가 있고, NabBar엔 Dropdown/Accordion/Button/Flex/Tables/Search components가 있음
  - NavBar의 각 components를 클릭하면, 클릭한 컴포넌트를 보여주는 새로운 페이지로 이동하게 됨
- 이번 Project의 목표
  - state, event 등 연습
  - component 설계 연습
  - Navigation(NabBar에 컴포넌트 클릭 시 새로운 페이지 이동)
  - styling에 대한 다른 접근법
  - components간 많은 정보의 공유를 요하지 않는 큰 App 만들기

## ▶ 154. Some Button Theory

- 하나의 App 내 여러 페이지에서 사용되는 button을 통일성있게(persistence) 유지하기 위한 방법
  
  - 페이지마다 `<button />` 요소를 새로 만들지 말고, Button component를 하나 만들어서 재사용하자
  - 페이지마다 직접 button을 스타일링하지 말고, 프로젝트 초기에 button의 목적/의도에 맞게 스타일링한 후 재사용하자

- button 목적에 따른 이름 지정 및 background-color 스타일링 예

  | Button Purpose                | Short Name | Color  |
  | ----------------------------- | ---------- | ------ |
  | good action을 유도할 때            | Primary    | Blue   |
  | kind of good action을 유도할 때    | Secondary  | Black  |
  | something good한 상황이 방금 일어났을 때 | Success    | Green  |
  | 경고해야할 때                       | Warning    | Yellow |
  | 위험한 행동 임을 알려야할 때              | Danger     | Red    |

- 다양한 button 모양 예
  - Standard: 직사각형 모양 버튼 + full background
  - Pill/Rounded: 알약 모양 버튼 + full background
  - Outline: 직사각형 모양 버튼 + border만 색칠
  - Outline and Rounded: 알약 모양 버튼 + border만 색칠



## ▶ 155. Underlying Elements

> 자료: [004_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/004_-_comps)

- 서로 다른 모양의 button마다 component를 만드는 것은 비효율적
- 대신, 하나의 Button component를 만들고 전달받은 props에 따라 스타일에 variation을 주는 것이 좋을 듯함
  - Outline 여부, Rounded 여부, 색깔
  - ex) props ⇒ { outline: true, rounded: true, primary: true }
  
- Button component를 만든 후, underlying element 넣기
  - 이 component에서 underlying element는 `<button></button>` 태그
  
  ```js
  function Button() {
    return <button>Hi there!</button>;
  }

  export default Button;
  ```

- App component에서 Button component를 import
  
  ```js
  import Button from './Button';

  function App() {
    return (
      <div>
        <div>
          <Button></Button>
        </div>
        <div>
          <Button></Button>
        </div>
        <div>
          <Button></Button>
        </div>
        <div>
          <Button></Button>
        </div>
        <div>
          <Button></Button>
        </div>
      </div>
    );
  }
  ```



## ▶ 156. The Children Prop

> 자료: [005_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/005_-_comps)

- component 태그 사이에 있는 자식 요소(plain text, 다른 component 등)는 자동으로 해당 component의 props으로 들어감
  - 이때, props 내 이 요소의 key값은 `children`이 됨
  - props ⇒ `{ children: 자식 요소 }`
- 따라서, button마다 특정 text를 넣고 싶다면 Button component의 children prop을 이용해보자
  
  ```js
  function Button({ children }) {
    return <button>{children}</button>;
  } 
  ```

  ```js
  function App() {
    return (
      <div>
        <div>
          <Button>Click me!!</Button>
        </div>
        <div>
          <Button>Buy Now!</Button>
        </div>
        <div>
          <Button>See Deal!</Button>
        </div>
        <div>
          <Button>Hide Ads!</Button>
        </div>
        <div>
          <Button>Something!</Button>
        </div>
      </div>
    );
  }
  ```



## ▶ 157. Props Design

> 자료: [006_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/006_-_comps)

- props를 통해 rounded 여부, outline 여부, 버튼 purpose(또는 variation)를 boolean 데이터 타입으로 받아보자
  - rounded, outline는 prop의 key로 그대로 작성
  - 버튼 purpose는 primary/secondary/success/warning/danger를 prop의 key로 작성
  - ex) props ⇒ { outline: true, rounded: true, primary: true }
- JSX에서 props의 값이 boolean일 때
  - `true`인 경우: 값은 생략하고 속성명만 적어도 됨 - ex) <input spellCheck />
  - `false`인 경우: 값을 중괄호 `{}`로 감싸서 적어야 하지만, 속성명 자체를 적지 않으면 어차피 undefined로 취급됨

  ```js
  function App() {
    return (
      <div>
        <div>
          <Button success rounded outline>
            Click me!!
          </Button>
        </div>
        <div>
          <Button danger outline>
            Buy Now!
          </Button>
        </div>
        <div>
          <Button warning>See Deal!</Button>
        </div>
        <div>
          <Button secondary outline>
            Hide Ads!
          </Button>
        </div>
        <div>
          <Button secondary rounded>
            Something!
          </Button>
        </div>
      </div>
    );
  }
  ```



## ▶ 158. Validating Props with PropTypes

- 일단, Button component가 props을 받음

  ```js
  function Button({ 
    children,
    primary,
    secondary,
    success,
    warning,
    danger,
    outline,
    rounded
  }) {
    return <button>{children}</button>;
  }
  ```

- Button component의 prop으로 purpose(primary/secondary/success/warning/danger)가 반드시 1개만 들어와야 함
  - 하지만 만약, 2개 이상 들어오면 어떻게 처리할 수 있을까?
  - `prop-types` 라이브러리를 활용하면 쉽게 validation을 할 수 있음

### 🔹 prop-types

- component로 들어오는 props를 validation해주는 JS 라이브러리 (optional)
- 만약 component에 원하지 않은 props type이 들어오면, 콘솔창에 경고를 띄워줌
- 과거에 매우 인기있던 라이브러리었으나, 현재는 Typescript가 대신하고 있음
- [prop-types 관련 문서](https://www.npmjs.com/package/prop-types)
- `PropTypes`를 이용해서 prop의 type 체크 등 validation이 가능함
  - `isRequired`: component의 필수 prop으로 반드시 넣어줘야 함

  ```js
  import PropTypes from 'prop-types';

  function Card({ title, content, showImage }) {
    ...
  }

  Card.propTypes = {
    title: PropTypes.string.isRequired,
    content: PropTypes.string,
    showImage: PropTypes.bool
  };
  ```

- 아래와 같은 방식으로 직접 validation function을 만들어 prop을 확인할 수 있음
  - key 이름은 customProp 이외 자유롭게 지정 가능
  - function의 첫번째 인자로 props가 들어옴
  - Button component의 prop으로 purpose(primary/secondary/success/warning/danger)가 1개만 들어오는지 검증 가능
  
  ```js
  MyComponent.propTypes = {
    customProp: function(props, propName, componentName) {
        if (!/matchme/.test(props[propName])) {
          return new Error(
            'Invalid prop `' + propName + '` supplied to' +
            ' `' + componentName + '`. Validation failed.'
          );
        }
      }
  }
  ```

- prop-types 라이브러리 설치
  
  ```bash
  $ npm install prop-types
  ```



## ▶ 159. PropTypes in Action

> 자료: [008_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/008_-_comps)

- JS의 특징
  - !!true == true이고, Number(true) == 1
  - !!false == false이고, Number(false) == 0
  - !!undefined == false
  
- PropTypes을 이용해 validation function을 만들어 보자

  ```js
  import PropTypes from 'prop-types';

  function Button(...) {
    // 생략
  }

  Button.propTypes = {
    checkVariationValue: ({ primary, secondary, success, warning, danger }) => {
      const count =
        Number(!!primary) +
        Number(!!secondary) +
        Number(!!warning) +
        Number(!!success) +
        Number(!!danger);

      if (count > 1) {
        return new Error(
          'Only one of primary, secondary, success, warning, danger can be true'
        );
      }
    },
  };
  ```



## ▶ 160. Introducing TailwindCSS

- Styling을 하기 위해서 직접 CSS를 작성하거나 CSS framework/library를 사용할 수 있음
  - CSS framework/library: Bulma, Bootstrap, Ant 등 매우 많음
  - Bulma 같은 경우, 하나의 class로 여러 개의 CSS rules을 적용할 수 있음

### 🔹 TailwindCSS

- 매우 많은 class를 사용하여 styling할 수 있는 CSS library
- 각 class는 하나의 CSS rule을 가지고 있음
- 따라서, 한 요소에 여러 CSS rules를 적용하려면 매우 많은 class명을 기입해야 함 
- 그럼에도 불구하고 TailwindCSS 라이브러리를 사용하는 이유는 재사용 가능하고 작은 components를 만드는데 도움을 주기 때문임



## ▶ 161. Installing Tailwind

> 자료: [010_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/010_-_comps)

- [TailwindCSS with create-react-app 문서](https://tailwindcss.com/docs/guides/create-react-app)

### 🔹 Tailwind CSS with Create React App (v3.2.7) 설치 과정

1. Tailwind CSS 설치하자
   
   ```bash
   $ npm install -D tailwindcss
   $ npx tailwindcss init
   ```

2. `tailwind.config.js` 파일에 template paths를 추가하자

   ```js
   // tailwind.config.js

   module.exports = {
     content: [
       "./src/**/*.{js,jsx,ts,tsx}",
     ],
     theme: {
       extend: {},
     },
     plugins: [],
   }
   ```

3. `./src/index.css` 파일을 생성하고, `@tailwind` directives를 추가하자
   
   ```css
   /* index.css */

   @tailwind base;
   @tailwind components;
   @tailwind utilities;
   ```

4. `index.js` 파일에 `index.css` 파일을 불러오자
   
   ```js
   // index.js

   import './index.css';
   ```



## ▶ 162. How to use Tailwind

> 자료: [011_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/011_-_comps)

### 🔹 Tailwind CSS 사용하는법

1. 어떤 styling rule을 적용할지 결정하자

2. `tailwindcss.com/docs`로 이동하자

3. 검색창(ctrl + K)을 누르자

4. 찾고자 하는 styling rule을 검색하자

5. 요소에 적절한 className을 추가하자

  ```js
  function Button({...}) {
    return <button className="bg-blue-500">{children}</button>;
  }
  ```



## ▶ 163. Review on Styling

> 자료: [012_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/012_-_comps)

- Tailwind CSS를 이용해 standard primary button을 만들어보자
  - 고려해야할 style rule: padding, border width, border color, background color, text color

  ```js
  function Button({...}) {
    return (
      <button className="px-3 py-1.5 border border-blue-600 bg-blue-500 text-white">
        {children}
      </button>
    );
  }
  ```

- CSS Box Model
  - Content < Padding < Border < Margin



## ▶ 164. The ClassNames Library

> 자료: [013_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/013_-_comps)

- Button component로 들어온 props에 따라 서로 다른 style을 적용하려면 어떻게 해야할까?
  - `classnames` 라이브러리를 이용하면 상황에 따라 쉽게 className을 지정할 수 있음

### 🔹 classnames

- 서로 다른 값들을 하나의 'className' string으로 만들어주는 JS library
- 주의) `classnames` 은 라이브러리, `className`은 component의 prop
- [classnames 문서](https://www.npmjs.com/package/classnames)
- classnames() 함수의 인자로 string, object 등을 넣어줄 수 있음
  - classnames() 함수는 인자들 중 truthy value만 공백으로 구분해서 하나의 string으로 반환해줌
  - 인자로 들어간 strings은 모두 공백으로 구분해서 하나의 string으로 반환해줌
  - object의 경우, value가 truthy value(true, 0 이상 숫자 등)인 것만 key를 모아 하나의 string으로 반환해줌

  ```js
  import className from 'classnames';

  className('bg-blue-500', 'px-3', 'py-1.5')  // 'bg-blue-500 px-3 py-1.5'
  className(undefined, 'px-3', 'py-1.5')      // 'px-3 py-1.5'
  ``` 

  ```js
  import className from 'classnames';

  const primary = true;
  const warning = false;

  className({
    'bg-blue-500': primary,
    'bg-yellow-500': warning
  });  // 'bg-blue-500 px-3 py-1.5'
  ``` 



## ▶ 164. The ClassNames Library

> 자료: [014_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/014_-_comps)

- `classnames` 라이브러리를 사용해 Button component로 들어온 props에 따라 서로 다른 style을 적용해보자

  ```js
  import className from 'classnames';

  function Button({...}) {
    const classes = className('px-3 py-1.5 border', {
      'border-blue-500 bg-blue-500 text-white': primary,
      'border-gray-900 bg-gray-900 text-white': secondary,
      'border-green-500 bg-green-500 text-white': success,
      'border-yellow-400 bg-yellow-400 text-white': warning,
      'border-red-500 bg-red-500 text-white': danger,
    });

    return <button className={classes}>{children}</button>;
  }
  ```



## ▶ 166. Finalizing the Variations

> 자료: [015_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/015_-_comps)

- rounded와 outline props도 `classnames` 라이브러리를 사용해 style을 적용해보자
  - 동일한 style에 영향을 주는 class가 2개 이상인 경우, 뒤에 있는 class가 앞에 있는 class를 덮어쓰게 됨

  ```js
  import className from 'classnames';

  function Button({...}) {
    const classes = className('px-3 py-1.5 border', {
      ...
      'rounded-full': rounded,
      'bg-white': outline,
      'text-blue-500': outline && primary,
      'text-gray-900': outline && secondary,
      'text-green-500': outline && success,
      'text-yellow-400': outline && warning,
      'text-red-500': outline && danger,
    });

    return <button className={classes}>{children}</button>;
  }
  ```



## ▶ 167. Using Icons in React Projects

> 자료: [016_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/016_-_comps)

- `react-icons` 라이브러리를 사용해 버튼 안에 아이콘을 넣어보자

### 🔹 React Icons

- Bootstrap Icons, Font Awesome 등과 같은 icon sets를 모아놓은 곳임
- [react-icons 문서](https://react-icons.github.io/react-icons/)
- react-icons 라이브러리 설치 방법
  
  ```bash
  $ npm install react-icons
  ```

- react-icons 라이브러리 사용 방법
  - Bootstrap Icons, Font Awesome 등 원하는 icon sets를 선택한 후, 원하는 아이콘을 `import { IconName } from 'react-icons/fa'` 이런 식으로 import 해옴
  - 가져온 아이콘은 component처럼 `<IconName />` 이런 식으로 사용하면 됨

  ```js
  import { GoBell } from 'react-icons/go';

  function App() {
    return (
      <div>
        <div>
          <Button secondary outline rounded>
            <GoBell />
            Click me!!
          </Button>
        </div>
        ...
      </div>
    );
  }
  ```

- 버튼 내 아이콘과 텍스트를 한 행에 놓기 위해 style을 추가함

  ```js
  function Button({...}) {
    const classes = className('flex items-center px-3 py-1.5 border', {
      ...
    });

    return <button className={classes}>{children}</button>;
  }
  ```

- 버튼 내 아이콘과 텍스트 간격을 띄우기 위해 `index.css` 파일에 style을 추가함

  ```css
  /* index.css */
  
  button > svg {
    margin-right: 5px;
  }
  ```



## ▶ 168. Issues with Event Handlers

> 자료: [017_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/017_-_comps)

- props와 event handler를 이용해 button에 onClick 이벤트를 추가해보자

  ```js
  function App() {
    const handleClick = () => {
      console.log('Click!!');
    };

    return (
      <div>
        <div>
          <Button secondary outline rounded onClick={handleClick}>
            <GoBell />
            Click me!!
          </Button>
        </div>
        ...
      </div>
    );
  }
  ```

  ```js
  function Button({ ..., onClick }) {
    ...
    return (
      <button onClick={onClick} className={classes}>
        {children}
      </button>
    );
  }
  ```

- 만약 button에 mouseover, mouseenter 등 여러 개의 event handler를 더 추가해야 한다면?
  - 위 방법에서처럼 모든 event handler를 각각 prop으로 넘겨주는 것은 지저분해 보일 수 있음



## ▶ 169. Passing Props Through

> 자료: [018_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/018_-_comps)

- Button component에서 children, primary 등 variation, outline, rounded 외 다른 prop을 받고자할 때, `...rest`를 이용해서 객체 형태로 전부 받을 수 있음
  - `...rest`: `{ onClick: f, onMouseOver: f }`
  - `...rest`를 통해 받은 모든 event handler를 button 요소에 넣어주기 위해 아래와 같이 `{...rest}` 를 적어주면 됨

  ```js
  function Button({..., ...rest}) {
    ...
    return (
      <button {...rest} className={classes}>
        {children}
      </button>
    );
  }
  ```



## ▶ 170. Handling the Special ClassName Case

> 자료: [019_-_comps](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/10.Custom_Navigation_and_Routing_Systems/019_-_comps)

- 한 버튼 아래 margin style을 주고 싶어서 그 Button component의 props로 className을 추가하면 어떻게 될까?
  - 아래 코드처럼 Button component에 className만 추가하게 되면, style이 적용되지 않음
  - 이유: button 요소 내 `className={classes}`에 의해 `className="mb-5"`가 덮어 씌워지기 때문
  
  ```js
  function App() {
    ...
    return (
      <div>
        <div>
          <Button secondary outline rounded className="mb-5" onClick={handleClick}>
            <GoBell />
            Click me!!
          </Button>
        </div>
        ...
      </div>
    );
  }  
  ```

- 따라서, Button component에서 `...rest`로 들어온 className을 따로 classnames 라이브러리를 이용해 처리해 줘야함 

  ```js
  function Button({ ..., ...rest}) {
    const classes = className(rest.className, ...);

    return (
      <button {...rest} className={classes}>
        {children}
      </button>
    );
  }
  ```

- 이제, 우리가 지금까지 만든 Button component를 이제 plain HTML button 요소처럼 사용하면 됨