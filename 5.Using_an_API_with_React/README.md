# ✔ '05.Using an API with React' 이론 정리



## ▶ 59. App Overview

- 만들고자하는 Project
  - 구글 검색엔진처럼, 검색창에 단어를 입력하고 enter 키를 누르면 해당 단어에 해당하는 이미지들을 일렬로 나타냄



## ▶ 60. Project Setup

> 자료: [002_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/002_-_pics)

- src 폴더 안에 `index.js`, `App.js`, `SearchBar.js`, `ImageList.js`, `ImageShow.js` 총 5개의 파일을 만듦
  - App: 단순히 SearchBar, ImageList components를 묶어둠
  - SearchBar: 검색창 관련 컴포넌트
  - ImageList: 여러 ImageShow를 묶어둠
  - ImageShow: 이미지 하나에 대한 컴포넌트
- src 폴더 안에 components 폴더를 따로 만들어 `SearchBar.js`, `ImageList.js`, `ImageShow.js` 세 파일을 넣어둠



## ▶ 61. The Path Forward

- `Unsplash API`를 이용해 검색어와 관련된 사진 데이터들을 받을 것임
  - our App -`request`→ Unsplash API 
  - our App ←`response`- Unsplash API 
- HTTP Request and HTTP Response
  - 우리 앱이 Unsplash API로 특정 term에 대한 이미지를 request하면, 각 이미지에 대한 정보를 담은 object들을 배열에 담아 response해줌
- React는 오로지 content를 화면에 보여주고 사용자 event를 핸들링하는 역할만 할 뿐, HTTP request를 하진 못함



## ▶ 62. Overview of HTTP Requests

### 🔹 HTTP Request and HTTP Response

- our App이 특정 server로 HTTP Request를 보내면, server가 our App으로 HTTP Response를 보내게 됨

1. HTTP Request
   
   - `Request Line`  
     - HTTP method: request의 목표 (GET, POST 등)

     ```
     GET https://api.unsplash.com/images/search HTTP/1.1
     ```
  
   - `Headers`
     - 요청하는 사람에 대한 정보

     ```
     Accept-Version: v1
     Authorization: Client-ID ABC123
     ```

   - `Body`

2. HTTP Response
   
   - `Status Line`  
     - status: request 성공 여부 (2xx, 3xx, 4xx, 5xx)

     ```
     HTTP/1.1 200 OK
     ```
  
   - `Headers`

     ```
     Content-Length: 1000
     Content-Type: application/json
     ```

   - `Body`
  
      ```
     [
      {id: '123'}
     ]
     ``` 

### 🔹 HTTP Methods

- GET: server로부터 정보를 얻고자 요청함
- POST: server가 새로운 record를 만들도록 요청함
- PUT: 기존에 존재하는 record를 완전히 update하게 요청함
- PATCH: 기존에 존재하는 record를 부분적으로 update하게 요청함
- DEL: record를 삭제하게 요청함

### 🔹 HTTP Status Codes

1. `2xx` (성공)
   
   - 200: 요청을 성공적으로 처리함
   - 201: record가 생성됨
   - 204: record가 삭제됨
  
2. `3xx` (redirection 완료)
   
   - 301: 요청한 URL이 바꼈음
  
3. `4xx` (request 오류)
   
   - 400: request에 오류가 있음
   - 401: 인증되지 않음
   - 403: (forbidden) 접근 금지
   - 404: (Not Found) 존재하지 않는 주소
  
4. `5xx` (server 오류) 
   
   - 500: server쪽 오류

### 🔹 request와 response 간의 시간차

- request를 보내고 response를 받을 때까지 시간이 걸림
- 아래 코드에서 response를 받을 때까지 기다리지 않고 바로 아래 console.log()가 실행됨
  - 따라서, response를 받을 때까지 기다렸다가 다음 코드들을 실행시키는 과정이 필요
  
   ```js
   const fetchData = () => {
    const response = makeRequest();

    console.log(response);
   };
   ```



## ▶ 63. Understanding the API

