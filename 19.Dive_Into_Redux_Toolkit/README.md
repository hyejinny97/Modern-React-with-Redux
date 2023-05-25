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

## ▶ 302. Understanding Slices

-   `useReducer` hook을 사용하면, 직접 action object types를 작성하고 Reducer 내부의 Switch문을 통해 type에 해당하는 case문을 일일이 작성해 코드를 실행해야 했음

-   `Slices`는 `useReducer` hook을 사용했을 때의 귀찮은 boilerplate code 작성 작업을 대체해줌 (`Redux-Toolkit`의 이점)

    -   즉, slice 내부 'reducers' property에는 'mini-reducers'이 존재하는데, 각 mini-reducer는 `useReducer` hook을 사용했을 때의 case문처럼 생각하면 됨
    -   slice는 이 'mini-reducers'를 하나의 big reducer로 합쳐 하나의 'reducer' property를 만들게 됨
    -   따라서, store를 생성할 때 songs key의 value로 적은 `songsSlice.reducer`가 바로 'mini-reducers'를 하나로 합친 big reducer를 의미함

-   `Slice` 내부의 'mini-reducers'는 state를 mutate하는 것처럼 적어도 상관없고, 따로 return 키워드를 적어 반환해 줄 필요도 없음

    -   이유) `Redux-Toolkit` 자동으로 immer를 포함하고 있기 때문

-   `Slice` 내부의 각 'mini-reducers'를 호출하기 위해서는, dispatch 보낼때 action object type을 `song/addSong`과 같이 `sliceName/mini-reducer명` 형식으로 적어야 함

-   주의) 'mini-reducers'에서 첫번째 인자로 받는 state는 하나의 big state가 아닌, 각 reducer가 담당하고 있는 piece of state임

## ▶ 303. Understanding Action Creators

-   `Slices`는 자동으로 아래와 같은 'action creator' functions를 생성해줌

    -   'action creator' functions는 각 'mini-reducer'에 해당하는 action object를 반환해주기 때문에, 우리가 직접 action object를 작성할 필요없이 action create function으로부터 특정 action object를 받아 dispatch를 보내면 됨 (`Redux-Toolkit`의 이점)
    -   'action creator' functions는 각 'mini-reducer'와 동일한 이름을 가지고 있음
    -   첫번째 인자로 payload를 받음

    ```js
    // 'action creator' functions
    {
      addSong(payload) {
        return {
          type: 'songs/addSong',
          payload: payload,
        };
      },
      removeSong(payload) {
        return {
          type: 'songs/removeSong',
          payload: payload,
        };
      }
    }
    ```

-   'action creator' functions는 slice 내부에 'actions' property에 저장되어 있음

    ```js
    console.log(songsSlice.actions); // {addSong: f, removeSong: f}
    console.log(songsSlice.actions.addSong("some song!"));
    // {type: "songs/addSong", payload: "some song!"}
    ```

-   실제 slice의 'actions' property를 이용해 dispatch를 보내보자

    ```js
    // src/store/index.js
    ...
    const startingState = store.getState();
    console.log(JSON.stringify(startingState));  // {"songs": []}

    store.dispatch(songsSlice.actions.addSong("some song!"));

    const finalState = store.getState();
    console.log(JSON.stringify(finalState));  // {"songs": ["some song!"]}
    ```

## ▶ 304. Connecting React to Redux

-   `React-Redux` 라이브러리를 통해 Redux Store와 React를 연결시켜줄 수 있음
    -   `React-Redux`에는 Provider component가 존재하는데, Context API의 Provider처럼 생각하면 됨
    -   즉, `React-Redux` Provider component에 의해 React App 내 모든 components는 Redux Store (특히, dispatch function과 state)에 접근 가능함

### 🔹 `React-Redux` 라이브러리를 통해 React와 Redux를 연결하는 방법

1. Store을 생성한 파일에서 store 변수를 export한다.
2. root 'index.js' 파일로 store를 import한다.
3. `react-redux` 라이브러리로부터 Provider를 import한다.
4. App component를 Provider로 감싼 후, store를 prop으로 전달해준다.

    ```js
    // src/store/index.js
    ...
    export { store }
    ```

    ```js
    // index.js
    import { Provider } from 'react-redux';
    import { store } from './store';

    ...
    root.render(
      <Provider store={store}>
        <App />
      </Provider>
    )
    ```

