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

## ▶ 353. Understanding Async Thunks

1. Start the request일 때의 작동 방식

    -   1. Async Thunk Function이 데이터 로딩동안, 자동으로 아래 action을 dispatch 보냄
    -   2. `pending` type의 action을 dispatch 보냄 (pending = data fetching 과정 중)
    -   3. 해당 reducer가 호출되어 이때의 state로 바뀜

2. Request success일 때의 작동 방식

    -   1. Async Thunk Function이 request 성공 후, 자동으로 아래 action을 dispatch 보냄
    -   2. `fulfilled` type의 action을 dispatch 보냄 (fulfilled = data fetching 성공)
    -   3. 해당 reducer가 호출되어 이때의 state로 바뀜

3. Error occurred with request일 때의 작동 방식
    -   1. Async Thunk Function이 request 실패 후, 자동으로 아래 action을 dispatch 보냄
    -   2. `rejected` type의 action을 dispatch 보냄 (rejected = data fetching 실패)
    -   3. 해당 reducer가 호출되어 이때의 state로 바뀜

## ▶ 354. Steps for Adding a Thunk

> 자료: [012\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/012_-_media)

-   async thunk function은 request를 보내고, 그동안 자동으로 pending type의 action을 dispatch하고, request 결과에 또다시 fulfilled/rejected type의 action을 dispatch함

### 🔹 Async Thunk를 생성하는 방법(1)

1. thunk 작성을 위한 새 파일을 생성하고, request 목적에 맞게 이름 짓는다.

    - store 폴더 안에 thunks 폴더 생성 후, `fetchUsers.js` 파일을 만들자

2. thunk function을 생성하고, 첫 번째 인자로 request 목적에 맞게 'base type'를 넣어준다.

    - base type: action type에서의 앞부분
    - 'users/fetch'로 넣어주게 되면 앞으로 request 로딩/성공/실패 시 각각 'users/fetch/pending', 'users/fetch/fulfilled', 'users/fetch/rejected' type의 actions를 보내게 됨

3. thunk function 두 번째 인자인 'payloadCreator(callback function)'에서 request를 보내고 reducer가 필요해 하는 데이터를 return 해준다.

    ```js
    import { createAsyncThunk } from "@reduxjs/toolkit";
    import axios from "axios";

    const fetchUsers = createAsyncThunk("users/fetch", async () => {
    	const response = await axios.get("http://localhost:3005/users");

    	return response.data;
    });

    export { fetchUsers };
    ```

## ▶ 355. More on Adding Thunks

> 자료: [013\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/013_-_media)

### 🔹 Async Thunk를 생성하는 방법(2)

4. Slice에서 extraReducers를 추가하고 해당 thunk function에 의해 만들어진 action types를 watch한다.

    - fetchUsers는 자체적으로 actions type을 가지고 있기 때문에, slice에서 extraReducers를 생성할 때 fetchUsers로부터 type을 가지고 오면 됨
    - fetchUsers async thunk function에 의해 request가 성공하게 되면, `{type: 'users/fetch/fulfilled', payload: 반환한 response 데이터}` 형태의 action으로 dispatch 보내게 됨
    - fetchUsers async thunk function에 의해 request가 실패하게 되면, `{type: 'users/fetch/rejected', error: error object}` 형태의 action으로 dispatch 보내게 됨

    ```js
    fetchUsers.pending === "users/fetch/pending";
    fetchUsers.fulfilled === "users/fetch/fulfilled";
    fetchUsers.rejected === "users/fetch/rejected";
    ```

    ```js
    import { fetchUsers } from "../thunks/fetchUsers";

    const usersSlice = createSlice({
    	name: "users",
    	initialState: {
    		data: [],
    		isLoading: false,
    		error: null,
    	},
    	extraReducers(builder) {
    		builder.addCase(fetchUsers.pending, (state, action) => {
    			state.isLoading = true;
    		});
    		builder.addCase(fetchUsers.fulfilled, (state, action) => {
    			state.isLoading = false;
    			state.data = action.payload;
    		});
    		builder.addCase(fetchUsers.rejected, (state, action) => {
    			state.isLoading = false;
    			state.error = action.error;
    		});
    	},
    });
    ```

## ▶ 356. Wrapping up the Thunk

