# ✔ '08.Communication Using the Context System' 이론 정리

## ▶ 130. Introducing Context

- section 6에서 진행한 book project는 물론 기능상으로는 문제가 없는 코드이지만, App component과 하위 component 간의 communication을 위해 book state와 event handler를 prop을 통해 계속해서 아래로 전달해줘야하는 불편함이 있음
- `Context`를 사용하면 상위 component와 하위 component가 다른 방법으로 communication이 가능함
  - Context는 Props를 대체해서 사용할 수 있는 기능임
  - 서로 다른 component끼리 직접 데이터를 주고 받지 않아도, context를 통해 데이터를 공유할 수 있음
  - 위에서 말한 데이터로는 string, number, object, array, function 등 어떠한 자료형도 가능 (ex) books state, editBookId(), deleteBookById(), createBook())
  - 주의) Context는 Props의 완전한 대체제가 아닐 뿐더러 (혼용해서 사용 가능), Redux와 같은 centralized state stores의 대체제도 아님
  - 단지 Context는 communication channel이라, Redux와 달리 공유하고자하는 데이터가 무엇인지도 모르고 어떻게 organize되어있는지도 모름

### 🔹 Context 사용하는 법

1. Context를 만든다
  
   - 일단, 새로운 파일을 만들어 그 안에 Context를 생성한다
   - react 모듈에서 `createContext()` 함수를 import해서 context 생성 가능
   - `createContext()` 함수는 context object를 만들어주고, `Provider`와 `Consumer` property를 가짐
     - `Provider`: 공유할 데이터를 명시해주기위해 사용되는 component
     - `Consumer`: 공유해준 데이터에 접근하기위해 사용되는 component
   - `Consumer`는 자주 사용되진 않음
  
   ```js
   // book.js
   import { createContext } from 'react';

   const BooksContext = createContext();
   ```

   ```js
   <BooksContext.Provider />
   ```

2. 공유(전달)하고자 하는 데이터를 명시해 준다

   - `value` prop을 사용해 공유할 데이터를 명시함
   - 데이터는 string, number, object, array, function 등 어떠한 자료형도 가능
   - Provider 사이에 위치한 component와 그의 children components는 context에 의해 공유된 value에 접근 가능

   ```js
   <BooksContext.Provider value={5}>
     <MyComponent />
   <BooksContext.Provider>
   ```

3. component 안에서 공유 데이터를 사용한다 

   - react 함수인 `useContext()`를 사용하면 context에 의해 공유된 value에 접근 가능
     - `useContext()` 함수의 인자로 context object를 넣어줘야 함

   ```js
   import { useContext } from 'react';
   import BooksContext from './book';

   function MyComponent() {
    const num = useContext(booksContext);  // num = 5

    return <div>{num}</div>
   }
   ```



## ▶ 131. Context in Action

> 자료: [002_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/002_-_context)

- 실제 Book Project에 Context를 적용해보자
  - 일단 value 값으로 숫자 5를 넣어 공유해보자

- Context 생성
  - 일단 context object 파일들을 담아둘 context 폴더를 만들고, 그 안에 `book.js` 파일을 생성함

  ```js
  // context/book.js
  import { createContext } from 'react';

  const BooksContext = createContext();

  export default BooksContext;
  ```

- 공유(전달)하고자 하는 데이터를 명시해 줌
  - `index.js`에서 context provider가 App component를 감싸도록 함
  - App을 감싸면 App component와 children component 모두 데이터 사용 가능
  - 여기선 일단 숫자 5를 context에 의해 공유됨

  ```js
  // index.js
  import BooksContext from './context/books';

  // 생략
  root.render(
    <BooksContext.Provider value={5}>
      <App />
    </BooksContext.Provider>
  );
  ```

- component 안에서 공유 데이터를 사용함
  - BookList component에서 `useContext()` 함수를 사용해 context 데이터를 가져옴
  - context 데이터를 바꾸려면 index.js 파일로 가서 context provider의 value 값을 직접 바꿔줘야 함 (비효율적)
  
  ```js
  // BookList.js
  import { useContext } from 'react';
  import BooksContext from '../context/books';

  function BookList({ books, onDelete, onEdit }) {
    const value = useContext(BooksContext);
    // 생략
    return (
      <div className="book-list">
        {value}
        {renderedBooks}
      </div>
    );
  }
  ```



