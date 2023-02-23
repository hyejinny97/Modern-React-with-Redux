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



## ▶ 70. Handling Form Submission

> 자료: [012_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/012_-_pics) 

- `input` 태그와 `button` 태그를 `form` 태그로 감싸면, 텍스트를 입력하고 엔터키를 누르거나 버튼을 클릭할 때 자동으로 submit됨

  - 이때, 텍스트는 query string 형식으로 만들어져 아래처럼 url에 포함된 후 request됨

    ```
    myapp.com?email=asdf@asdf.com&password=asdf
    ```
- `form` 태그를 사용하지 않는다면, 엔터키 누를 때와 버튼 클릭할 때 각각을 이벤트 걸어줘야해서 번거러움 
- form이 자동으로 새 url로 요청을 보내 새로고침되지 않게 하기 위해선, `preventDefault()` 함수를 사용해 form의 기본값을 막아줘야 함
  - event handler 함수의 인자로 `event object`를 넣어줄 수 있음

  ```js
  // SearchBar.js
  function SearchBar({ onSubmit }) {
    const handleFormSubmit = (event) => {
      event.preventDefault();
      onSubmit('cars');
    };

    return (
      <div>
        <form onSubmit={handleFormSubmit}>
          <input />
        </form>
      </div>
    );
  }
  ```



## ▶ 71. Handling Input Elements

> 자료: [013_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/013_-_pics) 

- React에서 Input 요소를 처리할 때, 아래와 같이 querySelector() 함수를 사용해 input의 value를 얻는 것은 **절대 옳지 않음**
  
   ```js
   // 아래 방법 절대 금지!
   const handleFormSubmit = (event) => {
     event.preventDefault();
     onSubmit(document.querySelector('input').value);
   };  
   ```

- 대신, React에서 Form 요소들(text input, checkboxes, radio buttons 등)을 처리할 때는 `state`와 `event`를 사용해야함
  - 이 방법이 복잡해 보여도 나중에 더 어려운 문제를 마주할 때 손쉽게 해결할 수 있음
  - text input 요소 이외의 form 요소들도 아래와 동일/유사한 방법으로 처리해야함

### 🔹 React에서 Text Input 요소 처리하는 법

1. `state`를 만든다
   
2. `onChange` 이벤트에 대한 event handler를 만들어준다
   
   - `onChange` 이벤트: text 입력, 삭제, 복사붙여넣기 등
  
3. `onChange` 이벤트가 일어날 때마다, input 요소로부터 value를 얻는다
   
4. setter 함수를 이용해 state를 input 요소로부터 얻은 value로 update한다
   
5. input 요소 내 value prop의 값으로 state 변수를 넣어준다
   
    ```js
    function SearchBar({ onSubmit }) {
      const [term, setTerm] = useState('');

      const handleChange = (event) => {
        setTerm(event.target.value);
      };

      return (
        <div>
          <form onSubmit={handleFormSubmit}>
            <input value={term} onChange={handleChange} />
          </form>
        </div>
      );
    }
    ```



## ▶ 72. [Optional] OK But Why?

### 🔹 Text Input 요소에 타이핑했을 때 일어나는 상세 과정

1. user가 input에 타이핑을 한다
2. 브라우저가 input 창에 타이핑한 텍스트를 update한다
3. 브라우저는 input 창이 update되었다는 event를 발생시킨다
4. event handler 함수가 호출되어, setter 함수에 의해 state가 update 된다
5. component가 rerender된다
6. input 요소의 value prop 값을 바꾸면서, input 창에 **동일한 text**가 덮어씌워진다

### 🔹 굳이 State와 Event를 이용해 Form 요소들을 처리해야하는 이유

1. `state`를 통해 input 요소의 value를 쉽게 읽을 수 있다
2. `setter function`을 통해 input 요소의 value를 쉽게 update 할 수 있다
3. 타이핑을 할 때마다 component가 rerender되므로, 어려운 문제를 쉽게 해결할 수 있다
   
    ```js
    // ex1) input 요소에 초깃값 설정하기
    function SearchBar({ onSubmit }) {
      const [term, setTerm] = useState('cars');
      // 생략
    }    
    ```

    ```js
    // ex2) 타이핑할 때마다, 아래에 타이핑한 글자 나타내기
    function SearchBar({ onSubmit }) {
      // 생략
      return (
        <div>
          <form onSubmit={handleFormSubmit}>
            <input value={term} onChange={handleChange} />
            your search: {term}
          </form>
        </div>
      );
    }    
    ```

    ```js
    // ex3) 조건에 따라 에러 메시지 보여주기 (validation)
    function SearchBar({ onSubmit }) {
      // 생략
      return (
        <div>
          <form onSubmit={handleFormSubmit}>
            <input value={term} onChange={handleChange} />
            {term.length < 3 && 'Term must be longer'}
          </form>
        </div>
      );
    }    
    ```

    ```js
    // ex4) 영어 소문자는 입력하지 못하게 하기
    function SearchBar({ onSubmit }) {
      // 생략
      const handleChange = (event) => {
        setTerm(event.target.value.replace(/[a-z]/, ''));
      };
      // 생략
    }  
    ```



