# ✔ '22. Modern Async with Redux Toolkit Query' 이론 정리

## ▶ 383. Introducing Redux Toolkit Query

-   `Redux Toolkit Query`는 Redux-Toolkit 라이브러리의 하나의 모듈로, 21챕터 강의에서 진행했던 request 작업을 좀 더 쉽게 하도록 도와줌

    -   Redux Toolkit Query 내 `createAPI` function을 사용해 requests 보낼 수 있음
    -   `createAPI` function은 자체적으로 **Slice, Thunks, Hooks**를 만들어주는데 우리는 Slice, Thunks는 사용하지 않고 Hooks만 유용하게 사용할 예정임

    ```js
    import { createApi } from "@reduxjs/toolkit/query/react";

    const albumsApi = createApi({
    	/* confings */
    });
    ```

-   아래와 같이 `createAPI` function의 `endpoints` property에 requests 로직을 적어주면, request-making 과정을 자동화하는 'Hooks'를 만들어줌

    -   `useFetchAlbumsQuery`
    -   `useAddAlbumMutation`
    -   `useRemoveAlbumMutation`
    -   Hooks의 이름는 endpoints에서 반환하는 key 이름을 사용해 만들어짐

    ```js
    import { createApi } from "@reduxjs/toolkit/query/react";

    const albumsApi = createApi({
      endpoints(builder) {
        return {
          fetchAlbums: /* fetch albums 코드 */,
          addAlbum: /* add albums 코드 */,
          removeAlbum: /* remove albums 코드 */,
        }
      }
    });
    ```

-   `createAPI` function이 자동으로 만들어준 Hooks를 가지고 component에서 사용하기만 하면 됨

    ```js
    function AlbumsList({ user }) {
    	const { data, error, isLoading } = useGetAlbumsQuery();

    	// Do something with data, error, isLoading
    }
    ```

-   `Redux Toolkit Query` 모듈의 특징

    -   매우 많은 corner case를 처리함
    -   requests를 만드는 거의 모든 과정을 처리함
        -   Fine-grained loading state
        -   Fine-grained error state
        -   Data caching and refetching

## ▶ 384. Creating a RTK Query API

> 자료: [003\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/003_-_albums)

### 🔹 Redux-Toolkit Query API 만드는 법(1)

1. App에서 필요한 모든 requests를 그룹화한다.

    - fetch albums, add album, remove album 👉 AlbumsApi
    - fetch photos, add photo, remove photo 👉 PhotosApi

2. API를 만들기 위해 새로운 파일을 생성한다.

    - 주의) 여기서 말하는 API는 server가 아니라, requests를 위한 interface임

    - store 폴더 안에 apis 폴더를 만들고 albumsApi.js 파일을 생성함

    ```js
    // store/apis/albumsApi.js
    import { createApi } from "@reduxjs/toolkit/query/react";

    const albumsApi = createApi({});
    ```

3. `reducerPath` property를 추가해 API가 생성한 수많은 state들을 Redux Store에 저장해야한다.

    - `createApi` function은 requests를 처리하기 위해 자동으로 Slice를 만들어 수많은 state를 생성함

    - 이 states를 Redux Store에 저장하기 위해, `reducerPath` property에 저장할 위치(key)를 알려줘야 함

    ```js
    // store/apis/albumsApi.js
    const albumsApi = createApi({
    	reducerPath: "albums",
    });
    ```

    ```js
    // Redux Store의 State
    {
      users: ...,
      albums: {
        queries: {...},
        mutations: {...},
        provided: {...},
        subscriptions: {...},
        config: {...},
      }
    }
    ```

## ▶ 385. Creating an Endpoint

> 자료: [004\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/004_-_albums)

### 🔹 Redux-Toolkit Query API 만드는 법(2)

4. API에 `baseQuery` property를 추가해 어디로 어떻게 requests를 보낼지 명시해줘야 함

    - 이전에는 JSONServer로 request를 보낼 때 'axios' 라이브러리를 직접 설치해 사용했지만, `Redux-Toolkit Query`에서는 request를 보낼 때 브라우저 API인 'fetch' function를 사용함

    - `Redux-Toolkit Query`에서는 `fetchBaseQuery`를 사용해 pre-configured fetch version을 먼저 만듦

    - 이후, pre-configured fetch version을 실제 requests를 위한 각 fetch version으로 만들어 JSONServer로 request를 보내게 됨

        ```js
        import {
        	createApi,
        	fetchBaseQuery,
        } from "@reduxjs/toolkit/query/react";

        const albumsApi = createApi({
        	reducerPath: "albums",
        	baseQuery: fetchBaseQuery({
        		baseUrl: "http://localhost:3005",
        	}),
        });
        ```