> 자료: [014\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/014_-_media)

### 🔹 Async Thunk를 생성하는 방법(3)

5. 'store/index.js' 파일로부터 thunk function을 export한다.

    ```js
    // store/index.js
    export * from "./thunks/fetchUsers";
    ```

6. user가 특정 event를 일으킬 때, thunk function이 실행되도록 dispatch를 보낸다.

    - UsersList component가 처음 render될 때, fetchUsers를 호출해 dispatch를 보내야 함
    - useEffect의 두번째 인자로 array 안에 dispatch를 적지 않아도 되지만, 단순히 ESLint에 의한 error를 없애기 위해 넣어둠

    ```js
    import { useEffect } from "react";
    import { useDispatch } from "react-redux";
    import { fetchUsers } from "../store";

    function UsersList() {
    	const dispatch = useDispatch();

    	useEffect(() => {
    		dispatch(fetchUsers());
    	}, [dispatch]);

    	return "Users List";
    }
    ```

## ▶ 357. Using Loading State

> 자료: [015\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/015_-_media)

-   UsersList component에서 users state를 가져와 사용하자

    -   isLoading, data, error state의 상태에 따라 서로 다른 UI를 화면에 보여줘야 함

    ```js
    import { useDispatch, useSelector } from 'react-redux';

    function UsersList() {
        const dispatch = useDispatch();
        const { isLoading, data, error } = useSelector((state) => {
            return state.users;
        });

        ...
        if (isLoading) {
            return <div>Loading...</div>;
        }

        if (error) {
            return <div>Error fetching data...</div>;
        }

        return <div>{data.length}</div>;
    }
    ```

## ▶ 358. Adding a Pause for Testing

> 자료: [016\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/016_-_media)

-   Loading과 관련된 UI를 개발하기 위해서, 잠시 fetched data를 반환하는 시간을 늦춰야할 필요가 있음

    -   개발이 끝나면, 아래 코드를 삭제할 예정

    ```js
    const fetchUsers = createAsyncThunk("users/fetch", async () => {
    	const response = await axios.get("http://localhost:3005/users");

    	// DEV ONLY!!!
    	await pause(1000);

    	return response.data;
    });

    // DEV ONLY!!!
    const pause = (duration) => {
    	return new Promise((resolve) => {
    		setTimeout(resolve, duration);
    	});
    };
    ```

## ▶ 359. Adding a Skeleton Loader

> 자료: [017\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/017_-_media)

-   loading 상태일 때 단순히 화면에 'Loading...' 글자를 띄우는 것이 아닌 skeleton loader를 띄워보자

    -   skeleton loader는 여러 개의 회색 박스가 나열된 형태로 약간의 애니메이션(shimmer effect)이 추가되어 있어 사용자들로 하여금 로딩 중임을 인식하게 해줌
    -   Skeleton component를 만들어, 원하는 회색 박스 개수인 times prop를 넘겨주도록 하자

    ```js
    // components/Skeleton.js
    import classNames from "classnames";

    function Skeleton({ times }) {
    	const boxes = Array(times)
    		.fill(0)
    		.map((_, i) => {
    			return <div key={i} />;
    		});

    	return boxes;
    }

    export default Skeleton;
    ```

    ```js
    import Skeleton from './Skeleton';

    function UsersList() {
        ...

        if (isLoading) {
            return <Skeleton times={6} />;
        }
        ...
    }
    ```

## ▶ 360. Animations with TailwindCSS