## ▶ 74. Running the Search

- form을 submit하면 입력한 text가 App component의 handleSubmit() 함수로 전달됨
  
   ```js
   function SearchBar({ onSubmit }) {
    // 생략
    const handleFormSubmit = () => {
      event.preventDefault();
      onSubmit(term);
    };
    // 생략
   }
   ```

- App component에서 searchImages() 함수를 불러와 term과 관련된 이미지를 request
  
   ```js
   import searchImages from './api';

   function App() {
     const handleSubmit = (term) => {
       searchImages(term);
     };
     // 생략
   }
   ```



## ▶ 75. Reminder on Async:Await

> 자료: [017_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/017_-_pics)

- Unsplash로 request해서 받은 data들을 아래와 같이 확인해보자
  - 아마 콘솔창에는 data가 아닌 promise object가 찍혀있을 것임
  - request한 후 response를 받을 때까지 약간의 시간이 걸리기 때문에, result에 결과 data가 할당되지 않았기 때문
  
   ```js
   function App() {
     const handleSubmit = (term) => {
       const result = searchImages(term);
       console.log(result);
     };
     // 생략
   }
   ```

- `await`/`async` 키워드를 사용하면, request가 성공적으로 완료될 때까지 기다렸다가 결과 data를 result에 할당해 주게 됨
  
   ```js
   function App() {
     const handleSubmit = async (term) => {
       const result = await searchImages(term);
       console.log(result);
     };
     // 생략
   }
   ```



## ▶ 76. Communicating the List of Images Down

> 자료: [018_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/018_-_pics)

- request를 통해 얻은 array of objects 형태의 image 데이터를 ImageList component로 내려 보내줘야 함
  - `state` system + `props` system을 이용
  - 화면에 content를 update하기 위해선 state를 사용해야함
  - parent component에서 child component로 data를 전달하기 위해선 props를 사용해야함
- setter function에 의해 state가 변경되면, **해당 component뿐만 아니라 모든 child component도 rerender**됨
  - 즉, images가 변경되면 App component 뿐만 아니라 SearchBar, ImageList component가 모두 rerender됨
  
  ```js
  // App.js
  function App() {
    const [images, setImages] = useState([]);

    const handleSubmit = async (term) => {
      const result = await searchImages(term);
      setImages(result);
    };

    return (
      <div>
        <SearchBar onSubmit={handleSubmit} />
        <ImageList images={images} />
      </div>
    );
  }   
  ```

  ```js
  // ImageList.js
  function ImageList({ images }) {
    return <div>ImageList: {images.length}</div>;
  }
  ```



## ▶ 77. Building a List of Images

> 자료: [019_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/019_-_pics)

- ImageList component에서 ImageShow component로 각 images 객체 전달
  - `map()` 함수 이용
  - 현 상태에서 콘솔창을 보면 key prop을 추가해야한다고 에러 메시지가 뜸
  
  ```js
  function ImageList({ images }) {
    const renderedImages = images.map((image) => {
      return <ImageShow image={image} />;
    });

    return <div>{renderedImages}</div>;
  }
  ```

- ImageShow component에서 props로부터 전달받은 image 객체의 id를 출력
  
  ```js
  function ImageShow({ image }) {
    return <div>{image.id}</div>;
  }
  ```



## ▶ 78. Handling List Updates

> List elements에서 `key` prop을 사용해야하는 이유

- 일부 records를 swap 하거나 특정 요소를 중간에 추가할 때처럼, State가 변하면 HTML을 update 해야함
  
   ```js
   // 기존 데이터
   const images = [
    {id: 1, desc: 'black shelby'},
    {id: 2, desc: 'orange lambo'},
    {id: 3, desc: 'black porsche'},
    {id: 4, desc: 'white car'},
   ]
   ```

   ```js
   // 변경된 데이터
   const images = [
    {id: 2, desc: 'orange lambo'},
    {id: 1, desc: 'black shelby'},
    {id: 3, desc: 'black porsche'},
    {id: 4, desc: 'white car'},
   ]
   ```

   ```js
   const renderedImages = images.map((image) => {
     return <ImageShow image={{desc: image.desc}} />;
   });
   ```

### 🔹 List elements가 변할 때, HTML을 update하는 방법

1. 방법1) 존재하는 모든 HTML을 제거한 후, 다시 모든 List elements를 recreate한다
   - 매우 비효율적인 방법
   - 만약 1,000개의 요소가 있고, 그 중 2개만 swap한다고 할 때도 전부를 지우고 recreate해야함

2. 방법2) List building 과정에서 각 element에 `key` prop을 추가해준다
   - records가 변해 rerender될 때, React는 rerender 전후의 ImageShow components의 각 `key` 순서를 비교함
   - React가 차이나는 DOM elements만 바꿔줌

   ```js
   const renderedImages = images.map((image) => {
     return <ImageShow key={image.id} image={{desc: image.desc}} />;
   });
   ```



## ▶ 79. Notes on Keys

> 자료: [021_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/021_-_pics)