## ▶ 305. Updating State from a Component

-   실제로 SongPlayList component에서 Redux로 dispatch를 보내 state를 update해보자

### 🔹 React에서 Redux Store의 state를 update하는 방법

1. 내가 update하고자 하는 바로 그 state를 관리하는 slice에 'mini-reducer'를 추가한다.

    ```js
    // src/store/index.js
    const songsSlice = createSlice({
    	name: "song",
    	initialState: [],
    	reducers: {
    		addSong(state, action) {
    			state.push(action.payload);
    		},
    	},
    });
    ```

2. slice가 자동으로 생성한 action creator function을 export한다.

    ```js
    // src/store/index.js
    export const { addSong } = songsSlice.actions;
    ```

3. React App 내에서 dispatch를 보내고자하는 component를 찾는다.

    ```js
    function SongPlaylist() {
      // Get list of songs
      const songPlaylist = [];

      const handleSongAdd = (song) => {
        // Add song to list of songs
      };
      ...
    }
    ```

4. action creator function와 `react-redux`로부터 `useDispatch` hook을 import한다.

    ```js
    import { addSong } from "../store";
    import { useDispatch } from "react-redux";

    function SongPlaylist() {}
    ```

5. `useDispatch` hook을 호출해 Redux Store의 dispatch function을 얻는다.

    ```js
    function SongPlaylist() {
    	const dispatch = useDispatch();
    }
    ```

6. user가 특정 반응을 할 때, action creator function으로부터 action object를 얻어 dispatch로 보내준다.

    ```js
    function SongPlaylist() {
      ...
      const handleSongAdd = (song) => {
        dispatch(addSong(song));
      };
      ...
    }
    ```

## ▶ 306. Accessing State in a Component

-   SongPlayList component에서 update한 state를 가져와 render해보자

### 🔹 React에서 Redux Store의 state를 가져오는 방법

1. 특정 state에 접근이 필요한 component를 찾는다.
2. `react-redux`로부터 `useSelector` hook을 import한다.

    ```js
    import { useDispatch, useSelector } from "react-redux";

    function SongPlaylist() {}
    ```

3. `useSelector` hook의 인자로 selector function을 넣어 호출한다.

    - selector function은 entire state를 인자로 받아 해당 component가 원하는 piece of state만을 반환해주는 함수임

    ```js
    function SongPlaylist() {
      const songPlaylist = useSelector((state) => {
        return state.songs;
      });

      ...
    }
    ```

4. state를 사용하자! state가 update될 때마다 component는 자동으로 rerender된다.

## ▶ 307. Removing Content

-   위와 동일한 방법으로 state에 저장된 song을 제거하고 결과를 rerender해보자

1. 내가 update하고자 하는 바로 그 state를 관리하는 slice에 'mini-reducer'를 추가한다.

    ```js
    // src/store/index.js
    const songsSlice = createSlice({
    	name: "song",
    	initialState: [],
    	reducers: {
        ...
    		removeSong(state, action) {
    			const index = state.indexOf(action.payload);
          state.splice(index, 1);
    		},
    	},
    });
    ```

2. slice가 자동으로 생성한 action creator function을 export한다.

    ```js
    // src/store/index.js
    export const { addSong, removeSong } = songsSlice.actions;
    ```

3. React App 내에서 dispatch를 보내고자하는 component를 찾는다.

    ```js
    function SongPlaylist() {
      ...
      const handleSongRemove = (song) => {
        // Remove song to list of songs
      };
      ...
    }
    ```

4. action creator function와 `react-redux`로부터 `useDispatch` hook을 import한다.

    ```js
    import { addSong, removeSong } from "../store";
    import { useDispatch } from "react-redux";

    function SongPlaylist() {}
    ```

5. `useDispatch` hook을 호출해 Redux Store의 dispatch function을 얻는다.

6. user가 특정 반응을 할 때, action creator function으로부터 action object를 얻어 dispatch로 보내준다.

    ```js
    function SongPlaylist() {
      ...
      const handleSongRemove = (song) => {
        dispatch(removeSong(song));
      };
      ...
    }
    ```