-   TailwindCSS를 사용해 Skeleton Loader에 애니메이션을 넣어보자

    -   TailwindCSS에는 우리가 원하는 형태의 애니메이션을 제공해주지 않으므로, `tailwind.config.js` 파일 내 extend property에 직접 custom한 애니메이션을 넣어 사용해보자
    -   Skeleton component가 className prop을 받아 원하는 크기대로 skeleton loader를 만들 수 있게 해보자

    ```js
    // tailwind.config.js
    module.exports = {
    	content: ["./src/**/*.{js,jsx,ts,tsx}"],
    	theme: {
    		extend: {
    			keyframes: {
    				shimmer: {
    					"100%": { transform: "translateX(100%)" },
    				},
    			},
    			animation: {
    				shimmer: "shimmer 1.5s infinite",
    			},
    		},
    	},
    	plugins: [],
    };
    ```

    ```js
    import classNames from "classnames";

    function Skeleton({ times, className }) {
    	const outerClassNames = classNames(
    		"relative",
    		"overflow-hidden",
    		"bg-gray-200",
    		"rounded",
    		"mb-2.5",
    		className
    	);
    	const innerClassNames = classNames(
    		"animate-shimmer",
    		"absolute",
    		"inset-0",
    		"-translate-x-full",
    		"bg-gradient-to-r",
    		"from-gray-200",
    		"via-white",
    		"to-gray-200"
    	);

    	const boxes = Array(times)
    		.fill(0)
    		.map((_, i) => {
    			return (
    				<div key={i} className={outerClassNames}>
    					<div className={innerClassNames} />
    				</div>
    			);
    		});

    	return boxes;
    }
    ```

    ```js
    function UsersList() {
        ...
        if (isLoading) {
            return <Skeleton times={6} className="h-10 w-full" />;
        }
        ...
    }
    ```

## ▶ 361. Rendering the List of Users

> 자료: [019\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/019_-_media)

-   성공적으로 users 데이터를 fetch했을 때, 화면에 users 데이터가 나타나게 하자

    ```js
    function UsersList() {
    ...
    const renderedUsers = data.map((user) => {
        return (
        <div key={user.id} className="mb-2 border rounded">
            <div className="flex p-2 justify-between items-center cursor-pointer">
            {user.name}
            </div>
        </div>
        );
    });

    return <div>{renderedUsers}</div>;
    }
    ```

## ▶ 362. Creating New Users

> 자료: [020\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/020_-_media)

-   UsersList component에서 'Add User' 버튼을 클릭하면 faker API로부터 랜덤하게 이름을 받아 server로 POST 보내야 함

    -   아래와 같이 'POST method'/'URL'/'request body'를 통해 JSONServer로 request를 보내는 것을 성공하면, `{id: 2, name 'Ervin'}` 데이터를 포함한 Response를 받게 됨
    -   이후, response로 받은 데이터로 users data state를 update해줘야함

    ```
    POST http://localhost:3005/users
    {name: 'Ervin'}
    ```

    ```js
    // store/thunks/addUser.js
    import { createAsyncThunk } from "@reduxjs/toolkit";
    import axios from "axios";
    import { faker } from "@faker-js/faker";

    const addUser = createAsyncThunk("users/add", async () => {
    	const response = await axios.post("http://localhost:3005/users", {
    		name: faker.name.fullName(),
    	});

    	return response.data;
    });

    export { addUser };
    ```

-   usersSlice는 addUser thunk function이 dispatch 보낸 action types을 받기 위해 extraReducers를 추가해줘야 함

    ```js
    import { addUser } from '../thunks/addUser';

    const usersSlice = createSlice({
    name: 'users',
    initialState: {
        data: [],
        isLoading: false,
        error: null,
    },
    extraReducers(builder) {
        ...
        builder.addCase(addUser.pending, (state, action) => {
        state.isLoading = true;
        });
        builder.addCase(addUser.fulfilled, (state, action) => {
        state.isLoading = false;
        state.data.push(action.payload);
        });
        builder.addCase(addUser.rejected, (state, action) => {
        state.isLoading = false;
        state.error = action.error;
        });
    },
    });
    ```

-   store/index.js에서 addUser async thunk function를 export해서 components에서 사용할 수 있게 해주자

    ```js
    // store/index.js
    export * from "./thunks/addUser";
    ```

    ```js
    import { fetchUsers, addUser } from '../store';
    import Button from './Button';

    function UsersList() {
        ...
        const handleUserAdd = () => {
            dispatch(addUser());
        };
        ...
        return (
            <div>
            <div className="flex flex-row justify-between m-3">
                <h1 className="m-2 text-xl">Users</h1>
                <Button onClick={handleUserAdd}>+ Add User</Button>
            </div>
            {renderedUsers}
            </div>
        );
    }
    ```

## ▶ 363. Unexpected Loading State

