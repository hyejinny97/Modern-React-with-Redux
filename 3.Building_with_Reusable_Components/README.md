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



## ▶ 31. The React Developer Tools

- chrome extension 중 하나로 `React Developer Tools`을 깔고, 브라우저 화면에서 개발자 도구의 `component` 영역을 사용하면 UI가 어떤 컴포넌트로 구성되어 있는지 한눈에 알 수 있음



## ▶ 32. The Most Common Props Mistake

- 부모 컴포넌트와 자식 컴포넌트에서 props의 이름은 반드시 동일해야 함
- 오타가 나거나 동일하지 않은 이름을 넣으면 자식 컴포넌트에서 해당 props의 값을 찾지 못해 undefined를 반환하게 됨



## ▶ 34. Including Images

> 자료: [010_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/010_-_pdas)

- 리액트에서 js 파일을 import 할 때는 확장자를 따로 안적어도 됨
- 하지만, js 파일 이외의 image 파일, font 파일 등은 import 할 때 확장자를 반드시 적어야 함
  
  ```js
  import ProfileCard from './ProfileCard';      // JS 파일
  import AlexaImage from './images/alexa.png';  // Image 파일
  ```

- 9.7kb 이하의 이미지 파일은 base64 인코딩 포맷으로 전환되어 JS 파일에 문자열로 저장됨
- 9.7kb 이상의 이미지 파일은 하나의 분리된 파일로 저장됨

  ```js
  import AlexaImage from './images/alexa.png';
  import SiriImage from './images/siri.png';

  console.log(AlexaImage);   // 9.7kb 이하인 이미지
  console.log(SiriImage);    // 9.7kb 이상인 이미지
  ```

  ```
  data:image/png;base64,.....   // 9.7kb 이하인 이미지
  /state/media/siri......png    // 9.7kb 이상인 이미지
  ```

- `<img>` 태그의 `src` 속성에 import한 이미지 변수 또는 외부 URL 문자열을 넣어서 사용
  - 9.7kb 이하/이상 상관없이 동일한 방식으로 넣어주면 됨

  ```js
  function App() {
    return (
      <div>
        <img src={AlexaImage} />
        <img src={SiriImage} />
        <img src="https://picsum.photos/200/300" />  // 외부 URL
      </div>
    );
  }
  ```



## ▶ 35. Handling Image Accessibility

> 자료: [011_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/011_-_pdas)

- 속성값으로 expression이나 변수를 사용할 때 중괄호 `{}`를 사용하는 것처럼, 이미지 변수도 중괄호를 사용해 자식 컴포넌트에 데이터를 넘겨줄 수 있음



## ▶ 36. Review on how CSS Works

- 이 수업 실습은 CSS 라이브러리인 [Bulma](https://bulma.io/documentation/components/card/)를 사용해서 스타일링할 예정
- 스타일링하는 방법: 1) CSS Library 이용, 2) 직접 CSS 작성

1. CSS Library
   - 방법1) CSS 파일을 다운로드한 후, `public` 폴더에 추가하고 HTML 파일에 `<link>` 태그로 연결시킴
   - 방법2) CSS 파일을 다운로드한 후, `src` 폴더에 추가하고 `import` 함
   - 방법3) `CDN`을 이용해서 CSS 파일을 HTML 파일에 `<link>` 태그로 연결시킴
   - 방법4) `NPM`을 이용해서 CSS 라이브러리를 설치한 후, CSS 파일을 `import` 함

2. 직접 CSS 작성
   - 방법1) CSS 파일에 CSS 작성 후, `public` 폴더에 추가하고 HTML 파일에 `<link>` 태그로 연결시킴
   - 방법2) CSS 파일에 CSS 작성 후, `src` 폴더에 추가하고 `import` 함
   - 방법3) CSS-in-JS 라이브러리 설치 후, JS 파일에 CSS를 작성
   - 방법4) HTML 파일에 직접 CSS 작성
   - 방법5) SASS 작성하고 CRA가 SASS 파일을 처리하도록 설정한 후, SASS 파일에 `import` 함
   - 방법6) JSX 요소에 inline-style로 스타일 추가



## ▶ 37. Adding CSS Libraries with NPM

> 자료: [013_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/013_-_pdas)

- npm을 이용한 [Bulma](https://bulma.io/documentation/components/card/) 설치 명령어
  
  ```bash
  $ npm install bulma
  ```

- 위를 통해 설치하면, `node_modules` 폴더에 bulma 폴더가 생성됨
- bulma 폴더 내 css 폴더 안에 있는 `bulma.css` 파일을 사용하려면, js 파일에서 `import` 해야 함
  - `node_modules` 폴더에 있는 파일을 import 하려면, 상대 경로로 표시할 필요없이 바로 해당 폴더를 적으면 됨
  
  ```js
  import 'bulma/css/bulma.css';
  ```



## ▶ 38. A Big Pile of HTML!

> 자료: [014_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/014_-_pdas)

- Bulma 라이브러리 중 Card component와 Columns를 이용해서 스타일링했음
- 위 라이브러리를 이용할 때, class명을 동일하게 지켜주는 것이 중요



## ▶ 39. Last Bit of Styling

> 자료: [015_-_pdas](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/3.Building_with_Reusable_Components/015_-_pdas)
> 실습: 

