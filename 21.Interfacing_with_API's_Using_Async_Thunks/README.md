# ✔ '21. Interfacing with API's Using Async Thunks' 이론 정리

## ▶ 341. App Overview

-   앞으로 만들 media app 소개

    -   화면에 users list가 보여짐
    -   'Add user' 버튼을 누르면 새로운 user가 추가됨
    -   한 user를 클릭하면, 해당 user의 album list를 볼 수 있음
    -   'Add Album' 버튼을 누르면 새로운 album이 추가됨
    -   한 album을 누르면, 해당ㅌ album의 photo list를 볼 수 있음
    -   'Add photo'를 누르면 새로운 photo가 추가됨

-   이 프로젝트에서는 `JSON Server`를 통해 서버에 'list of users', 'list of albums', 'list of photos'를 저장하고 필요할 때마다 Redux에서 request를 보내 해당 데이터를 가져올 예정임

-   API를 통해 데이터를 request하기 위해서 몇 가지 염두해둬야할 것이 있음

    -   1.  user의 네트워크 상태가 좋지 않을 수도 있다는 점을 기억하자
    -   2.  로딩 이미지와 같은 data-loading experience를 반드시 제공해줘야함
    -   3.  users list 데이터를 가져올 때는 일단 plain `Redux-Toolkit`을 사용하고, albums list나 photo list 데이터를 가져올 때는 `Redux-Toolkit Query` 모듈을 사용할 예정임

-   app이 로딩될 때 아래와 같이 두 가지 방법으로 데이터를 fetching할 수 있음
    -   1.  Eager Over-Fetching
        -   app이 시작될 때, 이 앱에서 필요한 모든 데이터를 가져오는 것
        -   users/albums/photos list 데이터를 한번에 모두 가져옴
        -   문제점) 만약 데이터가 어마무시하게 많다고 했을 때, user의 네트워트 상태가 좋지 않을 경우 한번에 모든 데이터를 가지고 오는 것은 매우 많은 시간이 들 수 있음
    -   2.  Lazy Fetching
        -   일단 지금 바로 필요한 데이터만 먼저 가져오는 것
        -   app이 시작되면 먼저 users list 데이터를 먼저 가져와 나타내고, 한 user를 클릭하면 albums list 데이터를 가져와 나타내고, 한 album을 클릭하면 photos list 데이터를 가져와 나타냄
        -   이 방법을 선택해 서버에서 데이터를 가져와보자

## ▶ 342. Adding a Few Dependencies

-   create-react-app으로 media app을 생성하자
-   media 폴더에서 `@faker-js/faker`, `@reduxjs/toolkit`, `axios`, `classnames`, `json-server`, `react-icons`, `react-redux` 라이브러리를 설치하자
-   TailwindCSS docs에 나와있는 설치 방법에 따라, `TailwindCSS`도 설치하자

## ▶ 343. Initial App Boilerplate

> 자료: [003\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/003_-_media)

-   src 폴더 내 파일 전부 지우고 `index.js`, `App.js`, `index.css` 파일 추가
-   TailwindCSS docs에 나와있는 코드를 index.css에 추가

## ▶ 344. API Server Setup

> 자료: [004\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/004_-_media)

-   JSONServer API를 이용하기 위해, 일단 media app root directory에 `db.json` 파일 만들기

    -   `db.json` 파일에서 'users', 'albums', 'photos' 세 개의 properties 만들기

    ```json
    {
    	"users": [],
    	"albums": [],
    	"photos": []
    }
    ```

-   JSONServer를 실행하기 위해 `package.json` 파일의 scripts에 추가하고 터미널에서 아래 명령어를 실행하면 서버가 실행됨

    ```json
    {
      ...
      "scripts": {
        "start": "react-scripts start",
        "start:server": "json-server --watch db.json --port 3005",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject"
      },
      ...
    }
    ```

## ▶ 346. Adding a Few Components

-   media-components 폴더를 다운받아, src 폴더에 components 폴더를 만들고 그 안에 media-components 폴더 내부에 있는 `Button.js`, `Panel.js` 파일을 옮겨놓자
-   `UsersList.js`를 생성하고, App component에서 사용하자

    ```js
    import UsersList from "./components/UsersList";

    function App() {
    	return (
    		<div className="container mx-auto">
    			<UsersList />
    		</div>
    	);
    }
    ```

## ▶ 347. Creating the Redux Store

> 자료: [006\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/006_-_media)

-   src 폴더에 store 폴더를 만들고 slices 폴더를 만든 후 usersSlice.js 파일을 생성하자

    ```js
    // store/slices/usersSlice.js
    import { createSlice } from "@reduxjs/toolkit";

    const usersSlice = createSlice({
    	name: "users",
    	initialState: {
    		data: [],
    	},
    	reducers: {},
    });

    export const usersReducer = usersSlice.reducer;
    ```

-   store 폴더에 index.js 파일을 추가하자

    ```js
    // store/index.js
    import { configureStore } from "@reduxjs/toolkit";
    import { usersReducer } from "./slices/usersSlice";

    export const store = configureStore({
    	reducer: {
    		users: usersReducer,
    	},
    });
    ```

-   index.js 파일에서 react-redux 라이브러리로부터 Provider를 import해 react components에 redux store를 전달해주자

    ```js
    ...
    import { Provider } from 'react-redux';
    import { store } from './store';

    ...
    root.render(
      <Provider store={store}>
        <App />
      </Provider>
    );
    ```