-   현재 'Add user' 버튼을 누르면, loading 되는동안 전체 화면이 깜빡거리는 것을 확인할 수 있음

    -   isLoading state가 True일 때, Skeleton Loader가 render되게 했기 때문임
    -   이처럼 사용자를 추가할 때마다, 화면 전체에 gray box들이 나타나게 하는 것은 좋지 않아 보임
    -   대신, 'Add user' 버튼 내부에 loading spinner가 나타나게끔 하는 것이 좋은 방법인 것 같음

-   또한 특정 user의 삭제 버튼을 눌렀을 때도, 해당 버튼 자체에 loading spinner가 나타나게끔 구현해야 함
-   그렇다면, 어떤 request에 대한 loading 상태인지를 어떻게 구분해서 각 loading별로 서로 다른 로딩 UI를 화면에 나타나게 할 수 있을까?
    -   `'Fine-Grained' Loading state`를 이용해 보자
    -   각 request마다 분리된 state 변수들을 유지
    -   일반적으로, 두 가지 options으로 구현 가능
        -   1. redux store에서 isLoading과 error state를 빼서 components에서 해당 state를 직접 구현
        -   2. `Redux-Toolkit Query` 모듈과 유사한 방식을 사용

## ▶ 364. Strategies for Fine-Grained Loading State

-   `'Fine-Grained' Loading state` 사용을 위한 아래 두 가지 options이 존재

1. loading/error state를 Redux Store에서 Components로 옮김

    - 즉, UsersList와 UsersListItem component에 아래 무수히 많은 state를 추가해 줌
    - 주의) Redux를 사용해도 따로 components에 state를 생성해도 상관 없음!!

    ```js
    // Redux Store
    {
    	users: {
    		data: [{ id: 1, name: "Myra" }],
    	}
    }
    ```

    ```
    App → UsersList → UsersListItem
            👇                 👇
    isLoadingUsers state      isDeletingUsers state
    loadingUsersError state   deletingUsersError state
    isCreatingUsers state
    creatingUsersError state
    ```

2. Redux Store에 requests state를 추가하고, components가 자신이 생성한 requests를 기억할 수 있게 함으로써 현재 request의 상태를 추적 가능하게 하자

    - `dispatch(thunk function)`는 `requestId`를 포함한 Promise 객체를 반환함
    - UsersList component가 dispatch 보낼 때 requestId를 기억해 뒀다가, Redux Store 내 requests state로부터 해당 request를 추적해 현재 어떤 상태(pending/fulfilled/rejected)인지 확인 가능
    - 이 방식은 `Redux-Toolkit Query (RTK Query)`와 유사한 방식임
    - 위 첫번째 option보다 이상적인 방법임
    - 다시 한번 말하지만, loading state를 관리하는 `Redux-Toolkit Query (RTK Query)`를 사용하는 것을 권장함

    ```js
    // Redux Store
    {
        users: {
            data: [{ id: 1, name: "Myra" }],
        },
        requests: [
            {id: requestId, status: 'pending'},
            {id: requestId, status: 'fulfilled'},
            {id: requestId, status: 'rejected', error: error},
        ]
    }
    ```

    ```
    App → UsersList
            👇
        requestId
    ```

## ▶ 365. Local Fine-Grained Loading State

> 자료: [023\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/023_-_media)

-   위에서 얘기한 첫번째 option대로 먼저 간단하게 구현해보자
-   fetchUsers async thunk function을 호출해 dispatch를 보내고, request가 fulfilled/rejected 됐는지 어떻게 알 수 있을까?

    -   `dispatch(fetchUsers())`는 `Promise {<pending>, requestId: 'WQHe...', ...}` 객체를 반환함
    -   반환한 Promise 객체를 통해 fulfilled/rejected 여부를 알 수 있음
    -   주의) `dispatch(fetchUsers())`가 반환한 Promise 객체는 다른 일반적인 Promise 객체와는 약간 다르게 작동함
        -   원래, promise가 fulfilled 됐을 때 `then()` method가 작동하고, rejected 됐을 때는 `catch()` method가 작동됨
        -   하지만, 이 promise에 연결된 `then()` method는 request가 성공/실패 했을 때 모두 호출되고, `then()` method의 인자로 fulfilled 또는 rejected action object가 들어가게 됨
    -   따라서, `dispatch(fetchUsers())`가 반환한 Promise 객체를 `unwrap()` method를 사용해 일반적인 promise 객체로 다시 반환한 후, then/catch method를 연결해야 함

    ```js
    import { useEffect, useState } from 'react';

    function UsersList() {
        const [isLoadingUsers, setIsLoadingUsers] = useState(false);
        const [loadingUsersError, setLoadingUsersError] = useState(null);
        const dispatch = useDispatch();
        const { data } = useSelector((state) => {
            return state.users;
        });

        useEffect(() => {
            setIsLoadingUsers(true);
            dispatch(fetchUsers())
            .unwrap()
            .then(() => {
                console.log('SUCCESS');
            })
            .catch(() => {
                console.log('FAIL!!!!');
            });
        }, [dispatch]);

        ...

        if (isLoadingUsers) {
            return <Skeleton times={6} className="h-10 w-full" />;
        }

        if (loadingUsersError) {
            return <div>Error fetching data...</div>;
        }
        ...
    }
    ```

