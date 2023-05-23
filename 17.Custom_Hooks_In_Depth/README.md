# ✔ '17. Custom Hooks In Depth' 이론 정리

## ▶ 276. Exploring Code Reuse

-   위 챕터에서 구현한 sortable table와 매우 유사한 방식으로 sortable list를 구현할 수 있음
-   SortableTable component처럼 `SortableList` component를 만들어보자
    -   SortableList component에는 SortableTable component처럼 'sortOrder'/'sortBy' state가 필요할 것임
    -   또한, data를 정렬하는 로직도 필요함
    -   두 component에서 중복된 부분인 'sortOrder'/'sortBy' state와 data를 정렬하는 로직을 따로 떼어내어 재사용하는 것이 어떨까?

## ▶ 277. Revisiting Custom Hooks

-   SortableTable component에서 'sortOrder'/'sortBy' state와 data를 정렬하는 로직을 따로 떼어내기 위해 Custom Hook을 사용하자

    -   일부 reusable logic을 포함하는 function
    -   Custom Hook은 대게 useState, useEffect와 같은 built-in hooks를 재사용함
    -   처음부터 Custom Hook을 만드는 것보다 기존 logic을 떼어내어 hook으로 옮기는 것이 더 쉬운 방법임

-   앞으로의 강의 진행 방향
    -   1. 아주 작은 logic을 담은 demo component를 만든다
    -   2. design process를 적용해서 logic을 떼어내어 custom hook으로 옮긴다
    -   3. SortableTable component로 돌아가서 design process를 적용한다

## ▶ 278. Creating the Demo Component

> 자료: [003\_-_sopt](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/17._Custom_Hooks_In_Depth/003_-_sopt)

-   counter 로직을 담은 demo component를 만들어 보자

    ```js
    // pages/CounterPage.js
    import { useState, useEffect } from "react";
    import Button from "../components/Button";

    function CounterPage({ initialCount }) {
    	const [count, setCount] = useState(initialCount);

    	useEffect(() => {
    		console.log(count);
    	}, [count]);

    	const handleClick = () => {
    		setCount(count + 1);
    	};

    	return (
    		<div>
    			<h1>Count is {count}</h1>
    			<Button onClick={handleClick}>Increment</Button>
    		</div>
    	);
    }

    export default CounterPage;
    ```

    ```js
    import CounterPage from "./pages/CounterPage";

    function App() {
    	return (
    		<div className="container mx-auto grid grid-cols-6 gap-4 mt-4">
    			<Sidebar />
    			<div className="col-span-5">
    				...
    				<Route path="/counter">
    					<CounterPage initialCount={10} />
    				</Route>
    			</div>
    		</div>
    	);
    }
    ```

    ```js
    function Sidebar() {
      const links = [
        ...
        { label: 'Counter', path: '/counter' },
      ];
      ...
    }
    ```

## ▶ 279. Custom Hook Creation

-   CounterPage component에서 count, useEffect, handleClick 부분은 나중에 재사용할 수 있는 유용한 부분이므로 따로 hook을 만들어 옮겨놓는게 좋을 것 같음
-   Custom Hook을 만드는 기본 design process
    -   1. component에서 state와 연관된 코드를 모두 찾는다
    -   2. 코드를 hook function에 옮긴다
    -   3. broken references를 모두 수정한다

## ▶ 281. Hook Creation Process in Depth

> 자료: [005\_-_sopt](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/17._Custom_Hooks_In_Depth/005_-_sopt)

-   Custom Hook을 만드는 자세한 design process

    -   1. component 파일 내부에 'useSomething' function을 생성함
    -   2. component애서 state와 연관된 모든 코드(JSX 제외)를 찾음
    -   3. state와 연관된 모든 코드를 잘라 'useSomething' function에 붙여넣음
    -   4. component 내부에서 발생된 'not defined' errors를 찾음
    -   5. hook이 component가 필요한 변수들을 모두 포함하는 object를 반환하게 함
    -   6. component 내부에서 hook을 호출하고, destructuring을 통해 component가 필요한 properties를 가져옴
    -   7. hook 내부에서 발생된 'not defined' errors를 찾아, 필요한 변수들을 hook의 인자로 넣어줌
    -   8. hook 이름을 의미있는 이름으로 다시 지어줌
    -   9. hook이 반환하는 properties 이름을 의미있는 이름으로 다시 지어줌
    -   10. hooks 폴더에 'use-hook이름.js' 파일을 생성하고, hook function을 잘라 옮겨줌
    -   11. broken references를 모두 수정함

    ```js
    // hooks/use-counter.js
    import { useState, useEffect } from "react";

    function useCounter(initialCount) {
    	const [count, setCount] = useState(initialCount);

    	useEffect(() => {
    		console.log(count);
    	}, [count]);

    	const increment = () => {
    		setCount(count + 1);
    	};

    	return {
    		count,
    		increment,
    	};
    }

    export default useCounter;
    ```

    ```js
    import useCounter from "../hooks/use-counter";

    function CounterPage({ initialCount }) {
    	const { count, increment } = useCounter(initialCount);

    	return (
    		<div>
    			<h1>Count is {count}</h1>
    			<Button onClick={increment}>Increment</Button>
    		</div>
    	);
    }
    ```

## ▶ 282. Making a Reusable Sorting Hook

> 자료: [006\_-_sopt](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/17._Custom_Hooks_In_Depth/006_-_sopt)

-   SortableTable component로 돌아가서 위에서 설명한 design process를 적용해 정렬 기능을 따로 떼어내어 hook으로 만들자

    ```js
    // hooks/use-sort.js
    import { useState } from "react";

    function useSort(data, config) {
        const [sortOrder, setSortOrder] = useState(null);
        const [sortBy, setSortBy] = useState(null);

        const setSortColumn = (label) => {
            ...
        };

        let sortedData = data;
        if (sortOrder && sortBy) {
            const { sortValue } = config.find((column) => column.label === sortBy);
            sortedData = [...data].sort((a, b) => {
                ...
            });
        }

        return {
            sortOrder,
            sortBy,
            sortedData,
            setSortColumn,
        };
    }

    export default useSort;
    ```

    ```js
    import useSort from '../hooks/use-sort';

    function SortableTable(props) {
        const { config, data } = props;
        const { sortOrder, sortBy, sortedData, setSortColumn } = useSort(
            data,
            config
        );

        const updatedConfig = config.map((column) => {
            ...
        });

        return <Table {...props} data={sortedData} config={updatedConfig} />;
    }
    ```
