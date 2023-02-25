# ✔ '06.How To Handle Forms' 이론 정리



## ▶ 83. App Overview

- 만들고자하는 Project
  - book title을 입력하고 제출하면, reading list에 입력한 책이 저장되어 보여짐
  - reading list에 저장된 각 책에는 수정 버튼과 삭제 버튼이 있음
  - 수정 버튼을 누르면 각 책의 title을 변경할 수 있음
  - 삭제 버튼을 누르면 reading list에서 해당 책이 지워짐
- book app은 3가지 서로 다른 방법으로 구현할 예정임
  1. 지금까지 배웠던 내용을 토대로 구현
  2. Context를 사용해 리팩토링
  3. outside API를 사용해 books를 저장하는 방법으로 리팩토링



## ▶ 84. Initial Setup

> 자료: [002_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/002_-_books)

- src 폴더 안에 `index.js`, `App.js`, `BookCreate.js`, `BookList.js`, `BookShow.js`, `BookEdit.js` 총 5개의 파일을 만듦
  - App: 단순히 BookCreate, BookList components를 묶어둠
  - BookCreate: 새로운 책 생성
  - BookList: 여러 BookShow를 묶어둠
  - BookShow: 저장한 책 하나
  - BookEdit: 저장한 책 수정
- src 폴더 안에 components 폴더를 따로 만들어 `BookCreate.js`, `BookList.js`, `BookShow.js`, `BookEdit.js` 네 파일을 넣어둠



## ▶ 85. State Location

> 자료: [003_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/003_-_books)

- BookShow component는 저장된 하나의 책을 나타내기 위해서, id와 title에 대한 정보를 담은 object를 가지고 있어야 함
  - ex) `{id: 1, title: 'Harry Potter'}`
- BookList component는 저장된 여러 책에 대한 정보를 BookShow에 각각 전달해야하므로, list of objects를 가지고 있어야 함
  - ex) `[{id: 1, title: 'Harry Potter'}, {id: 2, title: 'Dark Tower'}]`
- books list는 언제든 바뀔 수 있는 UI 데이터이므로, state로 관리해야 함
- 그럼 books state는 어느 component에 위치하는게 좋을까?

### 🔹 여러 component에서 사용되는 state는 어디에 위치하는게 좋을까?

- 일단 books state가 update되면 해당 component뿐 아니라, children component 모두 rerender됨
- 따라서, books state가 필요한 모든 components를 찾은 후, 가장 가까운 공통 parent component에 state를 정의하는 것이 좋음
  - book app에서는 App component를 제외한 4가지 components가 모두 books state를 필요로 함
  - component 계층구조를 그렸을 때, 4가지 components의 가장 가까운 공통 parent component는 App component임
  - 그러므로, books state는 App component에 위치해야함

  ```js
  function App() {
    const [books, setBooks] = useState([]);

    return <div>App</div>;
  }
  ```



## ▶ 86. Reminder on Event Handlers

- CreateBook component에서 책 이름을 입력하고 submit하면 parent인 App component에 입력한 정보를 전달해줘야 함
  - 이때, event handler를 이용해서 전달 가능
- App component는 정보를 전달 받아 books state에 `{id: 1, title: 'Harry Potter'}` 형태의 objects를 추가함
- App component는 books state와 관련된 3가지의 함수가 필요
  - createBook
  - editBook
  - deleteBook



## ▶ 88. Receiving New Titles

> 자료: [005_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/005_-_books)

- App component에서 `createBook()` 함수를 만들어 CreateBook component에 전달해 줌
  
  ```js
  function App() {
  // 생략
    const createBook = (title) => {
      console.log('Need to add book with:', title);
    };

    return (
      <div>
        <BookCreate onCreate={createBook} />
      </div>
    );
  }
  ```