5. `endpoints` property를 추가해 requests 종류를 정해준다.

    - 아래 endpoints design process에 따라 만들어보자

        ```js
        import {
        	createApi,
        	fetchBaseQuery,
        } from "@reduxjs/toolkit/query/react";

        const albumsApi = createApi({
        	reducerPath: "albums",
        	baseQuery: fetchBaseQuery({
        		baseUrl: "http://localhost:3005",
        	}),
        	endpoints(builder) {
        		return {
        			fetchAlbums: builder.query({
        				query: (user) => {
        					return {
        						url: "/albums",
        						params: {
        							userId: user.id,
        						},
        						method: "GET",
        					};
        				},
        			}),
        		};
        	},
        });
        ```

#### ➕ `endpoints` Design Process

1. request 목적을 찾자

2. 목적에 부합하는 simplified name을 만들자

3. 해당 request가 query인지 mutation인지 정하자

    - `query`: 데이터를 가져오는 것 (read data)
    - `mutation`: 기존 데이터를 변경하는 것 (write data)

4. url path를 정하자

5. url query string을 정하자

6. method를 정하자

7. body를 정하자

|      **목적**       | albums list를 fetch 하고자 함 | album을 create 하고자 함 | album을 remove 하고자 함 |
| :-----------------: | :---------------------------: | :----------------------: | :----------------------: |
| **simplified name** |          fetchAlbums          |       createAlbum        |       removeAlbum        |
| **query/mutation**  |             query             |         mutation         |         mutation         |
|      **path**       |            /albums            |         /albums          |         /albums          |
|  **query string**   |        ?userId=userId         |            -             |            -             |
|     **method**      |              GET              |           POST           |          DELETE          |
|      **body**       |               -               |     {title, userId}      |            -             |

## ✔ 386. Using the Generated Hook

> 자료: [005\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/005_-_albums)

### 🔹 Redux-Toolkit Query API 만드는 법(3)

6. `createApi` function이 자동으로 생성한 Hooks를 export 해준다.

    ```js
    // store/apis/albumsApi.js
    ...
    export const { useFetchAlbumsQuery } = albumsApi;
    export { albumsApi };
    ```

7. API를 Redux Store와 연결시켜준다.

    - Reducer, middleware, listeners 사용
    - store에 직접 'albums'를 작성하는 대신, `albumsApi.reducerPath`로부터 불러와 key를 만들어 줌

    ```js
    //  store/index.js
    import { setupListeners } from "@reduxjs/toolkit/query";
    import { albumsApi } from "./apis/albumsApi";

    export const store = configureStore({
    	reducer: {
    		users: usersReducer,
    		[albumsApi.reducerPath]: albumsApi.reducer,
    	},
    	middleware: (getDefaultMiddleware) => {
    		return getDefaultMiddleware().concat(albumsApi.middleware);
    	},
    });

    setupListeners(store.dispatch);
    ```

8. store/index.js 파일에서 API Hooks를 export해준다.

    ```js
    //  store/index.js
    ...
    export { useFetchAlbumsQuery } from './apis/albumsApi';
    ```

9. component에서 API Hooks를 사용한다.

    - 기본적으로 해당 component가 initial render될 때만 Hook이 실행되기 때문에, 따로 `useEffect` hook을 사용할 필요가 없음

    ```js
    // components/AlbumsList.js
    import { useFetchAlbumsQuery } from "../store";

    function AlbumsList({ user }) {
    	const { data, error, isLoading } = useFetchAlbumsQuery(user);

    	console.log(data, error, isLoading);

    	return <div>Albums for {user.name}</div>;
    }
    ```

## ✔ 387. A Few Immediate Notes

> 자료: [006\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/006_-_albums)

-   component에서 hook을 호출할 때 여러 값이 담긴 object를 반환하게 됨

    -   `data`: server로부터 반환된 데이터
    -   `error`: error가 일어났는지 여부(null | error object)
    -   `isLoading`: 첫번째로 데이터를 로딩되는 경우에만 True (boolean)
    -   `isFetching`: 데이터를 로딩할 때마다 True(boolean)
    -   `refetch`: refetch하게 해주는 function

## ✔ 388. Rendering the List

