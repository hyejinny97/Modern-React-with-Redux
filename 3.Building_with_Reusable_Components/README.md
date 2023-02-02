# ✔ '03.Building with Reusable Components' 이론 정리



## ▶ 24. Project Overview

- 만들고자하는 Project
  - 세 개의 PDA(Personal Digital Assistants) 카드
  - 각 카드에는 이미지, 타이틀, 설명이 포함되어 있음
- 세 개의 카드에 대한 코드를 모두 적기 보단, `ProjectCard` 컴포넌트를 하나 만들어서 재사용하는 것이 효율적임
- 결과적으로 src 폴더에 `index.js`, `App.js`, `ProfileCard.js`을 만들어야 함
  - `index.js`: 화면에 App 컴포넌트를 보여줌
  - `App`: 3개의 `ProfileCard`을 나타내는 컴포넌트
  - `ProfileCard`: 하나의 카드를 나타내는 컴포넌트
- 컴포넌트 계층 구조
  - App 컴포넌트는 ProfileCard 컴포넌트의 `Parent`  
  - ProfileCard 컴포넌트는 App 컴포넌트의 `Children`  
  - ProfileCard 컴포넌트는 다른 ProfileCard 컴포넌트의 `Sibling`  



## ▶ 25. Creating Core Components

> 자료: [002_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/002_-_pdas)

- src 폴더에 세 파일 `index.js`, `App.js`, `ProfileCard.js`을 만듦 
- `App.js`, `ProfileCard.js` 파일은 일단 틀만 구축



## ▶ 26. Introducing the Props System

### 🔹 Props System

- parent 컴포넌트로부터 child 컴포넌트로 data를 전달해주는 방식
- 이를 이용해 parent 컴포넌트는 각 child 컴포넌트를 customize할 수 있음
- props 데이터는 부모에서 자식으로 한 방향으로만 흐름

### 🔹 Props를 이용한 데이터 전달 과정

1. parent 컴포넌트의 JSX 요소에 속성을 추가한다
2. 한 요소에 추가한 모든 속성들을 모아 object 데이터 타입에 담는다 - `{color: 'red'}` 
3. child 컴포넌트의 첫 번째 인자에 props object가 들어간다
4. child 컴포넌트에서 props를 사용해 데이터를 얻는다
   
   ```js
   // Parent 컴포넌트
   function App() {
      return <Child color="red" />
   }
   ```

   ```js
   // Child 컴포넌트
   function Child(props) {
      return <div>{props.color}</div>
   }
   ```



## ▶ 27. Picturing the Movement of Data / 28. Adding Props

> 자료: [005_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/005_-_pdas)

- 세 개의 `ProfileCard`에 각각 서로 다른 데이터를 담은 props를 내려보내줌 

   ```js
   // Parent 컴포넌트
   function App() {
      return (
        <div>
          <ProfileCard title="Alexa" handle="@alexa99"/>
          <ProfileCard title="Cortana" handle="@cortana32"/>
          <ProfileCard title="Siri" handle="@siri01"/>
        </div>
      );
   }
   ```

   ```js
   // Child 컴포넌트
   function ProfileCard(props) {
      return (
        <div>
          <div>Title is {props.title}</div>
          <div>Handle is {props.handle}</div>
        </div>
      );
   }
   ```



## ▶ 29. Using Argument Destructuring

> 참고: [mdn 구조 분해 할당(Destructuring Assignment)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)<br>
> 참고: [모던 자바스크립트 튜토리얼 - 구조 분해 할당](https://ko.javascript.info/destructuring-assignment)<br>
> 자료: [006_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/006_-_pdas)

- 기존 코드

   ```js
   function ProfileCard(props) {
      return (
        <div>
          <div>Title is {props.title}</div>
          <div>Handle is {props.handle}</div>
        </div>
      );
   }
   ```

- Argument Destructuring 적용한 코드

   ```js
   function ProfileCard({ title, handle }) {
      return (
        <div>
          <div>Title is {title}</div>
          <div>Handle is {handle}</div>
        </div>
      );
   }
   ```