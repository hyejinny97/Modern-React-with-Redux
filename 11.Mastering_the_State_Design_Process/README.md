# ✔ '11. Mastering the State Design Process' 이론 정리

## ▶ 171. Project Organization

- 흔히 React Component는 'Component'와 'Page' 둘 다 의미함

1. Component
  - 재사용 가능한 React Component
  - ex) Button, ItemShow, ProductList, Dropdown, SearchBar, Checkbox
  
2. Page
  - React Component, 재사용하진 않음
  - 다양한 component를 포함하고 있음
  - ex) CheckoutPage, ProductPage, LoginPage, LandingPage, ReactPage, CartPage

### 🔹 Project Organization

1. `Feature`에 따라 그룹화
  - 단 Feature에 따라 분류할 경우, 여러 feature에서 공통으로 사용되는 component는 어디에 넣는게 좋을지 애매할 수 있음
  
  ```
  src
    ↳ auth
        ↳ LoginPage.js
        ↳ LoginPage.css
        ↳ LoginForm.js
        ↳ SignupForm.js
        ↳ SignupForm.css
    ↳ cart
        ↳ CartPage.js
        ↳ CartPage.css
        ↳ CartItem.js
        ↳ CheckoutButton.js
        ↳ CheckoutButton.css
  ```

2. `Type`에 따라 그룹화
  - 단 Type에 따라 분류할 경우, components 폴더 안에 매우 많은 파일이 들어가 구분하기 어려울 수 있음
  
  ```
  src
    ↳ components
        ↳ Button.js
        ↳ SearchBar.js
        ↳ Dropdown.js
        ↳ Input.js
    ↳ pages
        ↳ LoginPage.js
        ↳ CartPage.js
        ↳ ProductPage.js
  ```

3. `Feature`와 `Type`에 따라 그룹화
  
  ```
  src
    ↳ components
        ↳ forms
            ↳ Input.js
            ↳ SearchBar.js
        ↳ products
            ↳ ProductShow.js
            ↳ ProductList.js     
    ↳ pages
        ↳ LoginPage.js
        ↳ CartPage.js
        ↳ ProductPage.js
  ```



## ▶ 172. Refactoring with Organization

> 자료: [002_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/002_-_org)

- src 폴더 안에 components와 pages 폴더를 생성해서, components 폴더엔 Button.js 파일을 옯겨오고 pages 폴더엔 ButtonPage.js 파일을 하나 만들자
  - VS code editor에 의해 자동으로 import 파일 경로를 바꿀 수 있음
- App.js 파일 내 있는 코드를 그대로 복사해 ButtonPage.js 파일에 붙여놓고 component명을 ButtonPage로 바꿔주자
  
  ```js
  // pages/ButtonPage.js

  import { GoBell, GoCloudDownload, GoDatabase } from 'react-icons/go';
  import Button from '../components/Button';

  function ButtonPage() {
    ...
  }

  export default ButtonPage;
  ```



## ▶ 173. Component Overview

- Accordion을 만들어 보자
- Accordion 내 각각의 section을 하드코딩하는 대신, 재사용 가능한 component로 만들어 보자
- Accordion component는 props로 각 section에 대한 내용을 담은 object를 list에 모아 전달받을 것임
  - section의 헤더 부분은 label 또는 heading, section의 바디 부분은 content라고 부를 예정
  - props ⇒ `{ items: [{label: h1, content: b1}, {label: h2, content: b2}] }`



## ▶ 174. Component Setup

> 자료: [004_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/004_-_org)

- components 폴더에 Accordion.js 파일 생성
- App component에서 Accordion component로 items prop 전달

  ```js
  import Accordion from './components/Accordion';

  function App() {
    const items = [
      {
        label: 'Can I use React on a project?',
        content: ...
      },
      {
        label: 'Can I use Javascript on a project?',
        content: ...
      },
      {
        label: 'Can I use CSS on a project?',
        content: ...
      },
    ];

    return <Accordion items={items} />;
  }
  ```

- Accordion component가 items prop을 받음
  
  ```js
  function Accordion({ items }) {
    return <div />;
  }
  ```



