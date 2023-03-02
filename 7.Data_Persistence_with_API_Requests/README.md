# ✔ '07.Data Persistence with API Request' 이론 정리

## ▶ 115. Adding Data Persistence

- section 6에서 진행한 book project는 화면을 새로고침하면 기존에 저장해놓은 book list가 모두 사라짐 (persistence가 없음)
- 새로고침해도 데이터가 남아있게 하기 위해서는 API Server를 이용해서 Database에 데이터를 저장해놔야함
  - 이 수업에서는 오픈소스 서버인 `JSON-Server`를 이용해 plain file로 데이터를 저장할 예정
  - React app → JSON-Server: book list를 request
  - React app ← JSON-Server: book list를 response
- App이 시작할 때 Server로 request를 보내어 book list 데이터를 response 받으면, 이를 books state에 저장해 놓음
- `editBookById()`, `deleteBookById()`, `createBook()` 세 함수를 호출할 경우, Server로 book list를 변경해 달라는 요청을 보내게 되고 요청 성공했다는 response를 받게 되면 books state를 변경하게 됨 
- section 7에서 해야할 일
  - 1) API를 만들고 어떻게 동작하는지 이해한다
  - 2) App이 시작할 때, API로 request를 보내고 현재 books list 데이터를 얻는다
  - 3) 사용자가 book을 create/edit/delete할 때, API를 update하고 local data (book state)도 update한다

## ▶ 116. Server Setup

> 자료: [002_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/002_-_two)

### 🔹 JSON Server Setup 과정

1. 터미널에서 NPM을 이용해 JSON-Server를 설치한다
   
   - React 서버를 돌리는 터미널은 그대로 두고 다른 터미널을 또 하나 연 후, Book Project 폴더 안으로 들어가서 설치해야 함
   
   ```bash
   $ npm install json-server
   ```

2. Book Root Directory에 `db.json` 파일을 생성한다
   
   - `db.json` 파일에서 데이터 저장할 예정
   
   ```
   book
     ↳ node_modules
     ↳ public
     ↳ src
     ↳ db.json
   ```
   
   ```json
   {
    "books": []
   }
   ```

3. `package.json`에서 JSON-Server를 실행시킬 명령어를 생성한다
   
   ```json
   {
    "scripts": {
      "start": "react-scripts start",
      "server": "json-server -p 3001 --watch db.json",
    }
   }
   ```

4. 터미널에서 방금 생성한 명령어를 사용해 JSON-Server을 실행시킨다
   
   - `http://localhost:3001`에서 실행됨
   
   ```bash
   $ npm run server
   ```

## ▶ 117. What Just Happened?

- 이제 Book App을 실행시키려면, 아래 두 개의 명령어를 모두 실행시켜함
  
  - `npm run start`: React dev server를 실행
  - `npm run server`: JSON-Server를 실행

- React Dev Server는 기본적으로 3000 port와 연결됨
  
  - 만약 현재 3000번 port가 이미 다른 server와 연결되어 있다면, 다른 port에 연결시키면 됨

- JSON-Server는 기본적으로 3001 port와 연결됨
  
  - 만약 현재 3001번 port가 이미 다른 server와 연결되어 있다면, 다른 port에 연결시키면 됨
  - JSON-Server가 3001번 port에 연결되어 있다면, React에서 JSON Server로 request할 필요가 있을때 `localhost:3001`을 이용하면 됨

- JSON Server Setup 과정 4번째에서 `"server": "json-server -p 3001 --watch db.json"`의 의미
  
  - 간단하게 `npm run server`을 사용하면 위 명령어가 실행됨
  - `-p 3001`: server를 3001 port에 연결해줘
  - `--watch db.json`: `db.json` 파일에 데이터를 저장해줘

## ▶ 118. How the API Works

- 데이터를 저장하는 `db.json` 초기 상태
  
  - 아래 코드를 통해 books property에 book list 데이터를 저장할 것임을 JSON-Server에게 알려줌
    
    ```json
    {
    "books": []
    }
    ```

### 🔹 JSON-Server를 통해 데이터 저장하기 👉 `Create`

1. Request을 보냄
   
   - method: `POST`
   - URL: `http://localhost:3001/books`
   - body: `{"title": "Harry Potter"}`

2. JSON-Server가 request을 받아 `db.json`의 books property에 데이터를 저장
   
   - 이때, JSON-Server는 자동으로 unique한 `id`를 만들어 요청받은 데이터와 함께 저장함
   
   ```json
   {
    "books": [{"id": 1, "title": "Harry Potter"}]
   }
   ```