-   server에서 받아온 albums 데이터를 AlbumsList component에 나타나게 해보자

    ```js
    import Skeleton from "./Skeleton";
    import ExpandablePanel from "./ExpandablePanel";
    import Button from "./Button";

    function AlbumsList({ user }) {
    	const { data, error, isLoading } = useFetchAlbumsQuery(user);

    	let content;
    	if (isLoading) {
    		content = <Skeleton times={3} />;
    	} else if (error) {
    		content = <div>Error loading albums.</div>;
    	} else {
    		content = data.map((album) => {
    			const header = <div>{album.title}</div>;

    			return (
    				<ExpandablePanel key={album.id} header={header}>
    					List of photos in the album
    				</ExpandablePanel>
    			);
    		});
    	}

    	return (
    		<div>
    			<div>Albums for {user.name}</div>
    			<div>{content}</div>
    		</div>
    	);
    }
    ```

## ✔ 389. Changing Data with Mutations

> 자료: [008\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/008_-_albums)

-   'Add Album' 버튼을 클릭하면, 랜덤하게 생성된 title을 포함한 album 데이터를 server로 POST 해주자

    -   아래 코드에서는 album state 데이터를 추가해도 아직 화면이 rerender되지 않음
    -   component에서 mutation hook을 호출하면, 첫 번째 인자로 hook function이 반환됨

    ```js
    // store/apis/albumsApi.js
    import { faker } from '@faker-js/faker';

    const albumsApi = createApi({
      reducerPath: 'albums',
      baseQuery: fetchBaseQuery({
        baseUrl: 'http://localhost:3005',
      }),
      endpoints(builder) {
        return {
          addAlbum: builder.mutation({
            query: (user) => {
              return {
                url: '/albums',
                method: 'POST',
                body: {
                  userId: user.id,
                  title: faker.commerce.productName(),
                },
              };
            },
          }),
          ...
        };
      },
    });

    export const { useFetchAlbumsQuery, useAddAlbumMutation } = albumsApi;
    ```

    ```js
    // store/index.js
    ...
    export { useFetchAlbumsQuery, useAddAlbumMutation } from './apis/albumsApi';
    ```

    ```js
    import { useFetchAlbumsQuery, useAddAlbumMutation } from '../store';

    function AlbumsList({ user }) {
      ...
      const [addAlbum, results] = useAddAlbumMutation();

      const handleAddAlbum = () => {
        addAlbum(user);
      };

      ...
      return (
        <div>
          <div>
            Albums for {user.name}
            <Button onClick={handleAddAlbum}>+ Add Album</Button>
          </div>
          <div>{content}</div>
        </div>
      );
    }
    ```

## ✔ 390. Differences Between Queries and Mutations

-   query hook을 실행했을 때 반환되는 값이랑 mutation hook을 실행했을 때 반환되는 값이 다름

    ```js
    // Query Hook 호출
    const { data, error, isLoading } = useFetchAlbumsQuery(user);
    ```

    ```js
    // Mutation Hook 호출
    const [addAlbum, results] = useAddAlbumMutation(user);
    ```

1. `query hook`

    - component가 화면에 처음 display될 때 즉시 실행됨 (by default)
    - isLoading, error, data, isfetching, refetching 등 다양한 정보를 담은 object를 반환함

2. `mutation hook`

    - 데이터를 변경하고 싶을 때(ex)버튼 클릭, form submit)마다 실행할 수 있게 첫번째 인자로 function을 제공함
    - 두번째 인자로 isLoading, isError, data, status 등 다양한 정보를 담은 object를 반환함

## ✔ 391. Options for Refetching Data

-   `useAddAlbumMutation` hook을 사용해 Server 데이터를 변경한 후, 추가된 데이터를 화면에 나타내고 싶으면 어떻게 하면 될까?

### 🔹 변경된 데이터를 처리하는 방법 (두 가지 options)

1. response로 받은 newly-created album을 우리의 albums list에 추가해준다

    - 장점: 오직 한 번만 request함
    - 단점: 코드가 복잡해지고, response는 우리가 필요한 데이터를 충분히 제공해주지 못할 수도 있음

2. first request로 new album을 추가한 후, 다시 second request로 모든 albums 데이터를 가져온다.
    - 장점: app에서 data flow를 파악하기 훨씬 쉬움
    - 단점: 두 번 request 해야함
    - Redux-Toolkit Query 문서는 이 방법을 추천함

## ✔ 392. Request De-Duplication

-   위 두 options 중 두번째 방법을 선택해서 mutate된 데이터를 처리해보자
-   automatic data refetching은 `tag` 시스템을 사용해서 실행할 수 있음

    -   Redux-Toolkit Query가 어떻게 requests를 추적하는지 원리를 알면 `tag` 시스템을 이해하기 훨씬 쉬워짐
    -   따라서, Redux-Toolkit Query가 어떻게 requests를 추적하는지 간단하게 다음 강의에서 배울 예정임