## ▶ 175. Reminder on Building Lists

> 자료: [005_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/005_-_org)

- Accordion component가 items prop을 받아 각 item의 label과 content를 나타냄

  ```js
  function Accordion({ items }) {
    const renderedItems = items.map((item) => {
      return (
        <div key={item.id}>
          <div>{item.label}</div>
          <div>{item.content}</div>
        </div>
      );
    });

    return <div>{renderedItems}</div>;
  }
  ```

- 각 item에 key 속성을 넣어야 하므로, items 데이터를 외부 데이터베이스테서 받아왔다고 가정하고 item마다 무작위로 id값 넣기
  
  ```js
  function App() {
    const items = [
      {
        id: 'l2kj5',
        ...
      },
      {
        id: 'lk2j35lkj',
        ...
      },
      {
        id: 'l1kj2i0g',
        ...
      },
    ];

    return <Accordion items={items} />;
  }
  ```



## ▶ 177. State Design Process Overview

- 아래에서 Accordion component에 대한 state design process를 총 8단계로 그려나감
  - 1 ~ 3단계: 어떤 state, event handler가 있어야 할까?
  - 4 ~ 7단계: state는 어떤 이름과 type을 가지는게 좋을까?
  - 8단계: state와 event handler는 어느 component에 두는 것이 좋을까?

- state의 type은 number, boolean, string, array, object 등 모두 가능함
  - 하지만, 이상적으로 state의 type은 array나 object는 피하는게 좋음

### 🔹 Events + State Design Process

1. 사용자가 어떤 반응(클릭, 드래그 등)을 하면, 어떤 변화가 일어나는지 적어보자

   ```
   세 번째 section을 클릭한다.
   첫 번째 section이 collapse 된다.
   세 번째 section이 expand 된다.
   두 번째 section을 클릭한다.
   새 번째 section이 collapse 된다.
   두 번째 section이 expand 된다.
   ```

2. 각 step을 'state'나 'event handler'로 카테고리화 해보자
   - state: 화면에서 변하는 것
   - event handler: 사용자의 행동

   ```
   세 번째 section을 클릭한다.       👉 event handler
   첫 번째 section이 collapse 된다.  👉 state
   세 번째 section이 expand 된다.    👉 state
   두 번째 section을 클릭한다.       👉 event handler
   세 번째 section이 collapse 된다.  👉 state
   두 번째 section이 expand 된다.    👉 state
   ```

3. 1__공통 step들을 묶고, 2__중복된 것을 제거하고, 3__재정의 해보자

   ```
   1) 공통 step들을 묶자
   세 번째 section을 클릭한다.       👉 event handler
   두 번째 section을 클릭한다.       👉 event handler

   첫 번째 section이 collapse 된다.  👉 state
   세 번째 section이 expand 된다.    👉 state  
   세 번째 section이 collapse 된다.  👉 state
   두 번째 section이 expand 된다.    👉 state 
   ```

   ```
   2) 중복된 것을 제거하자
   세 번째 section을 클릭한다.       👉 event handler

   첫 번째 section이 collapse 된다.  👉 state
   ```

   ```
   3) 재정의 해보자
   section header를 클릭한다.       👉 event handler

   한 section은 expand되고,         👉 state
   다른 section들은 collapse 된다.  
   ```

4. mockup을 보면서 변하지 않는 것들은 제거해보자

5. 나머지 요소들을 간단한 text로 대체해보자

   ```
   Expended
   Collapsed
   Collapsed
   ```

6. 변화된 상황이라 가정하고, 4번과 5번 과정을 반복하자
  
   ```
   Collapsed
   Collapsed
   Expended
   ```