## ▶ 132. Changing Context Values

- 131번 코드에서는 context value를 우리가 직접 바꾸는 방법 외에 시간에 따라 자동으로 바꿀 수 있는 방법이 없다
  - 사용자의 반응에 따라 context value를 바꾸고 자동으로 rerender되어 화면을 update하려면 state system을 이용해야 함!
- context value로 state system을 적용하기 위해 `Custom Provider`을 이용해야 함
  - 직접 만든 component로, context object 생성 시 자동으로 만들어진 기존 provider component를 감싸서 object 형태의 데이터를 전달할 수 있음
  - 결과적으로, Custom Provider component > 기존 Provider component > App component 형태로 감싸게 됨
  - 컨벤션에 의해 보통 component 이름을 'Provider'라고 짓지만, 다른 이름을 지어줘도 무방함
- 여기서는 간단한 실습으로 'count' state와 'incrementCount' 함수를 object 형태로 value에 넘겨줄 예정
  - 'incrementCount'에 의해 'count' state가 update될 때마다, 이 custom component와 children components는 rerender되고 화면이 update됨
  - Provider component의 prop인 children에는 기존 Provider component가 감싸고 있는 App component가 들어옴

  ```js
  // context/book.js
  import { createContext, useState } from 'react';

  const BooksContext = createContext();

  function Provider({ children }) {    // Custom Provider
    const [count, setCount] = useState(0);

    const valueToShare = {   // 다른 components에 공유하고자 하는 object
      count: count,
      incrementCount: () => {
        setCount(count + 1);
      }
    };

    return (
      <BooksContext.Provider value={valueToShare}>
        {children}
      </BookContext.Provider>
    );
  }

  export { Provider };
  export default BooksContext;
  ```



## ▶ 133. More on Changing Context

> 자료: [004_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/004_-_context)

- Custom Provider가 App component를 감싸게 수정
  
  ```js
  // index.js
  import { Provider } from './context/books';
  // 생략
  root.render(
    <Provider>
      <App />
    </Provider>
  );
  ```

- BookList component에서 object 형태의 value를 받도록 수정
  - `useContext(BooksContext)`는 아래 value를 반환
  - value=`{ count, incrementCount: () => {setCount(count + 1)} }`
  - 구조분해할당을 통해 두 property를 받음
  - 버튼을 클릭할 때마다 incrementCount() 함수가 호출되고 count가 증가함
  - count state가 변하면 Provider component와 하위의 모든 component가 rerender되고 화면이 update됨

  ```js
  function BookList({ books, onDelete, onEdit }) {
    const { count, incrementCount } = useContext(BooksContext);
    // 생략
    return (
      <div className="book-list">
        {count}
        <button onClick={incrementCount}>Click</button>
        {renderedBooks}
      </div>
    );
  }
  ```



## ▶ 134. Application vs Component State

- 어떤 데이터를 Context를 통해 공유하는 것이 좋을까?
- 각 component에 있는 모든 state와 event handler를 Context에 공유할 필요는 없고, 중요한 state만 공유해도 될 것 같다
- State를 중요도에 따라 분류해보자
  - `Application State`: 많은 다른 components에서 사용되는 데이터
  - `Component State`: 일부 components에서만 사용되는 데이터
- Application State만 Context를 통해 공유하는 것이 좋을 것 같다
  - Book Project에서는 어떤 데이터가 Application State일까?
  - Application State: books state, editBookById(), deleteBookByID() 등



## ▶ 135. Refactoring to Use Context

> 자료: [006_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/006_-_context)

- App component에 있는 book state, editBookById(), deleteBookByID(), createBook(), fetchBooks()를 Provider component에 옮겨야 함
- App 하위 components로 prop을 통해 전달해주던 위 데이터는 지워도 됨
- 132번 강의에서 잠시 실습을 위해 적었던 count 관련 코드는 지움



## ▶ 136. Refactoring the App

> 자료: [007_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/007_-_context)