## ▶ 308. Practice Updating State!

-   위 방식과 동일하게 MoviePlayList component에서도 state에 영화를 추가할 수 있도록 해보자

    ```js
    // src/store/index.js
    const moviesSlice = createSlice({
    	name: "movie",
    	initialState: [],
    	reducers: {
    		addMovie(state, action) {
    			state.push(action.payload);
    		},
    	},
    });
    ...
    export const { addMovie } = moviesSlice.actions;
    ```

    ```js
    import { useDispatch } from "react-redux";
    import { addMovie } from "../store";

    function MoviePlaylist() {
      const dispatch = useDispatch();

      const handleMovieAdd = (movie) => {
        dispatch(addMovie(movie));
      };
      ...
    }
    ```

## ▶ 309. Practice Accessing State!

-   MoviePlayList component에서 state에 접근할 수 있도록 해보자

    ```js
    import { useDispatch, useSelector } from "react-redux";

    function MoviePlaylist() {
      ...
      const moviePlaylist = useSelector((state) => {
        return state.movies;
      });
      ...
    }
    ```

## ▶ 310. Even More State Updating!

-   MoviePlayList component에서 state에서 영화를 삭제할 수 있도록 해보자

    ```js
    // src/store/index.js
    const moviesSlice = createSlice({
      name: "movie",
      initialState: [],
      reducers: {
        ...
        removeMovie(state, action) {
          const index = state.indexOf(action.payload);
          state.splice(index, 1);
        },
      },
    });
    ...
    export const { addMovie, removeMovie } = moviesSlice.actions;
    ```

    ```js
    import { addMovie, removeMovie } from "../store";

    function MoviePlaylist() {
      ...
      const handleMovieRemove = (movie) => {
        dispatch(removeMovie(movie));
      };
      ...
    }
    ```

## ▶ 311. Resetting State

-   reset 버튼을 클릭하면 movie playlist와 song playlist를 모두 삭제되게 해보자

    -   일단, movie playlist만 삭제되게 해보자
    -   moviesSlice에서 새로운 mini-reducer를 생성할 때, state가 empty array가 되도록 해야함
    -   Redux-Toolkit에 의해 자동으로 immer가 포함되므로, 기존 state를 직접 mutate 가능함
    -   단순히 `state = [];`을 적으면, 기존 state를 조작하는 것이 아니기 때문에 immer가 이 코드를 감지하지 못하고 새로운 state가 생성되게 되므로 옳지 않은 방법임
    -   따라서, 기존 state를 직접 mutate하지 못할 경우 `return` 키워드를 사용해 state가 변경되기 원하는 값을 반환해주면 Toolkit이 이 값을 new state로 할당해줌

    ```js
    // src/store/index.js
    const moviesSlice = createSlice({
      name: "movie",
      initialState: [],
      reducers: {
        ...
        reset(state, action) {
          return [];
        },
      },
    });
    ...
    export const { addMovie, removeMovie, reset } = moviesSlice.actions;
    ```

    ```js
    import { useDispatch } from "react-redux";
    import { reset } from "./store";

    export default function App() {
      const dispatch = useDispatch();

      const handleResetClick = () => {
        dispatch(reset());
      };
      ...
    }
    ```

## ▶ 312. Multiple State Updates

-   reset 버튼을 클릭했을 때 movie playlist와 song playlist가 모두 삭제되게 하는 방법은 여러 개 존재
    -   1. movieSlice의 reset function에서 songs playlist를 update하면 되지 않을까?
    -   2. 2개의 action을 dispatch 보내는 방법
    -   3. songsSlice가 존재하는 reset action을 watch하는 방법
    -   4. 새롭고 독립적인 reset action을 생성해 두 slices가 이것을 watch하는 방법

1. 방법1) movieSlice의 reset function에서 songs playlist를 update하면 되지 않을까?

    - mini-reducer가 인자로 받는 state는 entire state가 아닌 해당 slice가 관리하는 piece of state이기 때문에 **절대 불가**!

2. 방법2) 2개의 action을 dispatch 보내는 방법

    - 물론 이 방법으로 작동 가능하지만, 'Redux' 방식은 아님
    - 보통 dispatch function을 가능한 적게 호출하여 state를 update하는 것이 좋음

    ```js
    dispatch(resetMovies());
    dispatch(resetSongs());
    ```