3. 요청이 성공하면, JSON-Server가 저장한 데이터로 response 해줌
   
   ```js
   {"id": 1, "title": "Harry Potter"}
   ```

### 🔹 JSON-Server를 통해 데이터 가져오기 👉 `Read`

1. Request을 보냄
   
   - method: `GET`
   - URL: `http://localhost:3001/books`

2. JSON-Server가 request을 받아 `db.json`의 books property 데이터를 가져옴
   
   ```json
   {
    "books": [{"id": 1, "title": "Harry Potter"}]
   }
   ```

3. 요청이 성공하면, books property 데이터 전부를 response 해줌
   
   ```js
   [{"id": 1, "title": "Harry Potter"}]
   ```

### 🔹 JSON-Server를 통해 데이터 수정하기 👉 `Update`

1. Request을 보냄
   
   - method: `PUT`
   - URL: `http://localhost:3001/books/1`
   - body: `{"title": "Dark Tower"}`

2. JSON-Server가 request을 받아 `db.json`의 books property 내 특정 id에 해당하는 데이터를 수정
   
   ```json
   {
    "books": [{"id": 1, "title": "Dark Tower"}]
   }
   ```

3. 요청이 성공하면, JSON-Server가 수정한 데이터로 response 해줌
   
   ```js
   {"id": 1, "title": "Dark Tower"}
   ```

### 🔹 JSON-Server를 통해 데이터 삭제하기 👉 `Delete`

1. Request을 보냄
   
   - method: `Delete`
   - URL: `http://localhost:3001/books/1`

2. JSON-Server가 request을 받아 `db.json`의 books property 내 특정 id에 해당하는 데이터를 삭제
   
   ```json
   {
    "books": []
   }
   ```

3. 요청이 성공하면, JSON-Server가 삭제한 데이터로 response 해줌
   
   ```js
   {"id": 1, "title": "Dark Tower"}
   ```

## ▶ 119. Introducing the REST Client

### 🔹 Standalone API Client

- API Server로 requests을 보내주는 프로그램

- 주로 development나 test용으로 사용됨

- 무료 API Client가 많음

- Book Root Directory에 `api.http` 파일을 만들어 request 내용을 적으면 좌측 상단에 send request 버튼이 나타나게 되는데, 이 버튼을 누르면 VScode 오른쪽에 response를 바로 볼 수 있음

- `api.http` 파일을 이용해 requests와 response를 한 눈에 볼 수 있으므로, 이 문서를 다른 개발자들에게 공유하면 App을 이해하는데 도움을 줌

- 이 수업에서는 VSCode extension에서 바로 설치 가능한 `REST Client`를 이용할 예정

- `api.http` 파일 내 코드 예시
  
  ```
  GET http://localhost:3001/books HTTP/1.1
  Content-Type: application/json
  ```

- send request 버튼을 누르면 Response를 볼 수 있음
  
  ```
  [
   {
      "id": 1,
      "title": "Harry Potter"
   }
  ]
  ```

## ▶ 121. Using the REST Client

> 자료: [006_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/006_-_two)

- 실제 Book Root Directory에 `api.http` 파일을 만들고 request 코드를 적어보자
  - 이때, 대소문자/철자/특수기호 등 정확하게 적어야 함
  - `#`을 하나 사용해 주석처리 가능
  - `###`는 separator로 서로 다른 requests를 구분지어 줌 
  - `Content-Type` 코드 아래에 한 줄 건너띄고 그 다음 줄에 request body를 적어야 함
  - request body에서 object를 적을 때, key와 value는 반드시 큰 따옴표로 감싸야 함
- API Client를 통해 JSON Server에 request를 보내는 것을 성공하면, `db.json` 파일 내 데이터가 변경됨
  - Axios를 사용하지 않고도 바로 request-response 결과를 볼 수 있어서 편리함

### 🔹 API Client를 이용해 모든 데이터 가져오기 👉 `Read`

- request
  
  ```
   # Get all books
   GET http://localhost:3001/books HTTP/1.1
   Content-Type: application/json
  ```

- response
  
  ```
  []
  ```

### 🔹 API Client를 통해 데이터 저장하기 👉 `Create`

- request
  
  ```
   # Create a book
   POST http://localhost:3001/books HTTP/1.1
   Content-Type: application/json
  
   {
    "title": "Harry Potter"
   }
  ```

- response
  
  ```
  {
    "title": "Harry Potter",
    "id": 1
  }
  ```

### 🔹 API Client를 통해 데이터 수정하기 👉 `Update`