- [Unsplash API 주소: unsplash.com/developers](https://unsplash.com/developers)
- 계정 생성 후, app 만들고 access key 얻기
- documentation 학습 후, HTTP request 보내기
  - root URL: `https://unsplash.com`
  - Search Photo parameters: `query`, `page` 등
  - Authorization: Client-ID YOUR_ACCESS_KEY
- `Request Line`  

   ```
   GET https://api.unsplash.com/search/photo?query=cars HTTP/1.1
   ```

- `Headers`

   ```
   Accept-Version: v1
   Authorization: Client-ID YOUR_ACCESS_KEY
   ```



## ▶ 64. Making an HTTP Request

> 자료: [006_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/006_-_pics)

- Request를 보내기 위해서, 흔히 `Axios` 나 `Fetch`를 사용
  - [Axios API](https://axios-http.com/kr/docs/intro): JS library
  - [Fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch): browser function
  - 이 수업에서는 Axios를 사용해 request를 보낼 것임

### 🔹 Axios 

- pics project 내부에서 npm을 사용해 axios 설치
  - node_modules 폴더 안에 axios 폴더가 생성됨
  
   ```bash
   $ npm install axios
   ```

- axios를 이용해 request 하는 방법
  - request method: `get`, `post`, `del` 등
  - method의 첫번째 인자로 요청 보내고자하는 url를 적어야 함
  - method의 두번째 인자로 headers, params 등과 같은 options를 적을 수 있음
    - params: key-value 쌍으로 query string으로 전환된 후, url에 추가됨
  
   ```js
   axios.get(url, {
    headers: {}, 
    params: {},
   });
   ```

- pics project의 src 폴더 내에 `api.js` 생성한 후, axios 요청 코드 작성
  
   ```js
   import axios from 'axios';
 
   const searchImages = async () => {
     const response = await axios.get('https://api.unsplash.com/search/photos', {
       headers: {
         Authorization: 'Client-ID MY_ACCESS_KEY',
       },
       params: {
         query: 'cars',
       },
     });
 
     return response;
   };
 
   export default searchImages;
   ```



## ▶ 65. [Optional] Using Async:Await

> 자료: [007_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/007_-_pics)  

- `async`와 `await` 키워드를 사용하면, request 보낸 후 response가 올 때까지 기다렸다가 다음 줄을 실행시킴
  - `await` 키워드는 `async` 함수 안에서만 동작하기 때문에 반드시 함수 앞에 `async` 키워드를 적어줘야 함
  
   ```js
   const fetchData = async () => {
    const response = await makeRequest();

    console.log(response);
   };
   ```
  


## ▶ 66. Data Fetching Cleanup

> 자료: [008_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/008_-_pics)  

- unsplash는 시간 당 request 수가 50번으로 정해져 있음 
- 'cars'로 하드 코딩하는 대신, term 인자를 받아서 query에 할당해줌
- response를 return하는 대신, response.data.results를 반환해줌
  - response.data.results는 array of objects 형태임

   ```js
   const searchImages = async (term) => {
     const response = await axios.get(
      // 생략 
      {
       // 생략
       params: {
         query: term,
       },
     });
 
     return response.data.results;
   };
   ``` 



## ▶ 67. Thinking About Data Flow

- pics App의 기본 작동 방식
  - SearchBar component에는 text 타입의 input 태그가 존재
  - 사용자가 text input에 검색하고자 하는 text를 적고 'enter' key를 누름
  - request을 통해 검색한 용어에 관련된 image objects의 array를 반환 받음
  - array of image objects는 ImageList component에서 사용됨
- 즉, SearchBar component에서 얻은 term을 searchImages() 함수의 인자로 넣어 array of image objects를 반환받은 후 ImageList component로 넘겨줘야 함
- sibling components(SearchBar, ImageList)간 직접 데이터를 주고받는 방법은 없음
  - sibling components끼리 데이터를 공유하려면, 반드시 parent component를 거쳐 가야함
  - 따라서, App이 SearchBar에서 term을 넘겨받아 관련 이미지들을 ImageList로 넘겨줘야함
  - SearchBar --`term`-→ App --`images by searchImages()`-→ ImageList
- 부모 자식 component간 데이터 주고받기
  - parent component → child component: `props` system 이용
  - parent component ← child component: event handling 이용



## ▶ 68. Child to Parent Communication

- child component에서 parent component로 데이터를 전달하는 방법은 한 component의 요소에서 event를 처리하는 방식과 유사함
  - 가정: App(parent component), button 요소(child component)
  - button에 클릭 이벤트가 일어나면, handleClick() 함수가 실행되는 코드를 짰다고 하자
  - button 요소는 props로 `{onClick: handleClick}`를 넘겨받았다고 할 수 있음
  - 버튼을 클릭하면, props로 받은 onClick 함수를 호출하게 되고 이는 handleClick 함수를 실행시킴
  - 즉, button 요소(child component)가 App(parent component)에 영향을 줌
- SearchBar(child component)에서 App(parent component)으로 데이터 전달하는 방법
  - SearchBar는 App으로부터 props로 `{onSubmit: handleSubmit}`을 넘겨받음
  - 검색창에 text를 적고 엔터키를 누르면, props로 받은 onSubmit 함수를 호출하게 되고 이는 App 내에 있는 handleSubmit 함수를 실행시킴



## ▶ 69. Implementing Child to Parent Communication

> 자료: [011_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/011_-_pics)  

- SearchBar에서 임시로 input 요소 대신 button 요소를 넣어, 버튼을 클릭할 때마다 App 내에 있는 handleSubmit 함수를 실행시켰음
  
  ```js
  // App.js
  function App() {
    const handleSubmit = (term) => {
      console.log('Do a search with', term);
    };

    return (
      <div>
        <SearchBar onSubmit={handleSubmit} />
      </div>
    );
  }
  ```

  ```js
  // SearchBar.js
  function SearchBar({ onSubmit }) {
    const handleClick = () => {
      onSubmit('cars');
    };

    return (
      <div>
        <input />
        <button onClick={handleClick}>Click me</button>
      </div>
    );
  }
  ```