## ▶ 313. Understanding Action Flow

3. 방법3) songsSlice가 존재하는 reset action을 watch하는 방법

    - 각 slice 내 mini-reducer는 slice에 의해 하나의 combined reducer function이 생성되게 됨
    - 특정 action object로 dispatch를 보내면, combined reducer는 오로지 자신이 관리하고 있는 state에 대한 type을 가진 actions만 신경을 씀
        - 즉, Songs Reducer는 'song/addSong', 'song/removeSong' type을 가진 action만 신경쓰는 반면, Movies Reducer는 'movie/addMovie', 'movie/removeMovie', 'movie/reset' type을 가진 action만 신경씀
    - 단, action이 dispatch될 때 이 action object는 store에 있는 모든 reducer function을 호출하게 됨
        - 모든 reducer function이 호출되지만, combined reducer는 단지 오로지 자신이 관리하고 있는 state에 대한 type을 가진 actions만 신경을 쓸 뿐임!
        - 즉, 'movie/reset' type을 가진 action으로 dispatch를 보내면 movies reducer 뿐만 아니라 song reducers도 호출됨
    - 만약, Combined songs reducer가 'movie/reset' type을 가진 action을 추가적으로 신경쓰게 한다면 어떨까?

## ▶ 314. Watching for Other Actions

-   실제, Combined songs reducer가 'movie/reset' type을 가진 action를 watch하게 해 해당 type의 action이 dispatch 됐을 때 songs state가 empty array가 되도록 하자

    -   slice 내부의 `extraReducers()` property을 통해 자신이 관리하고 있는 state에 대한 actions 이외에 다른 action을 watch할 수 있음
    -   `extraReducers()` function은 builder를 인자로 받음
    -   `builder.addCase(action type, mini-reducer)`
        -   첫번째 인자: watch하고자 하는 action type명
        -   두번째 인자: 해당 type의 action이 dispatch 됐을 때 호출할 mini-reducer

    ```js
    // src/store/index.js
    const songsSlice = createSlice({
      name: "song",
      initialState: [],
      reducers: {
        ...
      },
      extraReducers(builder) {
        builder.addCase("movie/reset", (state, action) => {
          return [];
        });
      }
    });
    ```

## ▶ 315. Getting an Action Creator's Type

-   `builder.addCase()`에 첫 번째 인자로 type명을 직접 적는 것보다 moviesSlice의 해당 action creator로부터 type명을 가져오는 것이 버그의 가능성을 줄일 수 있는 방법임

    -   방법1) `moviesSlice.actions.reset().type`
    -   방법2) `moviesSlice.actions.reset.toString()`
    -   방법3) `moviesSlice.actions.reset`

    ```js
    // src/store/index.js
    const songsSlice = createSlice({
      ...
      extraReducers(builder) {
        builder.addCase(moviesSlice.actions.reset, (state, action) => {
          return [];
        });
      }
    });
    ```

## ▶ 316. Manual Action Creation

-   위 세번째 방법은 songs reducer가 movies reducer에 의존한다는 문제점을 지닌다.

    -   만약 moviesSlice의 name을 변경하거나 전체를 삭제하거나 reset function의 이름을 변경한다면, `moviesSlice.actions.reset`을 watch하고 있는 songs reducer는 제대로 작동하지 않게 되는 문제가 있음
    -   이를 해결하기 위해 아래 네번째 방법을 채택하는 것을 추천함

4. 방법4) 새롭고 독립적인 reset action을 생성해 두 slices가 이것을 watch하는 방법

    - `Redux-Toolkit` 라이브러리로부터 `createAction()` function을 import해서 사용하자
    - `createAction(type명)`는 action creator function을 반환하는 함수임
    - `{type: "app/reset"}` 형태의 action object를 직접 생성해 combined songs reducer와 combined movies reducer가 이 action을 watch하도록 하자

    ```js
    // src/store/index.js
    import { createAction } from "@reduxjs/toolkit";

    export const reset = createAction("app/reset");
    console.log(reset());  // {type: "app/reset", payload: undefined}

    const moviesSlice = createSlice({
      name: "movie",
      initialState: [],
      reducers: {
        addMovie(state, action) {
          ...
        },
        removeMovie(state, action) {
          ...
        },
      },
      extraReducers(builder) {
        builder.addCase(reset, (state, action) => {
          return [];
        })
      }
    });

    const songsSlice = createSlice({
      name: "song",
      initialState: [],
      reducers: {
        addSong(state, action) {
          ...
        },
        removeSong(state, action) {
          ...
        },
      },
      extraReducers(builder) {
        builder.addCase(reset, (state, action) => {
          return [];
        });
      }
    });
    ```