-   만약 같은 user object에 대해서 `useFetchAlbumsQuery(user)`를 연속으로 두 번 실행했을 때, 과연 request가 두 번 발생할까?
    -   결론) Redux-Toolkit Query가 중복된 requests를 제거하기 때문에 오직 한 번의 request만 발생함
    -   이 문제도 Redux-Toolkit Query가 어떻게 requests를 추적하는지 알면 이해할 수 있음

## ✔ 393. Some Internals of Redux Toolkit Query

-   component에서 `useFetchAlbumsQuery({id:1, name:'Myra'})` hook을 실행했을 때, Redux Store의 albums state에는 endpoint/argument/result가 저장이 되고 server로 request를 보내게 됨

    -   endpoint: fetchAlbums
    -   argument: {id:1, name:'Myra'}
    -   result: {status: 'pending', data:null, ...}

-   만약, 첫 번째와 동일한 `useFetchAlbumsQuery({id:1, name:'Myra'})` hook을 한번 더 실행하게 되면, Redux-Toolkit Query는 기존 Redux Store의 albums state에서 동일한 'endpoint/argument'를 가진 기록이 존재하는지 확인하게 되고, 존재한다면 새로 저장하지 않고 기존 result 값을 반환해주게 됨

-   `store.getState()`를 통해 Redux Store에 저장된 state를 확인해보면, Redux Toolkit Query가 hook을 실행할 때마다 어떻게 state를 저장하는지 알 수 있음

    ```js
    store.getState();
    // {
    //   users: {...},
    //   albums: {
    //     config: {...},
    //     mutations: {},
    //     provided: {},
    //     queries: {
    //       'fetchAlbums({"id":1, "name":"Myra"})': {
    //         data: [{...}, {...}, {...}],
    //         status: 'fulfilled',
    //         requestId: 'EoXep...',
    //         ...
    //       }
    //     },
    //     subscriptions: {}
    //   }
    // }
    ```

## ✔ 394. Refetching with Tags

> 자료: [013\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/013_-_albums)

-   `Tag` system을 사용하면, 나중에 특정 mutations이 발생한 이후에 특정 queries가 'out of data'라고 마크해줄 수 있음
-   즉, component에서 `useFetchAlbumsQuery({id:1, name:'Myra'})` hook을 실행했을 때, Redux Store의 albums state에 endpoint/argument/result 뿐만 아니라 createApi에서 지정한 tags도 함께 저장하게 함

    -   endpoint: fetchAlbums
    -   argument: {id:1, name:'Myra'}
    -   result: {status: 'pending', data:null, ...}
    -   tags: ['Album']

-   이후, AddAlbum mutation hook을 실행하면 server에서의 myra album 데이터와 Redux Store에서의 해당 state 간의 차이가 발생하게 되고, 이때 'Album' tags를 가진 queries는 'out of data'라고 마크해주면 자동으로 Redux-Toolkit Query는 해당 queries를 다시 실행해 server로부터 변경된 데이터를 가져와 재할당하게 됨

    ```js
    const albumsApi = createApi({
      reducerPath: 'albums',
      baseQuery: fetchBaseQuery({
        baseUrl: 'http://localhost:3005',
      }),
      endpoints(builder) {
        return {
          addAlbum: builder.mutation({
            invalidatesTags: ['Album'],
            query: (user) => { ... },
          }),
          fetchAlbums: builder.query({
            providesTags: ['Album'],
            query: (user) => { ... },
          }),
        };
      },
    });
    ```

-   하지만 위와 같은 tag로 Refetching을 하게 되면, 동일한 'Album' tag명을 가진 기존 여러 fetchAlbums state가 모두 refetching되는 문제가 발생함

## ✔ 395. Fine-Grained Tag Validation

> 자료: [014\_-_albums](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/22.Modern_Async_with_Redux_Toolkit_Query/014_-_albums)

-   상관없는 query들까지 원치않게 refetching되는 것을 막기 위해, tag를 각 query마다 unique하게 작성해야 함

    -   providesTags나 invalidatesTags properties의 값을 단순히 string 형태의 'Album'가 아닌 unique한 object를 반환하는 function을 만들어보자
    -   이 function은 순서대로 'result, error, argument' 인자를 받게 됨

    ```js
    const albumsApi = createApi({
      reducerPath: 'albums',
      baseQuery: fetchBaseQuery({
        baseUrl: 'http://localhost:3005',
      }),
      endpoints(builder) {
        return {
          addAlbum: builder.mutation({
            invalidatesTags: (result, error, user) => {
              return [{ type: 'Album', id: user.id }];
            },
            query: (user) => {...},
          }),
          fetchAlbums: builder.query({
            providesTags: (result, error, user) => {
              return [{ type: 'Album', id: user.id }];
            },
            query: (user) => {...},
          }),
        };
      },
    });
    ```
