# ✔ '09.Deeper Dive into Hooks!' 이론 정리

## ▶ 142. Return to useEffect

> 자료: [useEffect-return-value 관련 실습](https://codesandbox.io/s/useeffect-return-value-gwanryeon-silseub-c7iem4?file=/src/App.js)

- section 8에서 진행한 Books project 결과를 보면, react server 터미널창에 에러가 뜬 것을 확인할 수 있음
  - App component의 useEffect와 관련된 오류임을 알 수 있음
  - 따라서 이번 section에선 useEffect에 관해 발생할 수 있는 흔한 버그를 공부할 예정
- useEffect의 arrow function이 value를 return하는 것에 대해서 학습하려고 함
- useEffect를 사용해서 화면 어디를 클릭해도 현재 counter가 콘솔창에 나타나도록 구현
  - 하지만, 아래 코드에서는 아무리 increment 버튼을 클릭해도 콘솔창에 계속 0만 찍힘
  
  ```js
  import { useState, useEffect } from "react";

  function App() {
    const [counter, setCounter] = useState(0);

    useEffect(() => {
      // 옳지 못한 코드!!
      document.body.onclick = () => {
        console.log(counter);
      };
    }, []);

    return (
      <div>
        <button onClick={() => setCounter(counter + 1)}>+ Increment</button>
        <div>Count: {counter}</div>
      </div>
    );
  }

  export default App;
  ```



## ▶ 144. Understanding the Issue

- 143번 코드에서 콘솔창에 계속 0만 찍히는 이유
  - 1st render때 counter에 0이 할당되고, useEffect가 호출되어 body에 click event 함수가 실행됨
  - 이때, counter에 할당된 0이 콘솔창에 찍히게 됨
  - 첫번째 이후 rerender 땐, counter에 계속 1을 더해나가 새로운 값이 할당되지만 useEffect는 호출되지 않아서 첫번째 render 당시 counter 값인 0이 계속 찍힘



## ▶ 145. Applying the Fix

### 🔹 Stale Variable Reference

- useEffect가 변수를 참조하는 function을 포함할 때마다 항상 버그는 발생함
- Create-React-App은 `ESLint rule`을 포함하고 있어 버그를 찾을 수 있게 도움
  - 142번 코드에서 useEffect 두번째 인자인 [] 아래에 노란줄 나타나고, 여기에 마우스를 올리면 에러를 확인할 수 있음
  - 'React Hook useEffect has a missing dependency: 'onClick'. Either include it or remove the dependency array.'
- 하지만, 여기서 아래처럼 ESLint rule을 따르면 더 많은 버그들을 발생시킴
  - rerender할 때마다 counter state 값이 바껴 useEffect도 계속 호출되는데, 이때마다 useEffect 안에 있는 callback함수가 계속해서 새로 만들어짐

  ```js
  // 옳지 못한 코드!!
  useEffect(() => {
    document.body.onclick = () => {
      console.log(counter);
    };
  }, [counter]);
  ```



## ▶ 146. ESLint is Good, but be Careful!

> 자료: [004_-_ue](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/9.Deeper_Dive_into_Hooks/004_-_ue)

- Book Project에서도 ESLint rule에 따라 코드를 수정해보자
  - 브라우저를 보면 문제없이 작동하는 것처럼 보이지만, 개발자 도구를 열어서 네트워크란을 보면 끊임없이 JSON Server로 request를 보내는 것을 알 수 있음
  - rerender할 때마다 Provider component 내의 fetchBooks() 함수가 새로 만들어지기 때문에, useEffect가 rerender때마다 호출되고 그 안에 있는 fetchBooks() 함수가 호출되어 JSON Server로 request를 보내는 과정이 반복됨
  - 1st render: fetchBooks() 생성, useEffect() 실행, fetchBooks() 호출, books state 변화 → 2nd rerender: fetchBooks() 생성, useEffect() 실행, fetchBooks() 호출, books state 변화 → 반복...

  ```js
  function App() {
    const { fetchBooks } = useContext(BooksContext);

    // 옳지 못한 코드!!
    useEffect(() => {
      fetchBooks();
    }, [fetchBooks]);
    // 생략
  }
  ```

- 즉, 여기서는 위처럼 ESLint rule을 따르면 더 많은 버그가 발생함
  


## ▶ 147. Stable References with useCallback

> 자료: [005_-_ue](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/9.Deeper_Dive_into_Hooks/005_-_ue)

### 🔹 useCallback

- React에게 특정 function이 실제로 변하지 않음을 알려주는 Hook function
- 145번과 146번처럼 useEffect나 비슷한 상황에 관한 bug를 고쳐줌
- useCallback() 함수의 인자
  - 첫 번째 인자: 함수
  - 두 번째 인자: 빈 배열 또는 요소가 있는 배열
- 아래처럼 stableFetchBooks 변수에 useCallback() 함수를 할당해보자
  - render될 때마다 fetchBooks는 계속 새롭게 생성됨
  - useCallback() 함수는 첫 번째 인자로 받은 함수를 참조할 뿐, 절대 실행시키지 않음
  - 1st render 시, useCallback() 함수는 방금 생성된 fetchBooks() 함수를 참조함
  - 2nd render 시
    - useCallback() 함수의 두 번째 인자 = `[]`일 경우, useCallback() 함수는 1st render때 생성된 **기존** fetchBooks() 함수를 참조함
    - useCallback() 함수의 두 번째 인자 = `[element]`이고 요소의 값이 변한 경우, useCallback() 함수는 **새롭게 생성된** fetchBooks() 함수를 참조함

  ```js
  function Provider({ children }) {
    // 생략
    const fetchBooks = async () => {
      // 생략
    };

    const stableFetchBooks = useCallback(fetchBooks, []);

    const valueToShare = {
      // 생략
      stableFetchBooks,
    };
    // 생략
  }
  ```

  ```js
  function App() {
    const { stableFetchBooks } = useContext(BooksContext);

    useEffect(() => {
      stableFetchBooks();
    }, [stableFetchBooks]);
    // 생략
  }
  ```

- 따라서, stableFetchBooks 참조값은 일정하게 유지되므로 useEffect는 1st render때만 실행되고 그 이후에는 실행되지 않게 됨
- 즉, useCallback을 사용하면 146번 때처럼 끊임없이 JSON Server로 request를 보내는 것을 막을 수 있음



## ▶ 148. Fixing Bugs with useCallback

> 자료: [006_-_ue](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/9.Deeper_Dive_into_Hooks/006_-_ue)

- 147번 코드를 다시 리팩토링
  
  ```js
  import { createContext, useState, useCallback } from 'react';

  function Provider({ children }) {
    // 생략
    const fetchBooks = useCallback(async () => {
      const response = await axios.get('http://localhost:3001/books');

      setBooks(response.data);
    }, []);
    // 생략
    const valueToShare = {
      // 생략
      fetchBooks,
    };
    // 생략
  }
  ```

  ```js
  function App() {
    const { fetchBooks } = useContext(BooksContext);

    useEffect(() => {
      fetchBooks();
    }, [fetchBooks]);
    // 생략
  }
  ```



## ▶ 149. useEffect Cleanup Functions

> 자료: [useEffect-return-value 관련 실습](https://codesandbox.io/s/useeffect-return-value-gwanryeon-silseub-c7iem4?file=/src/App.js)

- useEffect의 첫 번째 인자로 들어오는 callback function는 반드시 또다른 function을 return 해야함
  - number, string는 반환할 수 없음
  - async/await은 자동으로 promise를 반환하기 때문에 callback function으로 async/await을 사용해선 안됨

  ```js
  // Number 불가!
  function App() {
    useEffect(() => {
      return 5
    }, []);
  }
  ```

  ```js
  // Sting 불가!
  function App() {
    useEffect(() => {
      return 'hi'
    }, []);
  }
  ```

  ```js
  // Async/Await 불가!
  function App() {
    useEffect(async () => {
      const res = swait axios.get('..')
    }, []);
  }
  ```

  ```js
  // Function 가능
  function App() {
    useEffect(() => {
      
      return () => console.log('hi');
    }, []);
  }
  ```

- `CleanUp Function`: useEffect의 callback function에서 return하는 function
  - **다음 rerender 시 useEffect가 호출되는 상황일 때, useEffect가 호출되기 전 CleanUp Function이 먼저 호출됨**
  - 1st render: useEffect() 실행되면서 callback function 실행, CleanUp function 반환(호출되진 않음) --counter state 변화-→ 2nd render: 1st render때 반환한 CleanUp function 호출, useEffect() 실행되면서 callback function 실행, CleanUp function 반환(호출되진 않음) --counter state 변화-→ 반복...
  
  ```js
  function App() {
    const [counter, setCounter] = useState(0);

    useEffect(() => {
      // 옳지 못한 코드!!
      document.body.onclick = () => {
        console.log(counter);
      };

      const cleanUp = () => {
        console.log('cleanup')
      }

      return cleanUp;
    }, [counter]);

    return (
      <div>
        <button onClick={() => setCounter(counter + 1)}>+ Increment</button>
        <div>Count: {counter}</div>
      </div>
    );
  }
  ```



## ▶ 150. The Purpose of Cleanup Functions

> 자료: [useEffect-return-value 관련 실습](https://codesandbox.io/s/useeffect-return-value-gwanryeon-silseub-c7iem4?file=/src/App.js)

- 실제론 event를 처리하기위해 `document.body.onClick = 함수` 방법을 사용하기보단 `addEventListener()` 함수를 사용함
  - 하지만, 아래처럼 코드를 작성하면 버튼을 클릭할 때마다 counter 개수만큼 addEventListener가 생성됨
  - 따라서 빈 화면을 클릭하면 counter 개수만큼 콘솔창에 0 ~ counter 수가 찍힘

  ```js
  function App() {
      const [counter, setCounter] = useState(0);

      useEffect(() => {
        const listener = () => {
          console.log(counter);
        };
        document.body.addEventListener('click', listener);
        // 생략
      }, [counter]);
      // 생략
    }
  ```

- 따라서, CleanUp function을 사용해서 rerender 시 useEffect가 호출될 때마다 이전 addEventListener는 제거해줘야 함
  - 1st render: useEffect() 실행되면서 addEventListener 생성, removeEventListener 반환(호출되진 않음) --counter state 변화-→ 2nd render: 1st render때 반환한 removeEventListener 호출, useEffect() 실행되면서 addEventListener 생성, removeEventListener 반환(호출되진 않음) --counter state 변화-→ 반복...
  
  ```js
  function App() {
      const [counter, setCounter] = useState(0);

      useEffect(() => {
        const listener = () => {
          console.log(counter);
        };
        document.body.addEventListener('click', listener);

        return () => {
          document.body.removeEventListener('click', listener);
        };
      }, [counter]);
      // 생략
    }
  ```