- request
  
  ```
   # Update a book
   PUT http://localhost:3001/books/1 HTTP/1.1
   Content-Type: application/json
  
   {
     "title": "Dark Tower"
   }
  ```

- response
  
  ```
  {
    "title": "Dark Tower",
    "id": 1
  }
  ```

### 🔹 API Client를 통해 데이터 삭제하기 👉 `Delete`

- request
  
  ```
   # Delete a book
   DELETE http://localhost:3001/books/1 HTTP/1.1
   Content-Type: application/json
  ```

- response
  
  ```
  {}
  ```

## ▶ 122. Creating a New Record

> 자료: [007_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/007_-_two)

- App component에서 `createBook()` 함수를 리팩토링해보자

- 사용자가 book을 create했을 때, JSON Server로 request를 보내 `db.json`를 update하자
  
  - request를 보내기 위해 npm을 이용해 Axios 설치
    
    ```bash
    $ npm install axios 
    ```
    
    - 네트워크와 연결할 경우, 반드시 `async`/`await`를 잊지 말고 적어줘야 함
    - `axios.post()`로 요청을 보내야 함
    - post() 메소드의 첫번째 인자에는 URL 경로를 적고, 두번째 인자에는 request body 부분을 적으면 됨
    - `db.json` 파일을 열면 저장된 모든 데이터를 바로 확인할 수 있음

- JSON-Server가 방금 저장한 데이터를 response해주면, 이를 바탕으로 `books state`도 update하자
  
  - `{data: {...}, config: {...}, headers: {...}, ...}` 형태로 response 해주는데, 여기서 내가 저장한 데이터를 가지고 오려면 `response.data`를 사용하면 됨
  - `data: {id: 1, title: "Harry Porter"}`
  
  ```js
   import axios from 'axios';
  
   function App() {
      const [books, setBooks] = useState([]);
      // 생략
      const createBook = async (title) => {
         const response = await axios.post('http://localhost:3001/books', {
            title: title,
         });
  
         const updatedBooks = [...books, response.data];
         setBooks(updatedBooks);
      };
      // 생략
   }
  ```

## ▶ 123. Fetching a List of Records

> 자료: [008_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/008_-_two)

- App이 가장 처음 render될 때, JSON-Server로 request를 보내 모든 데이터를 가지고 와야함
  
  - `fetchBooks()` 함수를 만들어 server로 request를 보내자
  - `axios.post()`로 요청을 보내야 함
  - 네트워크와 연결할 경우, 반드시 `async`/`await`를 잊지 말고 적어줘야 함
  
  ```js
   function App() {
      const [books, setBooks] = useState([]);
  
      const fetchBooks = async () => {
         const response = await axios.get('http://localhost:3001/books');
  
         setBooks(response.data);
      };
      // 생략
   }
  ```

- `fetchBooks()` 함수를 아래와 같이 호출하면 절대 안됨
  
  - 이유: 1) `fetchBooks()` 함수 호출 → 2) `setBooks()`에 의해 rerender 과정이 무한 반복됨
  
  ```js
   // 옳지 못한 방법!
   function App() {
      const [books, setBooks] = useState([]);
  
      const fetchBooks = async () => {
         // 생략
      };
  
      fetchBooks();
      // 생략
   }
  ```

- `fetchBooks()` 함수는 App이 가장 처음 render될 때 딱 한 번 호출되어야 하는데, 이는 `useEffect` 훅을 이용해 해결할 수 있음

## ▶ 124. Introducing useEffect

> 자료: [009_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/009_-_two)

### 🔹 useEffect란?

- React에서 가져올 수 있는 함수

- render되는 어느 특정 시점에 callback함수를 실행시킴
  
  - component가 가장 처음 render될 때나, rerender될 때마다 callback함수를 실행할 수 있음

- `useEffect()`의 argument
  
  - 첫번째 인자: 우리가 특정 시점에 실행시키고자하는 함수
  
  - 두번째 인자: 생략할 수도 있고 array를 넣을 수도 있음
  
  - 두번째 인자를 통해 App이 rerender될 때마다 첫번째 인자인 함수를 실행시킬지 말지 결정할 수 있음
    
    ```js
    useEffect(() => {
      console.log('Hi')
    }, [])
    ```

### 🔹 Book App에 useEffect 적용하기

```js
 import { useState, useEffect } from 'react';

 function App() {
    const [books, setBooks] = useState([]);

    const fetchBooks = async () => {
       const response = await axios.get('http://localhost:3001/books');

       setBooks(response.data);
    };

    useEffect(() => {
       fetchBooks();
    }, []);
    // 생략
 }
```