## ▶ 366. More on Loading State

> 자료: [024\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/024_-_media)

-   `dispatch(fetchUsers())`가 fulfilled 됐을 때, rejected 됐을 때 각각 실행할 코드를 넣어보자

    -   fulfilled 됐든지 rejected 됐든지 항상 isLoadingUsers는 false가 되어야 하므로, `finally()` method를 사용해 중복 코드를 없애보자

    ```js
    function UsersList() {
        ...

        useEffect(() => {
            setIsLoadingUsers(true);
            dispatch(fetchUsers())
            .unwrap()
            .catch((err) => setLoadingUsersError(err))
            .finally(() => setIsLoadingUsers(false));
        }, [dispatch]);
        ...
    }
    ```

## ▶ 367. Handling Errors with User Creation

> 자료: [025\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/025_-_media)

-   위 코드처럼 UsersList component에 isCreatingUser/creatingUserError state도 생성해 사용해보자

    ```js
    function UsersList() {
        ...
        const [isCreatingUser, setIsCreatingUser] = useState(false);
        const [creatingUserError, setCreatingUserError] = useState(null);
        ...

        const handleUserAdd = () => {
            setIsCreatingUser(true);
            dispatch(addUser())
            .unwrap()
            .catch((err) => setCreatingUserError(err))
            .finally(() => setIsCreatingUser(false));
        };

        ...

        return (
            <div>
            <div className="flex flex-row justify-between m-3">
                <h1 className="m-2 text-xl">Users</h1>
                {isCreatingUser ? (
                'Creating User...'
                ) : (
                <Button onClick={handleUserAdd}>+ Add User</Button>
                )}
                {creatingUserError && 'Error creating user...'}
            </div>
            {renderedUsers}
            </div>
        );
    }
    ```

## ▶ 368. Creating a Reusable Thunk Hook

> 자료: [026\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/026_-_media)

-   UsersList component 내부에 로딩/에러에 관련된 state와 function들이 복잡하게 존재하므로, 이를 따로 빼내어 custom hook을 만들어 사용해보자

    ```js
    // src/hooks/use-thunk.js
    import { useState, useCallback } from "react";
    import { useDispatch } from "react-redux";

    export function useThunk(thunk) {
    	const [isLoading, setIsLoading] = useState(false);
    	const [error, setError] = useState(null);
    	const dispatch = useDispatch();

    	const runThunk = useCallback(
    		(arg) => {
    			setIsLoading(true);
    			dispatch(thunk(arg))
    				.unwrap()
    				.catch((err) => setError(err))
    				.finally(() => setIsLoading(false));
    		},
    		[dispatch, thunk]
    	);

    	return [runThunk, isLoading, error];
    }
    ```

    ```js
    import { useThunk } from '../hooks/use-thunk';

    function UsersList() {
        const [doFetchUsers, isLoadingUsers, loadingUsersError] =
            useThunk(fetchUsers);
        const [doCreateUser, isCreatingUser, creatingUserError] = useThunk(addUser);
        const { data } = useSelector((state) => {
            return state.users;
        });

        useEffect(() => {
            doFetchUsers();
        }, [doFetchUsers]);

        const handleUserAdd = () => {
            doCreateUser();
        };
        ...
    }
    ```

## ▶ 369. Creating a Fetch-Aware Button Component