## ▶ 348. Thinking About Data Structuring

-   JSONServer와 Redux Store에서 데이터 구조를 어떻게 구성하는 것이 좋을까?
    -   방법1) Denormalized Form
    -   방법2) Normalized Form

1. Denormalized Form

    - 만약 데이터 구조가 UI 구조와 유사하게 구성되어 있다면 사용하기 쉬움
    - 만약 UI 구조가 변하지 않고 고정(rock-solid)되어 있다면 사용해도 괜찮음
    - 다만, user 안에 album이 아닌 album 안에 user를 나타내는 등 UI 구조가 변할 가능성이 있다면 추천하지 않는 방법임

    ```js
    [
    	{
    		id: 50,
    		name: "Mara",
    		albums: [
    			{ id: 1, title: "Album #1" },
    			{ id: 2, title: "Album #2" },
    		],
    	},
    	{
    		id: 63,
    		name: "Ervin",
    		albums: [
    			{ id: 3, title: "Album #3" },
    			{ id: 4, title: "Album #4" },
    		],
    	},
    ];
    ```

2. Normalized Form

    - 위 구조보다 더 유연한 데이터 구조
    - 데이터간의 관계를 한눈에 알아보기 쉬움
    - 하지만, 아래로 갈수록 적어야할 코드가 많아진다는 단점이 있음
    - 많은 프로젝트가 이 방법을 사용해 데이터를 구조화함
    - 우리 프로젝트에서는 JSONServer와 Redux Store 둘 다 이 방법을 사용해 데이터를 구조화할 예정임
    - 주의) JSONServer와 Redux Store를 반드시 같은 데이터 구조로 작성할 필요는 없음

    ```js
    // List of Users
    [
    	{ id: 50, name: "Mara" },
    	{ id: 64, name: "Ervin" },
    ];
    ```

    ```js
    // List of Albums
    [
    	{ id: 1, title: "Album #1", userId: 50 },
    	{ id: 2, title: "Album #2", userId: 50 },
    	{ id: 3, title: "Album #3", userId: 64 },
    	{ id: 4, title: "Album #4", userId: 64 },
    ];
    ```

## ▶ 349. Reminder on Request Conventions

-   User 데이터의 fields

    -   id(number)
    -   name(string)

-   Album 데이터의 fields

    -   id(number)
    -   title(string)
    -   userId(number)

-   Photo 데이터의 fields

    -   id(number)
    -   url(string)
    -   albumId(number)

-   JSONServer로 request 방법

    -   user 데이터 생성

        ```
        POST http://localhost:3005/users
        {name: 'Myra'}
        ```

    -   user 데이터 가져오기

        ```
        GET http://localhost:3005/users
        ```

    -   user 데이터 삭제

        ```
        DELETE http://localhost:3005/users/1
        ```

## ▶ 350. Data Fetching Techniques

-   Redux-Toolkit에서 data fetching하는 방법

    -   방법1) Async Thunk Functions 사용
    -   방법2) Redux Toolkit Query 모듈 사용

-   users를 처리할 때, 방법1을 사용할 예정
-   albums와 photos를 처리할 때, 방법2을 사용할 예정

-   주의) Reducers에서는 절대로 requests를 하면 안됨!
    -   Reducer는 반드시 100% synchronous이어야 함
    -   Reducer에서는 API call, promise, async/await 절대 불가
    -   Reducers는 반드시 외부 변수가 아닌 넘겨받은 인자들만 사용 가능함

## ▶ 352. Adding State for Data Loading

> 자료: [010\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/010_-_media)

-   our app을 로딩할 때, users 데이터가 request되면서 화면에 loading message가 뜨게 됨

    -   이후 성공적으로 데이터를 받아오면, users list가 화면에 나타나게 됨
    -   만약 request가 실해하면, error message가 화면에 나타나게 됨

-   이처럼 users 데이터를 request할 때 UI가 변하므로 이를 state으로 만들어줄 필요가 있음

    -   usersSlice의 state에 `data`(array of objects)뿐만 아니라, `isLoading`(boolean)과 `error`(null or error object)를 넣어주자

    ```js
    const usersSlice = createSlice({
    	name: "users",
    	initialState: {
    		data: [],
    		isLoading: false,
    		error: null,
    	},
    	reducers: {},
    });
    ```

-   request하게 되면 시간에 따라 아래 `1번 → 2번` 또는 `1번 → 3번` 두 가지 상황이 발생하게 되고, request가 시작/성공/실패할 때마다 state가 변하게 됨

    -   1. user가 app을 로드할 때, users 데이터를 fetch함
    -   2. request에 성공함
    -   3. request에 실패함 (error 발생)

        ```js
        // 1) Start the request
        {
            isLoading: true,
            data: [],
            error: null
        }
        ```

        ```js
        // 2) Request success
        {
            isLoading: false,
            data: [{id:1, name: 'Myra'}],
            error: null
        }
        ```

        ```js
        // 3) Error occurred with request
        {
            isLoading: false,
            data: [],
            error: {message: 'Failed'}
        }
        ```

-   따라서, request를 할 때마다 `1번 → 2번` 또는 `1번 → 3번` 두 개의 actions를 dispatch해야 함

## ▶