7. 5번과 6번의 step들의 text를 반환하는 함수를 만든다고 생각해보자.이 함수에  component의 props과 다른 인자들을 넣어야 한다면 어떤 것을 넣는 것이 좋을까?
   - 넣어야하는 인자의 이름과 type을 state의 이름과 type으로 결정하면 됨
   - 따라서, Accordion component의 state로 number type의 expandedIndex를 생성하면 됨

   ```js
   // Accordion의 props
   const items = [
     {
       id: 'l2kj5',
       label: 'Can I use React on a project?',
       content: ...
     },
     {
       id: 'lk2j35lkj',
       label: 'Can I use Javascript on a project?',
       content: ...
     },
     {
       id: 'l1kj2i0g',
       label: 'Can I use CSS on a project?',
       content: ...
     },
   ];
   ```

   ```js
   function myFunction(items, /* ?? */) {};

   myFunction(propsItems, /* ?? */);  // ['Expanded', 'Collapsed', 'Collapsed']
   myFunction(propsItems, /* ?? */);  // ['Collapsed', 'Collapsed', 'Expanded']
   ```

   ```js
   function myFunction(items, expandedIndex) {
     return items.map((item, index) => {
        if (index === expandedIndex) {
          return 'Expanded'
        } else {
          return 'Collapsed'
        }
     });
   };

   myFunction(propsItems, 0);  // ['Expanded', 'Collapsed', 'Collapsed']
   myFunction(propsItems, 2);  // ['Collapsed', 'Collapsed', 'Expanded']
   ```

8. 각각의 state와 event handler는 어디에 위치하는 것이 좋을지 결정해보자
   - 현재 우리 project에서 expandedIndex state는 Accordion component 이외의 다른 component에서는 필요하지 않으므로 Accordion에 정의하는 것이 좋을 것 같음
   - handleClick event handler는 expandedIndex state가 있는 Accordion component에 정의하는 것이 좋을 것 같음
   
   ```
   🧿 'state'는 parent와 child component 중 어디에 위치하는 것이 좋을까?
     👉 child 이외의 다른 component에서 state가 필요한 경우,'parent'에 state를 정의하자
     👉 child 이외의 다른 component에서 state가 필요하지 않은 경우, 'child'에 state를 정의하자
   ```

   ```
   🧿 'event handler'는 parent와 child component 중 어디에 위치하는 것이 좋을까?
     👉 event handler는 보통 state가 정의된 component에 정의한다
     👉 정의된 위치와는 달리 다른 component에서 사용되곤 한다
   ``` 



## ▶ 178. Finding the Expanded Item

> 자료: [007_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/007_-_org)

- 실제 Accordion component에 expandedIndex state를 만들어보자
  
  ```js
  function Accordion({ items }) {
    const [expandedIndex, setExpandedIndex] = useState(0);

    const renderedItems = items.map((item, index) => {
      const isExpanded = index === expandedIndex;

      return (
        <div key={item.id}>
          <div>{item.label}</div>
          <div>{item.content}</div>
        </div>
      );
    });

    return <div>{renderedItems}</div>;
  }
  ```

- `isExpanded = true`일 때만 content가 보이게 하려면 어떻게 해야할까?
  - CSS를 이용해서 content를 보여주는 div 요소를 안보이게끔 하는 것이 아니라 아예 div 요소가 반환되지 않도록 하고자 함
  - 조건부 렌더링을 적용해서 구현해보자



## ▶ 179. Conditional Rendering

> 자료: [008_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/008_-_org)

1. React는 booleans, nulls, undefined를 화면에 나타내지 않음

2. JS Boolean Expressions
   - `||` (or 연산자): 첫 번째 truthy value를 반환함
   - `&&` (and 연산자): 첫 번째 falsey value나 마지막 truthy value를 반환함
  
   ```js
    'hi' || 'there'   // 'hi'
   false || 'there'   // 'there'
       0 || true      // true
      50 || null      // 50
     100 || 200       // 100
   ```

   ```js
    'hi' && 'there'   // 'there'
   false && 'there'   // false
       0 && true      // 0
      50 && null      // null
     100 && 200       // 200
   ```

- 위 React와 JS의 규칙에 따라 Conditional Rendering를 이해할 수 있음
  - isExpanded = true인 경우, 뒤의 div 요소가 출력됨
  - isExpanded = false인 경우, false가 반환되고 React는 boolean을 화면에 나타내지 않음

  ```js
  function Accordion({ items }) {
    ...
    const renderedItems = items.map((item, index) => {
      ...
      return (
        <div key={item.id}>
          <div>{item.label}</div>
          {isExpanded && <div>{item.content}</div>}
        </div>
      );
    });

    return <div>{renderedItems}</div>;
  }
  ```