> 자료: [027\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/027_-_media)

-   'Add User' 버튼을 클릭해 로딩 중일 때, 해당 버튼에 'Creating User...'가 아닌 spinning loader가 뜨게 하자

    -   Button component에 loading prop을 추가해, 로딩 중일 때 버튼 안에 spinning loader가 뜨게 하고 어떠한 event도 작동하지 않도록 막아보자

    ```js
    function UsersList() {
        ...

        return (
            <div>
            <div className="flex flex-row justify-between m-3">
                <h1 className="m-2 text-xl">Users</h1>
                <Button loading={isCreatingUser} onClick={handleUserAdd}>
                + Add User
                </Button>
                {creatingUserError && 'Error creating user...'}
            </div>
            {renderedUsers}
            </div>
        );
    }
    ```

    ```js
    import { GoSync } from 'react-icons/go';

    function Button({
    ...
    loading,
    ...rest
    }) {
        const classes = className(
            rest.className,
            'flex items-center px-3 py-1.5 border h-8',
            {
                'opacity-80': loading,
            ...
            }
        );

        return (
            <button {...rest} disabled={loading} className={classes}>
            {loading ? <GoSync className="animate-spin" /> : children}
            </button>
        );
    }
    ```

## ▶ 370. Better Skeleton Display

> 자료: [028\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/028_-_media)

-   fetchUsers에 의해 로딩될 때나 request에 실패해 에러가 발생했을 때, 화면에서 상단 부분은 계속 유지되게 하고 아래 부분만 UI가 바뀌게 수정하자

    ```js
    function UsersList() {
        ...

        let content;
        if (isLoadingUsers) {
            content = <Skeleton times={6} className="h-10 w-full" />;
        } else if (loadingUsersError) {
            content = <div>Error fetching data...</div>;
        } else {
            content = data.map((user) => {
            return (
                <div key={user.id} className="mb-2 border rounded">
                <div className="flex p-2 justify-between items-center cursor-pointer">
                    {user.name}
                </div>
                </div>
            );
            });
        }

        return (
            <div>
            <div className="flex flex-row justify-between items-center m-3">
                ...
            </div>
            {content}
            </div>
        );
    }
    ```

## ▶ 371. A Thunk to Delete a User

> 자료: [029\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/029_-_media)

