# ✔ '19. Dive Into Redux Toolkit' 이론 정리

## ▶ 295. Into the World of Redux

-   Redux는 useReducer처럼 유사한 technique을 사용해서 state를 관리하는 라이브러리임

### 🔹 `useReducer` Hook vs `Redux` Library

#### ☝ 공통점

1. action object
2. Dispatch function
3. Reducer에 의해 새로운 State를 반환

#### ✌ 차이점

1. `useReducer`는 React world 안에서 모든 state를 만들고 관리하는 반면, `Redux`는 'Redux Store'이라는 것을 생성해서 그곳에 state를 따로 만들고 관리함

    - 따라서, 각 components는 redux store에 연결해 state에 접근할 수 있음

    ```
    CounterPage + useReducer → Button
                             ↳ Panel
    ```

    ```
    CounterPage → Button    | Redux Store (Dispatch → Reducer → State)
                ↳ Panel     |
    ```

2. Redux 라이브러리는 React으로부터 독립적인 존재임

    - `React-Redux`: React와 Redux를 연결시켜주는 라이브러리
    - 따라서, `React-Redux`와 같은 라이브러리를 사용해 communicate해야함

    ```
    Redux Store ⇆ React-Redux ⇆ React
    ```

3. `useReducer`는 state를 관리하기 위해 하나의 reducer function을 사용하는 반면, `Redux`는 state를 관리하기 위해 여러개의 reducer function을 사용함

    - 다시말해, state의 각 부분을 여러 reducer가 담당

    ```
    <useReducer>
    action → Dispatch function → Reducer → State
    ```

    ```
    <Redux>
    action → Dispatch function → users list를 관리하는 Reducer   → State
                               ↳ videos list를 관리하는 Reducer
                               ↳ messages list를 관리하는 Reducer
    ```

    ```js
    // Redux에서의 State (아래 State를 세부분으로 나눠 각 Reducer가 관리)
    {
      users: {
        currentUser: {id: 23}
      },
      videos: {
        playlist: [
          {title: 'Surfing Video'}
        ]
      },
      messages: {
        unread: [
          {content: 'Msg me'}
        ]
      }
    }
    ```

## ▶ 296. Redux vs Redux Toolkit

-   Redux가 인기있는 라이브러리인 이유
    -   Central point of initiating any change of state
    -   어느 state를 바꾸려고 하든 항상 Dispatch function을 호출해야하기 때문에, 중앙 집권형 state 관리를 할 수 있어 어느 state를 왜 바꿔야하는지 이해하기 쉬워짐

### 🔹 Redux 사용 방법

1. 오로지 `Redux`만 사용

    - 단점) action object를 작성해 dispatch 호출하고, reducer function 안에서 switch문을 사용해 type별로 서로 다른 state를 update하는 과정을 직접 일일이 해야하는 번거로움이 있음 (boilerplate code)
    - 오래된 방식으로 추천하지 않음

    ```
    Redux ⇆ React App
    ```

2. `Redux` + `Redux Toolkit(RTK)` 사용

    - Redux Toolkit은 plain Redux를 감싸, Redux를 좀 더 쉽게 사용할 수 있게 해주는 라이브러리임
    - **Redux Toolkit은 action type 생성 과정을 단순하게 해줌**
    - 즉, 직접 작정할 필요가 없어 boilerplate code를 피할 수 있음
    - 최근 추천되는 방식임

    ```
    Redux ⇆ Redux Toolkit ⇆ React App
    ```

## ▶ 297. App Overview

> [CodeSandbox Boilerplate Link](https://codesandbox.io/s/rtk-360ssw)

-   App은 Movie list와 Song list를 보여주고, 각각 item을 추가하거나 삭제할 수 있음
-   reset 버튼을 눌러 두 list에 있는 item들을 모두 삭제할 수 있음

    ```
    App → MoviePlayList
        ↳ SongPlayList
    ```

    ```js
    function MoviePlaylist() {
      // Get list of movies
      const moviePlaylist = [];

      const handleMovieAdd = (movie) => {
        // Add movie to list of movies
      };
      const handleMovieRemove = (movie) => {
        // Remove movie from list of movies
      };
      ...
    }
    ```

    ```js
    function SongPlaylist() {
      // Get list of songs
      const songPlaylist = [];

      const handleSongAdd = (song) => {
        // Add song to list of songs
      };
      const handleSongRemove = (song) => {
        // Remove song from list of songs
      };
      ...
    }
    ```

    ```js
    function App() {
      const handleResetClick = () => {
        // Reset all movies and all songs
      };
      ...
    }
    ```

## ▶ 298. The Path Forward

-   Redux Store에는 아래와 같이 state에 movies, songs properties가 담길 것임

    -   각 properties는 각각의 reducer function이 관리하게 됨
    -   따라서, dispatch function이 호출되면 두 reducer function의 인자로 action object가 들어가 자신이 담당한 state를 변경하고 반환하면 Redux Store는 이를 하나로 합쳐 새로운 state를 반환하게 됨

    ```js
    {
      movies: ['Inception'],
      songs: ['Shake It Off'],
    }
    ```

## ▶ 299. Implementation Time!

-   일단 바로 src 폴더에 store 폴더를 만들고 index.js 파일을 생성해 Redux Toolkit을 import해서 코드를 일부 작성해보자

    ```js
    // src/store/index.js
    import { configureStore, createSlice } from "@reduxjs/toolkit";

    const songsSlice = createSlice({
    	name: "song",
    	initialState: [],
    	reducers: {
    		addSong(state, action) {
    			state.push(action.payload);
    		},
    		removeSong(state, action) {
    			//
    		},
    	},
    });

    const store = configureStore({
    	reducer: {
    		songs: songsSlice.reducer,
    	},
    });
    ```

    ```js
    // index.js
    import "./store";
    ```

## ▶ 300. Understanding the Store

-   Redux Store는 하나의 our app 내의 모든 states를 포함하는 object임
-   요즘엔 일반적으로 Redux Store와 직접 데이터를 주고 받지 않고, 대신 `React-Redux` 라이브러리를 사용해 Redux Store를 handle함
-   그럼에도 불구하고 Redux Store와 직접 데이터를 주고 받으려면, 아래 방법을 통해 실행할 수 있음

    -   직접 action을 dispatch하는 방법

        ```js
        store.dispatch({ type: "songs/addSong" });
        ```

    -   store에 state를 보기 위한 방법

        ```js
        store.getState();
        ```

-   State object에서 `key`는 store이 생성될 때 결정이 되고, `value`는 각각의 reducers에 의해서 만들어짐

-   직접 Redux Store에 action을 dispatch해보고 state를 확인해보자

    ```js
    // src/store/index.js
    ...
    const startingState = store.getState();
    console.log(JSON.stringify(startingState));  // {"songs": []}

    store.dispatch({
      type: "song/addSong",
      payload: "New Song!!!"
    });

    const finalState = store.getState();
    console.log(JSON.stringify(finalState));  // {"songs": ["New Song!!!"]}
    ```

## ▶ 301. The Store's Initial State

-   `Slices`는 자동으로 reducers와 action types를 만들어줌

### 🔹 Slices의 역할 (3가지)

1. initial state를 정의함

    - `configureStore()` function에 의해 store가 생성될 때, state 각 key에 해당하는 slice에서 'initialState' property를 찾아 초기값을 설정해주게 됨

2. 'mini-reducers'을 하나의 big reducer로 합쳐줌

3. 'action creator' functions를 생성함

## ▶

> [CodeSandbox Completed Project Link](https://codesandbox.io/s/completed-media-project-zyz2mx)