## ▶ 180. Inline Event Handlers

> 자료: [009_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/009_-_org)

1. Event Handler (Longhand version)
   - 보통 코드가 한 줄 이상일 때 사용
   - list of elements에서는 약간 사용하기 힘들다는 단점이 있음

   ```js
   function ProductShow() {
     const handleClick = () => {
       console.log('hi there')
     };
 
     return <div onClick={handleClick}></div>
   }
   ```

2. Event Handler (Shorthand version)
   - 보통 코드가 한 줄일 때 사용
   - JSX를 읽기 어렵게 하는 단점이 있음

   ```js
   function ProductShow() {
     return <div onClick={() => console.log('hi there')}></div>
   }
   ```

- Inline Event Handler를 이용해서 accordion의 각 section header에 이벤트를 추가해보자
  - 아래처럼 Inline Event Handler를 사용하면, items 갯수만큼 handler 함수가 생성됨
  - `() => setExpandedIndex(0)`, `() => setExpandedIndex(1)`, `() => setExpandedIndex(2)`
  
  ```js
  function Accordion({ items }) {
    ...
    const renderedItems = items.map((item, index) => {
      ...
      return (
        <div key={item.id}>
          <div onClick={() => setExpandedIndex(index)}>{item.label}</div>
          {isExpanded && <div>{item.content}</div>}
        </div>
      );
    });

    return <div>{renderedItems}</div>;
  }  
  ```



## ▶ 181. Variation on Event Handlers

> 자료: [010_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/010_-_org)

- Event handler를 map() 함수 밖에 따로 정의해서 사용하려면 어떻게 해야할까?
- map() 함수 내에서만 사용되는 index 변수를 map() 함수 밖으로 보내려면, `onClick={handleClick}`이 아닌 `onClick={() => handleClick(index)}`을 통해 가능함

  ```js
  function Accordion({ items }) {
    ...
    const handleClick = (nextIndex) => {
      setExpandedIndex(nextIndex);
    };

    const renderedItems = items.map((item, index) => {
      ...
      return (
        <div key={item.id}>
          <div onClick={() => handleClick(index)}>{item.label}</div>
          {isExpanded && <div>{item.content}</div>}
        </div>
      );
    });

    return <div>{renderedItems}</div>;
  }
  ```



## ▶ 182. Conditional Icon Rendering

> 자료: [011_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/011_-_org)

- expanded section에는 아래쪽 방향 아이콘이어야 하고, collapsed section에는 왼쪽 방향 아이콘이어야 함

  ```js
  function Accordion({ items }) {
    ...
    const renderedItems = items.map((item, index) => {
      ...
      const icon = <span>{isExpanded ? 'DOWN' : 'LEFT'}</span>;

      return (
        <div key={item.id}>
          <div onClick={() => handleClick(index)}>
            {icon}
            {item.label}
          </div>
          {isExpanded && <div>{item.content}</div>}
        </div>
      );
    });

    return <div>{renderedItems}</div>;
  }
  ```



## ▶ 183. Displaying Icons

> 자료: [012_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/012_-_org)

- `react-icons` 라이브러리를 이용해 아이콘을 넣어보자

  ```js
  import { GoChevronDown, GoChevronLeft } from 'react-icons/go';

  function Accordion({ items }) {
    ...
    const renderedItems = items.map((item, index) => {
      ...
      const icon = (
        <span>{isExpanded ? <GoChevronDown /> : <GoChevronLeft />}</span>
      );
      ...
    });

    return <div>{renderedItems}</div>;
  }
  ```



## ▶ 184. Adding Styling

> 자료: [013_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/013_-_org)