- List elements에 `key` prop 추가

  ```js
  function ImageList({ images }) {
    const renderedImages = images.map((image) => {
      return <ImageShow key={image.id} image={image} />;
    });

    return <div>{renderedImages}</div>;
  }
  ```

### 🔹 key prop 사용 시, 지켜야할 필수 조건

1. List Elements에 반드시 `key` prop을 사용해야함
   - 따라서, map() 함수를 사용할 때 반드시 요소에 `key` prop을 적어줘야 함

2. 최상위 JSX element에 `key` prop을 적어야 함 

   ```js
   function ImageList({ images }) {
     const renderedImages = images.map((image) => {
       return (
         <div key={image.id}>
           <ImageShow image={image} />
         </div>
       );
     });
 
     return <div>{renderedImages}</div>;
   }
   ```
 
3. `key` prop의 value값으로 `string`이나 `Number`가 와야함

4. `key` prop의 value값은 Single List 내에서 Unique 해야함
   - 즉, 하나의 List 내에서 value값이 중복되어선 안됨
   - 서로 다른 List에서 동일한 value값을 가지는 건 상관없음

   ```js
   // 올바른 예
   function App() {
    const images = [{id: 'a1'}, {id: 'b2'}];
    const videos = [{id: 'a1'}, {id: 'b2'}];

    const renderedImages = images.map((image) => {
      return <div key={image.id} />
    });

    const renderedVideos = videos.map((video) => {
      return <div key={video.id} />
    });

    return (
      <div>
        {renderedImages}
        {renderedVideos}
      </div>
    );
   }
   ```

   ```js
   // 잘못된 예
   function App() {
    const images = [{id: 'a1'}, {id: 'b2'}];
    const videos = [{id: 'a1'}, {id: 'b2'}];

    const renderedImages = images.map((image) => {
      return <div key={image.id} />
    });

    const renderedVideos = videos.map((video) => {
      return <div key={video.id} />
    });

    return (
      <div>
        {renderedImages.concat(renderedVideos)}
      </div>
    );
   }
   ```

5. `key` prop의 value값은 Consistent 해야함
   - 즉, rerender할 때마다 `key` prop의 value값이 달라지면 안됨

   ```js
   // 잘못된 예
   function App() {
    const images = [{id: 'a1'}, {id: 'b2'}];

    const renderedImages = images.map((image) => {
      return <div key={Math.random()} />
    });

    return (
      <div>
        {renderedImages}
      </div>
    );
   }
   ```

### 🔹 key prop의 value로 적합한 값

1. API를 통해 fetch한 데이터인 경우, 일반적으로 array of objects 형태이고 각 object는 unique한 `id` 값을 가지고 있음

2. `id`가 없을 경우, 우리가 직접 unique한 값을 지정해줘야 함
   1. index를 사용한다
      - 단 data가 변하는 경우, 이 방법은 bug를 발생할 수 있음

       ```js
       function App() {
        const values = [];
  
        for (let i=0; i < 5; i++) {
          values.push(<div key={i}></div>);
        } 
  
        return (
          <div>
            {values}
          </div>
        );
       };
       ```
  
   2. unique ID를 직접 만들어준다
     - Redux에 대한 강의에서 다시 설명할 예정



## ▶ 80. Displaying Images

> 자료: [022_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/022_-_pics)

- ImageShow component에서 img 태그를 이용해 검색어 관련 사진 보여주기
  
  ```js
  function ImageShow({ image }) {
    return (
      <div>
        <img src={image.urls.small} alt={image.alt_description} />
      </div>
    );
  }
  ```


## ▶ 81. A Touch of Styling

> 자료: [023_-_pics](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/5.Using_an_API_with_React/023_-_pics)

- `SearchBar` component 스타일링
  
  ```js
  // SearchBar.js
  function SearchBar({ onSubmit }) {
    // 생략
    return (
      <div className="search-bar">
        <form onSubmit={handleFormSubmit}>
          <label>Enter Search Term</label>
          <input value={term} onChange={handleChange} />
        </form>
      </div>
    );
  }  
  ```

  ```css
  /* SearchBar.css */
  .search-bar {
    border: 1px solid lightgray;
    border-radius: 5px;
    padding: 10px;
  }

  .search-bar form {
    display: flex;
    flex-direction: column;
  }
  ```

- `ImageList` component 스타일링
  
  ```js
  // ImageList.js
  function ImageList({ images }) {
    // 생략
    return <div className="image-list">{renderedImages}</div>;
  } 
  ```

  ```css
  /* ImageList.css */
  .image-list {
    columns: 6 200px;
    column-gap: 10px;
  }

  img {
    width: 100%;
    margin-bottom: 10px;
  }
  ```



## ▶ 82. App Wrapup

> 실습: [Section5: Using an API with React](https://codesandbox.io/s/section5-using-an-api-with-react-ydkj9b?file=/src/App.js)

- Text input 등 form control 요소를 다룰 때, state와 event로 관리해야 함
- child component에서 parent component로 communicate할 때, event handler를 사용함
- List Elements는 key prop을 반드시 가지고 있어야 함