- Form elements는 반드시 state로 처리해야 함
- CreateBook component에서 사용자가 text input에 title을 적고 submit하면, `onCreate` prop을 이용해 title을 App component의 `createBook()` 함수로 전달해 줌
- submit한 후 text input에 입력한 title을 제거하기 위해, setter function을 이용해 빈 문자열로 초기화해 줌
  
  ```js
  function BookCreate({ onCreate }) {
    const [title, setTitle] = useState('');

    const handleChange = (event) => {
      setTitle(event.target.value);
    };

    const handleSubmit = (event) => {
      event.preventDefault();
      onCreate(title);
      setTitle('');
    };

    return (
      <div>
        <form onSubmit={handleSubmit}>
          <label>Title</label>
          <input value={title} onChange={handleChange} />
          <button>Create!</button>
        </form>
      </div>
    );
  }
  ```



## ▶ 89. Adding Styling

- 미리 만들어 놓은 `index.css` 파일을 src 폴더에 추가
- `index.js` 파일에서 `index.css` 파일 import하기
- BookCreate component에서 일부 요소와 className을 추가해 스타일 적용시키기
  
  ```js
  function BookCreate({ onCreate }) {
    // 생략
    return (
      <div className='book-create'>
        <h3>Add a Book</h3>
        <form onSubmit={handleSubmit}>
          <label>Title</label>
          <input className='input' value={title} onChange={handleChange} />
          <button className='button'>Create!</button>
        </form>
      </div>
    );
  }
  ```



## ▶ 90. Updating State

- 초기 books state를 만들때, books 변수에 빈 배열 `[]`이 할당됨
  - 이때, JS는 컴퓨터 내 빈 메모리 공간을 찾아서 배열을 넣게 되고 이를 books 변수가 참조하게 됨 
  - React는 books 변수의 참조값을 기억하고 있다가, setBooks() 함수가 호출될 때 기존 참조값과 새로운 참조값을 비교함
- books 배열에 `push` method를 사용해서 값을 넣어주면, 기존 books 배열에 값이 추가하는 것이기 때문에 books 변수의 참조값이 변하지 않음
  - **React는 기존 array/object의 참조값과 새로운 array/object의 참조값을 비교해, 만약 두 값이 같으면 rerender하지 않음**
  - 따라서, 아래 코드에서는 두 참조값이 같으므로 rerender되지 않음
  
  ```js
  // 옳지 않은 코드!!
  function App() {
    const [books, setBooks] = useState([]);

    const createBook = (title) => {
      books.push({ id: 123, title: title });
      setBooks(books);
    };
    // 생략
  }
  ```

### 🔹 Array/Object State를 Update하는 방법

1. 새로운 array/object를 만든다
2. 기존 array/object의 요소들을 복사해서 새로운 array/object에 넣어준다
3. 추가하고자 하는 데이터를 새로운 array/object 끝에 넣어준다

   - **React는 기존 array/object의 참조값과 새로운 array/object의 참조값을 비교해, 만약 두 값이 다르면 rerender하게 됨**

   ```js
   // 올바른 코드
   function App() {
     const [books, setBooks] = useState([]);
 
     const createBook = (title) => {
       const updatedBooks = [...books, { id: 123, title: title }];
       setBooks(updatedBooks);
     };
     // 생략
   }
   ```



## ▶ 91. Don't Mutate That State!