- App component에 있는 state와 event handler를 Provider component로 옮김
  
  ```js
  // books.js
  import axios from 'axios';

  const BooksContext = createContext();

  function Provider({ children }) {
    const [books, setBooks] = useState([]);

    const fetchBooks = async () => {
      // 생략
    };

    const editBookById = async (id, newTitle) => {
      // 생략
    };

    const deleteBookById = async (id) => {
      // 생략
    };

    const createBook = async (title) => {
      // 생략
    };

    return <BooksContext.Provider value={{}}>{children}</BooksContext.Provider>;
  }
  ```

- App component는 이제 context로부터 데이터를 받음
  - 하위 component에 전달해주던 prop은 모두 제거
  
  ```js
  import { useEffect, useContext } from 'react';
  import BooksContext from './context/books';

  function App() {
    const { fetchBooks } = useContext(BooksContext);

    useEffect(() => {
      fetchBooks();
    }, []);

    return (
      <div className="app">
        <h1>Reading List</h1>
        <BookList />
        <BookCreate />
      </div>
    );
  }
  ```



## ▶ 138. Reminder on Sharing with Context

> 자료: [008_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/008_-_context)

- Provider component에서 데이터들을 object 형태로 value prop에 넣어줘 context에 공유함
  
  ```js
  // books.js
  function Provider({ children }) {
   // 생략
    const valueToShare = {
      books,
      deleteBookById,
      editBookById,
      createBook,
      fetchBooks,
    };

    return (
      <BooksContext.Provider value={valueToShare}>
        {children}
      </BooksContext.Provider>
    );
  }
  ```

- BookCreate component는 이제 context로부터 데이터를 받음

  ```js
  import { useState, useContext } from 'react';
  import BooksContext from '../context/books';

  function BookCreate() {
    const { createBook } = useContext(BooksContext);
    // 생략
    const handleSubmit = (event) => {
      event.preventDefault();
      createBook(title);
      setTitle('');
    };
    // 생략
  }
  ```



## ▶ 138. Reminder on Sharing with Context

> 자료: [009_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/009_-_context)

- BookList component는 이제 context로부터 데이터를 받음
  
  ```js
  function BookList() {
    const { books } = useContext(BooksContext);

    const renderedBooks = books.map((book) => {
      return <BookShow key={book.id} book={book} />;
    });
    // 생략
  }
  ```



## ▶ 140. Last Bit of Refactoring

> 자료: [010_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/010_-_context)

- BookShow component는 이제 context로부터 데이터를 받음
  
  ```js
  import { useState, useContext } from 'react';
  import BooksContext from '../context/books';

  function BookShow({ book }) {
    const { deleteBookById } = useContext(BooksContext);

    const handleDeleteClick = () => {
      deleteBookById(book.id);
    };
    // 생략
  }
  ```

- BookEdit component는 이제 context로부터 데이터를 받음

  ```js
  import { useState, useContext } from 'react';
  import BooksContext from '../context/books';

  function BookEdit({ book, onSubmit }) {
    const { editBookById } = useContext(BooksContext);
    // 생략
    const handleSubmit = (event) => {
      event.preventDefault();

      onSubmit();
      editBookById(book.id, title);
    };
    // 생략
  }
  ```



## ▶ 141. A Small Taste of Reusable Hooks

> 자료: [011_-_context](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/8.Communication_Using_the_Context_System/011_-_context)

- 우리가 지금까지 사용했던 `useState()`, `useEffect()`, `useContext()`는 모두 `Hook`임
- `Hooks`: component에 추가적인 특징을 더해주는 함수
  - `useState()`: component가 state system을 사용하도록 해주는 hook function
  - `useEffect()`: component가 특정 시점에 callback함수를 호출하도록 해주는 hook function
  - `useContext()`: component가 context에 저장된 value에 접근하도록 해주는 hook function
- `Custom Hooks`: 재사용하기 위해 우리가 직접 작성한 함수
  - 보통 useState와 같은 basic hooks를 재사용함
- src 폴더 내에 hooks 폴더를 만들고 custom hook을 만들어 봄
  
  ```js
  // hooks/use-books-context.js
  import { useContext } from 'react';
  import BooksContext from '../context/books';

  function useBooksContext() {
    return useContext(BooksContext);
  }

  export default useBooksContext;
  ```

  ```js
  import useBooksContext from '../hooks/use-books-context';

  function BookList() {
    const { books } = useBooksContext();
    // 생략
  }
  ```