## ▶ 317. File and Folder Structure

-   store/index.js 파일 안에 너무 많은 코드가 섞여 있으니 이를 다른 파일로 분리할 필요가 있음
-   file/folder structure 방법에는 크게 아래 두가지 방법이 있음

    -   1.  organize by `Function`
    -   2.  organize by `Feature`

    ```
    <Function에 따라 분리>
    src
      ↳ components
                ↳ MoviesList.js
                ↳ SongsList.js
      ↳ store
            ↳ actions
                    ↳ actions.js
            ↳ slices
                    ↳ moviesSlice.js
                    ↳ songsSlice.js
            ↳ index.js
      ↳ App.js
      ↳ index.js
    ```

    ```
    <Feature에 따라 분리>
    src
      ↳ movies
            ↳ MoviesList.js
            ↳ moviesSlice.js
      ↳ songs
            ↳ SongsList.js
            ↳ songsSlice.js
      ↳ store
            ↳ actions.js
            ↳ index.js
      ↳ App.js
      ↳ index.js
    ```

-   위 두 방법에 의한 folder structure는 반드시 지켜야할 사항이 아닌, 회사나 각 개인마다 다를 수 있음

    -   Redux docs는 'Feature' approach를 추천
    -   Redux Toolkit docs는 직접적으로 추천하진 않음
    -   하지만 'Feature' approach을 채택해 structure를 구성했을 경우, 나중에 Redux Toolkit에서 'circular import'할 때 어려움을 겪을 수 있음
    -   따라서, 이 app에서는 'Function' approach를 적용할 예정임

-   'Function' approach를 적용했을 때, MoviesList/SongsList component가 slice 파일에서 action creator functions을 직접 import하는 방식은 지저분한 코드를 만들 수 있음

    -   대신, store/index.js이 각 slice로부터 action creator functions를 import해 여기서 store object와 함께 action creator functions을 export해주자
    -   즉, `store/index.js` 파일이 Redux와 관련된 모든 것(store, action creators)에 접근할 수 있는 중심점이 되는 것임
    -   이렇게 하면 나중에 'circular import' 문제를 해결하는데에도 큰 도움이 됨

## ▶ 318. Refactoring the Project Structure

-   `Function`에 따라 store 폴더 structure를 구성해보자

    ```js
    // store/slices/songsSlice.js
    import { createSlice } from "@reduxjs/toolkit";
    import { reset } from "../actions";

    const songsSlice = createSlice({
      ...
    });

    export const { addSong, removeSong } = songsSlice.actions;
    export const songsReducer = songsSlice.reducer;
    ```

    ```js
    // store/slices/moviesSlice.js
    import { createSlice } from "@reduxjs/toolkit";
    import { reset } from "../actions";

    const moviesSlice = createSlice({
      ...
    });

    export const { addMovie, removeMovie } = moviesSlice.actions;
    export const moviesReducer = moviesSlice.reducer;
    ```

    ```js
    // store/actions.js
    import { createAction } from "@reduxjs/toolkit";

    export const reset = createAction("app/reset");
    ```

    ```js
    // store/index.js
    import { configureStore } from "@reduxjs/toolkit";
    import { songsReducer, addSong, removeSong } from "./slices/songsSlice";
    import { moviesReducer, addMovie, removeMovie } from "./slices/moviesSlice";
    import { reset } from "./actions";

    const store = configureStore({
    	reducer: {
    		songs: songsReducer,
    		movies: moviesReducer,
    	},
    });

    export { store, reset, addSong, removeSong, addMovie, removeMovie };
    ```

## ▶ 319. Link to Completed Project

> [CodeSandbox Completed Project Link](https://codesandbox.io/s/completed-media-project-zyz2mx)