> 참고: [State Updates - Cheat Sheet](https://state-updates.vercel.app/)

- state가 Number, String, Boolean, Null, Undefined일 경우, 직접 새 값으로 바꿔주면 됨
- 만약 state가 array/object일 경우, 아래 예시처럼 직접 update하면 절대 안됨

  ```js
  // BAD Code!
  const [colors, setColors] = useState(['red', 'green']);
  
  colors.push('blue');
  ```

  ```js
  // BAD Code!
  const [colors, setColors] = useState(['red', 'green']);
  
  colors[0] = 'blue';
  ```

  ```js
  // BAD Code!
  const [fruit, setFruit] = useState([color: 'red', name: 'strawberry']);
  
  fruit.color = 'green';
  ```



## ▶ 93. [Optional] Adding Elements to the Start or End

- `Array`의 `시작` 부분에 elements `추가`하기
  
  ```js
  const [colors, setColors] = useState([]);

  const addColor = (newColor) => {
    const updatedColors = [newColor, ...colors];

    setColors(updatedColors);
  }
  ```

- `Array`의 `끝` 부분에 elements `추가`하기
  
  ```js
  const [colors, setColors] = useState([]);

  const addColor = (newColor) => {
    const updatedColors = [...colors, newColor];

    setColors(updatedColors);
  }
  ```



## ▶ 95. [Optional] Inserting Elements

- `Array`의 `중간` 부분에 elements `추가`하기
  - 특정 위치(index)에 요소 추가
  
  - `array.slice(idx1[, idx2])`: array의 idx1 이상 idx2 미만의 요소를 복사해 새로운 array로 반환
  
  ```js
  const [colors, setColors] = useState([]);

  const addColorAtIndex = (newColor, index) => {
    const updatedColors = [
      ...colors.slice(0, index), 
      newColor,
      ...colors.slice(index)
      ];

    setColors(updatedColors);
  }
  ```



## ▶ 97. [Optional] Removing Elements

- `Array`에서 `특정 값`을 가진 elements `삭제`하기
  
  - `array.filter((element[, idx]) => {})`: filer 함수 내부에는 callback 함수가 들어가 있고, 이 callback 함수는 array의 각 element와 index를 인자로 받아 True를 만족할 때만 새로운 array에 해당 element를 넣어 반환함
  
  ```js
  const [colors, setColors] = useState([]);

  const removeColor = (colorToRemove) => {
    const updatedColors = colors.filter((color) => {
      return color !== colorToRemove
    });

    setColors(updatedColors);
  }
  ```

- `Array`에서 `특정 인덱스`에 해당하는 elements `삭제`하기
  
  ```js
  const [colors, setColors] = useState([]);

  const removeColorAtIndex = (indexToRemove) => {
    const updatedColors = colors.filter((color, index) => {
      return index !== indexToRemove
    });

    setColors(updatedColors);
  }
  ```

- `Array of Objects`에서 `특정 property`에 해당하는 elements `삭제`하기
  
  ```js
  const [books, setBooks] = useState([
    { id: 1, title: 'Harry Potter' },
    { id: 2, title: 'Dark Tower' }
  ]);

  const removeBookById = (id) => {
    const updatedBooks = books.filter((book) => {
      return book.id !== id
    });

    setBooks(updatedBooks);
  }
  ```



## ▶ 99. [Optional] Modifying Elements

- `Array of Objects`에서 `특정 property`에 해당하는 elements `변경`하기
  
  - `array.map((element[, idx]) => {})`: map 함수 내부에는 callback 함수가 들어가 있고, 이 callback 함수는 array의 각 element와 index를 인자로 받아 작성된 코드에 따라 적용한 후 새로운 array에 해당 element를 넣어 반환함
  - JS의 objects의 key값들은 unique해야 함
  - 만약 objects에 중복된 key가 들어있으면, 앞에 위치한 key는 뒤에 위치함 key에 덮어씌워짐
  
  ```js
  const [books, setBooks] = useState([
    { id: 1, title: 'Harry Potter' },
    { id: 2, title: 'Dark Tower' }
  ]);

  const updateBookById = (id, newTitle) => {
    const updatedBooks = books.map((book) => {
      if (book.id === id) {
        return { ...book, title: newTitle };
      }

      return book;
    });

    setBooks(updatedBooks);
  }
  ```



## ▶ 100. [Super Optional] Why the Special Syntax?

- 99번 코드에서처럼 `Array of Objects` 내 특정 object를 update할 때에도, 새로운 object를 만들고 그 안에 기존 요소와 새로운 요소를 추가해야함
  - 비록 기존 object를 직접 수정해서 재사용해도 이미 array 자체를 새로 만들었기때문에 books state가 변경되고 rerender 되지만, 이렇게 하면 나중에 child component에서 optimization할 때 bug를 발생시킬 수 있음
- 즉, 아래 코드처럼 object를 직접 수정하면 안됨
  - child component에서 해당 object를 prop으로 받게 되면, 이전 참조값과 현 참조값이 차이가 없기 때문에 rerender하지 않게 됨

  ```js
  // BAD Code!!
  const updateBookById = (id, newTitle) => {
    const updatedBooks = books.map((book) => {
      if (book.id === id) {
        book.title = 'Dark Tower 2';
      }

      return book;
    });
  }
  ```



## ▶ 102. [Optional] Adding, Changing, or Removing Object Properties

- `Object`에서 특정 property `추가` 또는 `변경`하기

  ```js
  const [fruit, setFruit] = useState({
    color: 'red',
    name: 'apple'
  });

  const changeColor = (color) => {
    const updatedFruit = {...fruit, color: color};

    setFruit(updatedFruit);
  }
  ```

- `Object`에서 특정 property `삭제`하기
  - JS의 구조분해할당 이용

  ```js
  const [fruit, setFruit] = useState({
    color: 'red',
    name: 'apple'
  });

  const changeColor = (color) => {
    const { color, ...rest } = fruit;

    setFruit(rest);
  }
  ```



## ▶ 104. Adding a Book, For Real!

> 자료: [019_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/019_-_books)

- 이제 book app에서 books state에 title을 추가해보자
  
  ```js
  function App() {
    // 생략
    const createBook = (title) => {
      const updatedBooks = [...books, { id: 123, title }];
      setBooks(updatedBooks);
    };  
    // 생략
  }
  ```



## ▶ 105. Generating Random ID's

> 자료: [020_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/020_-_books)

- 보통 id는 백엔드에서 생성해서 전달해 주지만, 현 과정에서는 우리가 직접 랜덤으로 생성함
  - id는 반드시 unique 해야하지만, 랜덤으로 숫자를 선택하면 아무리 랜덤 범위를 넓게 잡아도 가끔 중복될 수도 있음
  
  ```js
  function App() {
    // 생략
    const createBook = (title) => {
      const updatedBooks = [
        ...books,
        {
          id: Math.round(Math.random() * 9999),
          title,
        },
      ];
      setBooks(updatedBooks);
    };  
    // 생략
  }
  ```



## ▶ 106. Displaying the List

- App component에서 BookList component로 books state 전달
  - 스타일링을 위해 className도 추가
  
  ```js
  function App() {
    // 생략
    return (
      <div className='app'>
        <BookList books={books}>
        <BookCreate onCreate={createBook} />
      </div>
    );
  }
  ```

- BookList component에서 각 BookShow component에 book object 전달
  - list elements는 반드시 key prop이 있어야 함
  - 스타일링을 위해 className도 추가
  
  ```js
  function BookList({ books }) {
    const renderedBooks = books.map((book) => {
      return <BookShow key={book.id} book={book} />
    });

    return (
      <div className='book-list'>
        {renderedBooks}
      </div>
    );
  }
  ```

- BookShow component는 book object 전달받아 title을 화면에 나타냄
  - 스타일링을 위해 className도 추가
  
  ```js
  function BookShow({ book }) {
    return (
      <div className='book-show'>
        {book.title}
      </div>
    );
  }
  ```



## ▶ 107. Deleting Records

> 자료: [022_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/022_-_books)

- BookShow component에서 삭제 버튼을 누르면, Books state에서 특정 id에 해당하는 book을 제거해야 함
  - child component에서 parent component로 communicate하기 위해선 event handler를 prop을 통해 전달해야 함
- App component에서 books state에 특정 id에 해당하는 book을 제거하는 함수인 `deleteBookById()`을 생성
  - 이를 BookShow component까지 prop을 이용해서 전달해줘야 하기 때문에, 그 중간 계층에 위치하는 BookList component도 마찬가지로 prop을 통해 전달해줘야 함

  ```js
  function App() {
    // 생략
    const deleteBookById = (id) => {
      const updatedBooks = books.filter((book) => {
        return book.id !== id;
      });

      setBooks(updatedBooks);
    };
    // 생략
    return (
      <div className="app">
        <BookList books={books} onDelete={deleteBookById} />
        <BookCreate onCreate={createBook} />
      </div>
    );
  }
  ```

  ```js
  function BookList({ books, onDelete }) {
    const renderedBooks = books.map((book) => {
      return <BookShow onDelete={onDelete} key={book.id} book={book} />;
    });
    // 생략
  }
  ```

  ```js
  function BookShow({ book, onDelete }) {
    const handleClick = () => {
      onDelete(book.id);
    };

    return (
      <div className="book-show">
        {book.title}
        <div className="actions">
          <button className="delete" onClick={handleClick}>
            Delete
          </button>
        </div>
      </div>
    );
  }
  ```



## ▶ 108. Toggling Form Display

> 자료: [023_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/023_-_books)

- BookShow component는 기본적으로 화면에 book의 title을 보여주다가, 수정 버튼을 누르면 BookEdit component를 보여줌
  - 수정 버튼을 누를 때마다 화면에 content가 달라지므로, boolean 타입의 showEdit State를 만들어 처리할 수 있음
  
  ```js
  function BookShow({ book, onDelete }) {
    const [showEdit, setShowEdit] = useState(false);
    // 생략
    const handleEditClick = () => {
      setShowEdit(!showEdit);
    };

    let content = <h3>{book.title}</h3>;
    if (showEdit) {
      content = <BookEdit />;
    }

    return (
      <div className="book-show">
        <div>{content}</div>
        <div className="actions">
          <button className="edit" onClick={handleEditClick}>
            Edit
          </button>
          // 생략
        </div>
      </div>
    );
  }
  ```



## ▶ 109. Default Form Values

- BookEdit component에 form을 만들어줌
  - form elements는 반드시 state로 관리해야 함
  - 기존 title값을 input 란에 넣어주기 위해, BookShow component로부터 book object를 prop을 통해 전달받음

  ```js
  function BookEdit({ book }) {
    const [title, setTitle] = useState(book.title);

    const handleChange = (event) => {
      setTitle(event.target.value);
    };

    const handleSubmit = (event) => {
      event.preventDefault();

      console.log(title);
    };

    return (
      <form onSubmit={handleSubmit} className="book-edit">
        <label>Title</label>
        <input className="input" value={title} onChange={handleChange} />
        <button className="button is-primary">Save</button>
      </form>
    );
  }
  ```



## ▶ 110. Updating the Title

> 자료: [025_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/025_-_books)

- BookEdit component에서 App component로 수정된 title을 전달해야 함
  - child component에서 parent component로 communicate하기 위해선 event handler를 prop을 통해 전달해야 함
- App component에서 books state에 특정 id에 해당하는 book을 수정하는 함수인 `editBookById()`을 생성
  - 이를 BookEdit component까지 prop을 이용해서 전달해줘야 하기 때문에, 그 중간 component인 BookList와 BookShow도 마찬가지로 prop을 통해 전달해줘야 함
  
  ```js
  function App() {
    // 생략
    const editBookById = (id, newTitle) => {
      const updatedBooks = books.map((book) => {
        if (book.id === id) {
          return { ...book, title: newTitle };
        }

        return book;
      });

      setBooks(updatedBooks);
    };
    // 생략
    return (
      <div className="app">
        <BookList onEdit={editBookById} books={books} onDelete={deleteBookById} />
        // 생략
      </div>
    );
  }
  ```

  ```js
  function BookList({ books, onDelete, onEdit }) {
    const renderedBooks = books.map((book) => {
      return (
        <BookShow onEdit={onEdit} onDelete={onDelete} key={book.id} book={book} />
      );
    });
    // 생략
  }
  ```

  ```js
  function BookShow({ book, onDelete, onEdit }) {
    // 생략
    let content = <h3>{book.title}</h3>;
    if (showEdit) {
      content = <BookEdit onEdit={onEdit} book={book} />;
    }
    // 생략
  }
  ```

  ```js
  function BookEdit({ book, onEdit }) {
    // 생략
    const handleSubmit = (event) => {
      event.preventDefault();

      onEdit(book.id, title);
    };
    // 생략
  }
  ```



## ▶ 111. Closing the Form on Submit

> 자료: [026_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/026_-_books)

- BookEdit component에서 새로운 title을 submit하면, 다시 edit form이 아닌 title이 떠야함
  - BookShow에서 event handle인 `handleSubmit()`를 만든 후, prop을 통해 BookEdit component로 전달해주자
  - 새로운 title을 submit할 때, handleSubmit()을 호출시키는 방법으로 해결해보자
  - 다음 강의에서 알게 되겠지만, 이 방법은 사실 좋지 못한 방법임!

  ```js
  function BookShow({ book, onDelete, onEdit }) {
    // 생략
    const handleSubmit = () => {
      setShowEdit(false);
    };

    let content = <h3>{book.title}</h3>;
    if (showEdit) {
      content = <BookEdit onSubmit={handleSubmit} onEdit={onEdit} book={book} />;
    }
    // 생략
  }
  ```

  ```js
  function BookEdit({ book, onEdit, onSubmit }) {
    // 생략
    const handleSubmit = (event) => {
      event.preventDefault();

      onEdit(book.id, title);
      onSubmit();
    };
    // 생략
  }
  ```



## ▶ 112. A Better Solution!

- 111번과 같은 방법은 좋지 못한 방법임
  - 이유: BookEdit component에서 하나의 submit event가 일어날 때, onEdit과 onSubmit 함수 두 개나 호출이 되기 때문
- 차라리 BookEdit component에서 하나의 submit event가 일어날 때, BookShow component에서 onEdit 함수를 호출하는게 나을 수 있음
  - 즉, BookShow component에서 submit을 처리하면 onEdit 함수 하나만 호출해도 됨
  - showEdit state가 BookShow component에 있기 때문에 바로 바꿔줄 수 있음



## ▶ 113. Collapsing Two Handlers into One

> 자료: [028_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/028_-_books)

- 112번에서 고안한 방법을 실행해보자
  - 더이상 BookEdit component까지 onEdit prop을 전달할 필요가 없음
  
  ```js
  function BookShow({ book, onDelete, onEdit }) {
    // 생략
    const handleSubmit = (id, newTitle) => {
      setShowEdit(false);
      onEdit(id, newTitle);
    };

    let content = <h3>{book.title}</h3>;
    if (showEdit) {
      content = <BookEdit onSubmit={handleSubmit} book={book} />;
    }
    // 생략
  }
  ```

  ```js
  function BookEdit({ book, onSubmit }) {
    // 생략
    const handleSubmit = (event) => {
      event.preventDefault();

      onSubmit(book.id, title);
    };
    // 생략
  }
  ```



## ▶ 114. Adding Images

> 자료: [029_-_books](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/6.How_To_Handle_Forms/029_-_books)
> 실습: [Section6: How To Handle Forms](https://codesandbox.io/s/section6-how-to-handle-forms-xzr6ib?file=/src/App.js)

- 각 BookShow component마다 서로 다른 image를 넣어야 함
- [Picsum](https://picsum.photos/) 사이트를 이용해 간단하게 랜덤 이미지를 불러올 수 있음
- img 요소의 src의 속성값으로 `https://picsum.photos/200/300`를 넣으면 200 x 300 크기의 랜덤 이미지를 가져올 수 있음
  - 단, 위 URL을 사용하면 콘솔창에서 `disable chache`의 체크 유무에 따라 계속 동일한 사진을 가지고 올 수 있음
- 따라서, BookShow component마다 유일한 사진을 넣고 싶다면 `https://picsum.photos/seed/{unique한 값}/200/300`을 사용해 특정 URL을 호출하는 것이 좋음

  ```js
  function BookShow({ book, onDelete, onEdit }) {
    // 생략
    return (
      <div className="book-show">
        <img alt="books" src={`https://picsum.photos/seed/${book.id}/300/200`} />
        // 생략
      </div>
    );
  }
  ```