- `TailwindCSS`를 이용해서 스타일링해보자
  
  ```js
  function Accordion({ items }) {
    ...
    const renderedItems = items.map((item, index) => {
      ...
      const icon = (
        <span className="text-2xl">
          {isExpanded ? <GoChevronDown /> : <GoChevronLeft />}
        </span>
      );

      return (
        <div key={item.id}>
          <div
            className="flex justify-between p-3 bg-gray-50 border-b items-center cursor-pointer"
            onClick={() => handleClick(index)}
          >
            {item.label}
            {icon}
          </div>
          {isExpanded && <div className="border-b p-5">{item.content}</div>}
        </div>
      );
    });

    return <div className="border-x border-t rounded">{renderedItems}</div>;
  }
  ```



## ▶ 185. Toggling Panel Collapse

> 자료: [014_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/014_-_org)

- Accordion component가 처음 render될 때 모든 section이 collapse된 상태로 보여지기 위해 expandedIndex state의 default 값을 -1로 바꿈
- 현재 이미 expand되어 있는 section을 클릭했을 때, 그 section은 collapse 되어야 함

  ```js
  function Accordion({ items }) {
    const [expandedIndex, setExpandedIndex] = useState(-1);

    const handleClick = (nextIndex) => {
      if (expandedIndex === nextIndex) {
        setExpandedIndex(-1);
      } else {
        setExpandedIndex(nextIndex);
      }
    };
    ...
  }  
  ```



## ▶ 187. [Optional] Delayed State Updates

- 브라우저 콘솔창을 통해 현재 expand된 0번 인덱스 section을 두 번 빠르게 클릭하자
  
  ```js
  $0.click(); $0.click();
  ```

- 예상: expandedIndex = 0 → 클릭 → setExpandedIndex(-1) → expandedIndex = -1 → 클릭 → setExpandedIndex(0) → expandedIndex = 0
  - 하지만, 우리가 원하는대로 0번 인덱스 section이 collapse, expand되어 결과적으로 expand된 상태로 나타나지 않을 것임
- 실제: expandedIndex = 0 → 클릭 → setExpandedIndex(-1) 호출을 지연 → 클릭 → setExpandedIndex(-1) 호출을 지연 → 시간이 흐른 후 처리 → expandedIndex = -1
  - 즉, React가 첫 번째 클릭 후 실행되어야하는 setExpandedIndex(-1)을 바로 실행하지 않기 때문임
  - 따라서 여전히 expandedIndex = 0인 상태에서 두 번째 클릭이 진행되어 setExpandedIndex(-1)이 호출됨



## ▶ 188. [Optional] Functional State Updates

> 자료: [016_-_org](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/11.Mastering_the_State_Design_Process/016_-_org)

- React가 state update를 delay하는 이유
  - state를 여러 번/여러 개 update 해야하는 경우, 기다렸다가 모든 state를 한 번에 바꿔주기 위함
  - 이를 통해 실행 속도가 빨라짐
- 위처럼 React가 state update를 delay하는 현상을 해결하려면 어떻게 해야할까?
  - 방법1) React가 state update를 즉시 진행하도록 설정하자 (이상적인 방법 x)
  - 방법2) handleClick event handler에서 최신 상태의 expandedIndex state에 접근하자
  
### 🔹 State Updates

1. Simple version
   - 새로운 state value가 이전 state value에 의존하지 않는 경우 사용
  
   ```js
   const [counter, setCounter] = useState(0);

   const handleClick = () => {
      setCounter(10);
   };
   ```

2. Functional version
   - 새로운 state value가 이전 state value에 의존하는 경우 사용
   - setter function 안에 callback 함수를 넣으면, callback 함수의 인자로 최신 상태의 state value가 들어가게 됨

   ```js
   const [counter, setCounter] = useState(0);

   const handleClick = () => {
      setCounter(currentCounter => {
        if (currentCounter > 10) {
          return 20;
        } else {
          return currentCounter + 1;
        }
      });
   };
   ```

- Accordion component에 Functional version handleClick()을 적용해서 버그를 해결해보자

  ```js
  function Accordion({ items }) {
    const [expandedIndex, setExpandedIndex] = useState(-1);

    const handleClick = (nextIndex) => {
      setExpandedIndex((currentExpandedIndex) => {
        if (currentExpandedIndex === nextIndex) {
          return -1;
        } else {
          return nextIndex;
        }
      });
    };
    ...
  } 
  ```