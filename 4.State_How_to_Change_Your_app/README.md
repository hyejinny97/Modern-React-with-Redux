# ✔ '04.State:How to Change Your App' 이론 정리



## ▶ 40. App Overview

- 만들고자하는 Project
  - 'Add Animal' 버튼을 클릭할 때마다, 아래에 동물 아이콘들이 나타남
  - 각 동물 아이콘의 우측 하단에는 작은 하트 아이콘이 위치함
  - 동물 아이콘이나 하트를 클릭할 때마다, 하트 아이콘의 크기가 점차 커짐



## ▶ 41. Initial App Setup

> 자료: [002_-_animals](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/002_-_pdas)

- src 폴더에 `index.js`, `App.js`, `AnimalShow.js`을 만들어야 함
  - `index.js`: 화면에 App 컴포넌트를 보여줌
  - `App`: 3개의 `AnimalShow`를 나타내는 컴포넌트
  - `AnimalShow`: 동물 이미지와 하트 이미지를 나타내는 컴포넌트
- 컴포넌트 계층 구조
  - App 컴포넌트는 AnimalShow 컴포넌트의 `Parent`  
  - AnimalShow 컴포넌트는 App 컴포넌트의 `Children`  
  - AnimalShow 컴포넌트는 다른 AnimalShow 컴포넌트의 `Sibling`  
- 'Add Animal' 버튼을 클릭할 때마다, `AnimalShow` 컴포넌트가 type을 포함한 props (ex)`{type: cow}`)를 전달받아 동물 이미지가 나타나게 됨



## ▶ 42. Introducing the Event System

> 자료: [003_-_animals](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/003_-_pdas)

- 'Add Animal' 버튼을 클릭할 때마다 동물 아이콘들이 나타나게 하는 대신, 숫자들이 증가하는 실습을 먼저 해보려 함
- Event System: 사용자의 행동(클릭, 드래그 등)을 감지해서 반응함
- State System: UI데이터가 변경될 때마다 화면에서 content를 update함



## ▶ 43. Event in Detail

### 🔹 Event 사용하는 법

1. 어떤 event를 사용할지 결정
   
   - [React SyntheticEvent](https://ko.reactjs.org/docs/events.html?)에서 사용가능한 이벤트 종류를 확인할 수 있음
   - 이벤트 예) `onClick`, `onDrag`, `onMouseMove`
   - `onClick` 이벤트: 사용자가 어떤 것을 클릭했을 때 호출됨
   - `onChange` 이벤트: text input에 타이핑하거나, radio 버튼을 클릭하는 등 form 요소에 변화가 생길때 호출됨

2. event가 발생했을 때 실행할 함수 생성
   
   - 보통 이 함수를 event handler 또는 callback function이라고 부름

3. `handler` + (element) + `EventName`의 형태로 함수 이름을 지음
   
   - convention일 뿐, 필수가 아님
   - `on` + `EventName`의 형태로 짓기도 함
   - ex) handlerClick, handlerButtonClick, onClick 

4. 함수를 plain element의 prop으로 넘겨줌
   
   - plain element: html 태그와 닮은 요소

5. 정확한 event명을 사용해 함수를 넘겨줘야 함
   
   - 이벤트 예) `onClick`, `onDrag`

6. 함수 reference만 넣어주면 됨(호출할 필요는 없음)
   
   - 함수 뒤에 소괄호 `()` 안 붙여도 됨

   ```js
   function App() {
    const handleClick = () => {
      console.log('Button was clicked');
    }

    return (
      <div>
        <button onClick={handleClick}>
          Add Animal
        </button>
      </div>
    );
   }
   ```



## ▶ 44. Variations on Event Handlers

> 자료: [005_-_animals](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/005_-_pdas)

- 위 설명처럼 이벤트를 사용할 때, 함수 뒤에 소괄호 `()`를 붙이면 안됨
  - 위 예제에서 버튼은 미래에 클릭됐을 때 비로소 함수를 호출하게 됨
  - 만약 소괄호를 붙였다면, 즉시 함수가 호출되었을 것임
- 함수를 요소 안에 바로 적어도 기능상 차이가 없음
  
   ```js
   function App() {
    return (
      <div>
        <button onClick={() => console.log('Button was clicked')}>
          Add Animal
        </button>
      </div>
    );
   }
   ```



## ▶ 46. Introducing the State System

> 자료: [007_-_animals](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/007_-_pdas)

- State System: UI데이터가 변경될 때마다 화면에서 content를 update함 ⇒ `rerender`
- 아래 예제에서 버튼을 클릭하면 화면이 rerendering 되어 숫자가 1씩 증가하는 것을 확인할 수 있음
  
   ```js
   import { useState } from 'react';
 
   function App() {
     const [count, setCount] = useState(0);
 
     const handleClick = () => {
       setCount(count + 1);
     };
 
     return (
       <div>
         <button onClick={handleClick}>Add Animal</button>
         <div>Number of animals: {count}</div>
       </div>
     );
   }
   ```