-   UsersList component에서 'x' 버튼을 클릭하면 해당 user 데이터를 삭제해야 함

    -   아래와 같이 'DELETE method'/'URL'를 통해 JSONServer로 request를 보내는 것을 성공하면, `{}` empty object 데이터를 포함한 Response를 받게 됨
    -   request 보낼 때 반드시 삭제할 user의 id를 넣어줘야 함
    -   이후, response로 받은 데이터로 users data state를 update해줘야함

    ```
    DELETE http://localhost:3005/users/1
    ```

    ```js
    // store/thunks/removeUser.js
    import { createAsyncThunk } from "@reduxjs/toolkit";
    import axios from "axios";

    const removeUser = createAsyncThunk("users/remove", async (user) => {
    	const response = await axios.delete(
    		`http://localhost:3005/users/${user.id}`
    	);

    	// FIX할 예정 !!!!
    	return response.data;
    });

    export { removeUser };
    ```

## ▶ 372. Updating the Slice

> 자료: [030\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/030_-_media)

-   usersSlice는 removeUser thunk function이 dispatch 보낸 action types을 받기 위해 extraReducers를 추가해줘야 함

    ```js
    import { removeUser } from '../thunks/removeUser';

    const usersSlice = createSlice({
        name: 'users',
        initialState: {
            isLoading: false,
            data: [],
            error: null,
        },
        extraReducers(builder) {
            ...
            builder.addCase(removeUser.pending, (state, action) => {
                state.isLoading = true;
            });
            builder.addCase(removeUser.fulfilled, (state, action) => {
                state.isLoading = false;
                // FIX ME!!!
                console.log(action);
            });
            builder.addCase(removeUser.rejected, (state, action) => {
                state.isLoading = false;
                state.error = action.error;
            });
        },
    });
    ```

-   store/index.js에서 removeUser async thunk function를 export해서 components에서 사용할 수 있게 해주자

    ```js
    // store/index.js
    export * from "./thunks/removeUser";
    ```

## ▶ 373. Refactoring the Component

> 자료: [031\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/031_-_media)

-   UsersList component에서 user 정보들을 화면에 나타내는 부분을 따로 UsersListItem component로 떼어내어 사용해보자

    ```js
    // components/UsersListItem.js
    function UsersListItem({ user }) {
    	return (
    		<div className="mb-2 border rounded">
    			<div className="flex p-2 justify-between items-center cursor-pointer">
    				{user.name}
    			</div>
    		</div>
    	);
    }

    export default UsersListItem;
    ```

    ```js
    import UsersListItem from './UsersListItem';

    function UsersList() {
        ...
        let content;
        if (isLoadingUsers) {
            ...
        } else if (loadingUsersError) {
            ...
        } else {
            content = data.map((user) => {
            return <UsersListItem key={user.id} user={user} />;
            });
        }
        ...
    }
    ```

## ▶ 374. Deleting the User

> 자료: [032\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/032_-_media)

-   UsersListItem component에 삭제 버튼을 만들고, 이 버튼을 클릭했을 때 removeUser async thunk function이 실행되도록 해보자

    ```js
    import { GoTrashcan } from "react-icons/go";
    import Button from "./Button";
    import { removeUser } from "../store";
    import { useThunk } from "../hooks/use-thunk";

    function UsersListItem({ user }) {
    	const [doRemoveUser, isLoading, error] = useThunk(removeUser);

    	const handleClick = () => {
    		doRemoveUser(user);
    	};

    	return (
    		<div className="mb-2 border rounded">
    			<div className="flex p-2 justify-between items-center cursor-pointer">
    				<div className="flex flex-row items-center justify-between">
    					<Button
    						className="mr-3"
    						loading={isLoading}
    						onClick={handleClick}
    					>
    						<GoTrashcan />
    					</Button>
    					{error && <div>Error deleting user.</div>}
    					{user.name}
    				</div>
    			</div>
    		</div>
    	);
    }
    ```

## ▶ 375. Fixing a Delete Error

> 자료: [033\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/033_-_media)

-   실제 위에서 작성한 removeUser thunk function을 사용하면 usersSlice에서 제대로 원하는 user를 삭제할 수가 없음

    -   이유) JSONServer에 DELETE method로 request를 보내 성공하면, `{}` empty object를 데이터로 반환해주기 때문
    -   이로 인해, `{ type: 'users/remove/fulfilled', payload: {} }` action type으로 dispatch를 보내게 되고, 이 action type에 의해 호출된 mini-reducer는 어떤 user를 삭제했는지에 대한 정보를 알 수 없음
    -   따라서, 이때는 removeUser thunk function에서 `response.data`가 아닌 `user` 객체를 반환해줘야 함

    ```js
    // thunks/removeUser.js
    const removeUser = createAsyncThunk("users/remove", async (user) => {
    	await axios.delete(`http://localhost:3005/users/${user.id}`);

    	return user;
    });
    ```

    ```js
    const usersSlice = createSlice({
        name: 'users',
        initialState: {
            isLoading: false,
            data: [],
            error: null,
        },
        extraReducers(builder) {
            ...
            builder.addCase(removeUser.fulfilled, (state, action) => {
                state.isLoading = false;
                state.data = state.data.filter((user) => {
                    return user.id !== action.payload.id;
                });
            });
            ...
        },
    });
    ```

## ▶ 376. Album Feature Overview

-   이제 album 영역을 구현해보자
-   UsersList에서 각 user를 클릭하면, panel이 expend되고 오직 클릭한 user과 연관된 albums 데이터만 fetch해서 panel에 나타나게 하고자 함
-   JSONServer로 albums 데이터와 관련된 request를 하는 방법

    -   albums 데이터 생성

        ```
        POST http://localhost:3005/albums
        {title: 'Forest Hike' userId: 2}
        ```

    -   특정 user와 관련된 album 데이터만 가져오기

        ```
        GET http://localhost:3005/albums?userId=2
        ```

    -   album 데이터 삭제

        ```
        DELETE http://localhost:3005/albums/33
        ```

## ▶ 377. Additional Components

-   Album 영역을 구현하기 위해 ExpandablePanel component를 포함한 아래 components를 앞으로 만들어가야 함

    ```
    App
       ↳ UsersList
               ↳ UsersListItem
                       ↳ ExpandablePanel
                               ↳ AlbumsList
                                   ↳ AlbumsListItem
                                           ↳ ExpandablePanel
    ```

-   ExpandablePanel component는 user에 대한 정보를 가진 'header' prop과 panel 안에 담을 content인 'children' prop을 받아야 함

    ```js
    const header = {
        <div>
            <Button>Delete</Button>
            <div>Myra</div>
        </div>
    };

    <ExpandablePanel header={header}>
        <h1>Albums By Myra</h1>
        <div>
            Album #1
            Album #2
        </div>
    </ExpandablePanel>
    ```

-   또한, ExpandablePanel component는 header 부분을 클릭했을 때 panel이 expand되도록 handler를 가져야 함
-   각 ExpandablePanel component의 'expanded' 여부에 대한 state는 어디에 두는 것이 좋을까?
    -   `Application-Level State`: 여러 components가 사용해야 하는 state인 경우, **Redux Store**에 저장하는 것이 좋음
    -   `Component-Level State`: 오직 한 component에서 사용하는 state인 경우, 해당 **component**에 저장하는 것이 좋음
    -   'expanded' state는 ExpandablePanel component에만 필요한 state이므로, 각 ExpandablePanel component에 저장하는 것이 좋음

## ▶ 378. Adding the ExpandablePanel

> 자료: [036\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/036_-_media)

-   ExpandablePanel component를 생성하고 UserListsItem component에서 사용해보자

    ```js
    // components/ExpandablePanel.js
    function ExpandablePanel({ header, children }) {
    	return (
    		<div className="mb-2 border rounded">
    			<div className="flex p-2 justify-between items-center cursor-pointer">
    				<div className="flex flex-row items-center justify-between">
    					{header}
    				</div>
    			</div>
    			<div className="p-2 border-t">{children}</div>
    		</div>
    	);
    }

    export default ExpandablePanel;
    ```

    ```js
    import ExpandablePanel from './ExpandablePanel';

    function UsersListItem({ user }) {
        ...
        const header = (
            <>
            <Button className="mr-3" loading={isLoading} onClick={handleClick}>
                <GoTrashcan />
            </Button>
            {error && <div>Error deleting user.</div>}
            {user.name}
            </>
        );

        return <ExpandablePanel header={header}>CONTENT!!!!!</ExpandablePanel>;
    }
    ```

## ▶ 379. Wrapping Up the ExpandablePanel

> 자료: [037\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/037_-_media)

-   ExpandablePanel component를 완성해보자

    -   화살표 아이콘을 클릭했을 때 panel이 expend되어야 함

    ```js
    import { useState } from "react";
    import { GoChevronDown, GoChevronLeft } from "react-icons/go";

    function ExpandablePanel({ header, children }) {
    	const [expanded, setExpanded] = useState(false);

    	const handleClick = () => {
    		setExpanded(!expanded);
    	};

    	return (
    		<div className="mb-2 border rounded">
    			<div className="flex p-2 justify-between items-center">
    				<div className="flex flex-row items-center justify-between">
    					{header}
    				</div>
    				<div onClick={handleClick} className="cursor-pointer">
    					{expanded ? <GoChevronDown /> : <GoChevronLeft />}
    				</div>
    			</div>
    			{expanded && <div className="p-2 border-t">{children}</div>}
    		</div>
    	);
    }
    ```

## ▶ 380. Adding the Albums Listing

> 자료: [038\_-_media](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/21.Interfacing_with_API's_Using_Async_Thunks/038_-_media)

-   panel이 expend 됐을 때 보여질 AlbumsList component를 만들자

    -   'user 객체'를 prop으로 받아야 함

    ```js
    // components/AlbumList.js
    function AlbumsList({ user }) {
    	return <div>Albums for {user.name}</div>;
    }

    export default AlbumsList;
    ```

    ```js
    import AlbumsList from './AlbumsList';

    function UsersListItem({ user }) {
        ...

        return (
            <ExpandablePanel header={header}>
                <AlbumsList user={user} />
            </ExpandablePanel>
        );
    }
    ```
