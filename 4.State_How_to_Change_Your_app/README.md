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



## ▶ 47. More on State

- State: 사용자의 반응(클릭, 드래그 등)에 따라 변하는 데이터
- 데이터(state)가 변할 때, react는 자동으로 화면을 update하게 됨
  - 심지어 react 이외의 다른 UI 라이브러리도 state system을 사용함

### 🔹 useState 함수

```js
const [count, setCount] = useState(0);
```

- `count`: state ⇒ 변하는 데이터
- `setCount`: setter function ⇒ state를 update 해줌
- `[count, setCount]`: array destructuring

### 🔹 State system 사용하는 법

1. 화면에서 특정 content를 변화시키고 싶을 때, state system을 사용한다.

2. `useState` 함수를 이용해 state를 정의한다.

   - 먼저, react 모듈로부터 useState를 import 해야함

3. `useState` 함수의 인자로 state의 default(초기값)을 넣어준다.
   
   - number, string, array, object 데이터 타입 모두 가능

4. JSX에서 중괄호 `{}` 안에 state를 넣어 사용한다.

5. 사용자가 반응하면, setter function을 사용해 state를 update한다.
   
6. setter function이 호출되면, react는 자동으로 component를 rerender해주고 update된 state를 화면에 다시 보여준다.



## ▶ 48. Understanding the Re-Rendering Process

- component는 state가 존재하면 여러번 rerender될 수 있음
- 사용자 반응 → setter function 호출 → component가 rerender됨 → update된 state를 화면에 보여주는 과정을 반복하게 됨



## ▶ 48. Understanding the Re-Rendering Process

> 참고: [Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

- `useState()` 함수는 배열 형태를 반환
  - 첫번째 인자: state
  - 두번째 인자: setter function

   ```js
   import { useState } from 'react';

   export default function App() {
     console.log(useState(10));   // (2) [10, ƒ bound dispatchSetState()]
     // 생략
   }
   ``` 

- Array Destructure을 통해 useState() 함수의 첫번째와 두번째 인자를 서로 다른 변수에 쉽게 할당할 수 있음



## ▶ 50. Back to the App

- 처음 App을 계획했을 때처럼, 'Add Animal' 버튼을 누르면 동물이 랜덤하게 나타나는 것을 구현하고자 함
- App component에 배열 형태의 'animal' state를 만들고, 버튼을 누를 때마다 'cow', 'bird'와 같은 동물 타입을 넣음
  - ex) animal = ['cow', 'bird']
- 'animal' state의 배열 내 요소 개수만큼 AnimalShow component가 생성
- AnimalShow component의 props로 animal type을 전달
  - ex) `{ type: 'cow' }`
- AnimalShow component는 props로부터 전달받은 type에 따라 해당 동물 이미지를 반환



## ▶ 51. Picking a Random Element

> 자료: [014_-_animals](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/014_-_pdas)

- 랜덤하게 동물을 선택하기 위해 `getRandomAnimal()` 함수를 만듦
  - 'bird', 'cat', 'cow', 'dog', 'gator', 'horse' 중 하나를 반환함

   ```js
   function getRandomAnimal() {
     const animals = ['bird', 'cat', 'cow', 'dog', 'gator', 'horse'];
 
     return animals[Math.floor(Math.random() * animals.length)];
   }
   ```

- `useState()` 함수를 사용해 'animal' state 생성
- `setAnimal()` 함수를 통해 버튼을 클릭할 때마다, 랜덤 동물을 'animal' state에 추가해줌

   ```js
   function App() {
     const [animals, setAnimals] = useState([]);
 
     const handleClick = () => {
       setAnimals([...animals, getRandomAnimal()]);
     };
 
     return (
       <div>
         <button onClick={handleClick}>Add Animal</button>
         <div>{animals}</div>
       </div>
     );
   }
   ```



## ▶ 52. List Building in React

> 자료: [015_-_animals](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/015_-_pdas)

- 'animal' state에 있는 각 동물들을 AnimalShow component의 props로 전달하기 위해서 built-in function인 `map()`를 사용
  - `map()` 함수의 첫번째 인자로는 array의 각 요소가 차례대로 들어오고, 두번째 인자로는 index가 0부터 순서대로 들어옴
  - array의 각 요소를 `map()` 함수 내 callback 함수의 인자로 넘겨준 후 반환된 값들을 **배열 형태로 반환**
  
   ```js
   const renderedAnimals = animals.map((animal, index) => {
     return <AnimalShow type={animal} key={index} />;
   });
   ```

- AnimalShow component를 차례로 담은 배열 변수를 return JSX에 사용
  
   ```js
   function App() {
     // 생략
     return (
       <div>
         <button onClick={handleClick}>Add Animal</button>
         <div>{renderedAnimals}</div>
       </div>
     );
   }
   ```