## ▶ 125. useEffect in Action

### 🔹 useEffect에 대해서 알아야할 것

1. 언제 arrow function을 호출하는지 이해하기
2. arrow function의 return 값 이해하기
3. stale variable reference 이해하기

### 🔹 useEffect에서 function 호출 시점 조절하기

- React에 의한 render 과정: Component 호출 → JSX return → DOM update 
  
  - 사용자 반응(클릭 등)에 의해 state가 update되면 rerender됨
  - `1st render` --state update-→ `2nd render` --state update-→ `3td render` 

- useEffect의 arrow function은 `1st render`가 일어날 때 반드시 호출되고, 이후 state가 update돼서 rerender될 때는 두번째 인자에 따라 호출될지 말지 결정됨
1. 두번째 인자 = `[]` 일 때
   
   - arrow function은 `1st render` 때 반드시 호출되고, 이후 **rerender될 때는 호출되지 않음**
   
   ```js
   useEffect(() => {
      console.log('Hi')
   }, [])
   ```

2. 두번째 인자 = `nothing` 일 때
   
   - arrow function은 `1st render` 때 반드시 호출되고, 이후 **rerender될 때마다 호출됨**
   
   ```js
   useEffect(() => {
      console.log('Hi')
   })
   ```

3. 두번째 인자 = `[ele1, ele2]` 일 때
   
   - array 안에 있는 요소는 prop이나 state 변수
   - arrow function은 `1st render` 때 반드시 호출되고, 이후 **rerender 과정에서 만약 array 안의 각 요소에 변화가 있을 때만 호출됨**
   
   ```js
   useEffect(() => {
      console.log('Hi')
   }, [counter])
   ```



## ▶ 126. More on useEffect

> 참고: [useEffect 활용 예](https://codepen.io/hyejinny97/pen/VwGmrKy?editors=0011)

|                              | []  | nothing | [countOne] | [countTwo] | [countOne, countTwo] |
| ---------------------------- |:---:|:-------:| ---------- | ---------- | -------------------- |
| 1st render                   | O   | O       | O          | O          | O                    |
| `counterOne` 변화에 의한 rerender | X   | O       | O          | X          | O                    |
| `counterTwo` 변화에 의한 rerender | X   | O       | X          | O          | O                    |

- useEffect의 두번째 인자에 따라 arrow function 호출 여부를 체크한 표
  - O 표시: 특정 시점의 render때, arrow function이 호출됨
  - X 표시: 특정 시점의 render때, arrow function이 호출되지 않음



## ▶ 127. Updating a Record

> 자료: [012_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/012_-_two)

- App component에서 `editBookById()` 함수를 리팩토링해보자
  
  ```js
   function App() {
      const [books, setBooks] = useState([]);
      // 생략
      const editBookById = async (id, newTitle) => {
         const response = await axios.put(`http://localhost:3001/books/${id}`, {
            title: newTitle,
         });

         const updatedBooks = books.map((book) => {
            if (book.id === id) {
            return { ...book, title: newTitle };
            }

            return book;
         });

         setBooks(updatedBooks);
      };
      // 생략
   }
  ```



## ▶ 128. Thinking About Updates

> 자료: [013_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/013_-_two)

- 127번에 적은 코드는 현재 문제없이 잘 진행되는 것처럼 보이지만, App을 사용하는 사용자가 2명 이상이고 book object의 property가 2개 이상일 경우 문제가 발생함
  - 따라서, books state를 update할 때 request한 후 얻은 response data를 이용해야 함

  ```js
   function App() {
      const [books, setBooks] = useState([]);
      // 생략
      const editBookById = async (id, newTitle) => {
         const response = await axios.put(`http://localhost:3001/books/${id}`, {
            title: newTitle,
         });

         const updatedBooks = books.map((book) => {
            if (book.id === id) {
            return { ...book, ...response.data };
            }

            return book;
         });

         setBooks(updatedBooks);
      };
      // 생략
   }
  ```



## ▶ 129. Deleting a Record

> 자료: [014_-_two](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/7.Data_Persistence_with_API_Requests/014_-_two)

- App component에서 `deleteBookById()` 함수를 리팩토링해보자

   ```js
   function App() {
   const [books, setBooks] = useState([]);
   // 생략 
   const deleteBookById = async (id) => {
      await axios.delete(`http://localhost:3001/books/${id}`);

      const updatedBooks = books.filter((book) => {
         return book.id !== id;
      });

      setBooks(updatedBooks);
   };
   // 생략
   }
   ```