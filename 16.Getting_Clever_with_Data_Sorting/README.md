# ✔ '16. Getting Clever with Data Sorting' 이론 정리

## ▶ 256. Adding Sorting to the Table

-   이전 Table component에 정렬 기능을 추가해보자

    -   기존 데이터는 원래 정렬되지 않은 채로 존재(unsorted)
    -   정렬이 가능한 column명을 한번 클릭하면, 해당 column을 기준으로 전체 row가 오름차순 정렬됨(ascending)
    -   정렬이 가능한 column명을 한번 더 클릭하면, 해당 column을 기준으로 전체 row가 내림차순 정렬됨(descending)
    -   다시 한번 더 클릭 시, 정렬되지 않은 상태로 되돌아옴(unsorted)
    -   `unsorted` → `ascending` → `descending`

-   이때, 가장 중요한 점은 Table component는 반드시 reusable해야 함

## ▶ 257. Reminder on Sorting in JavaScript

-   `arr.sort(comparator)` function을 이용해 정렬할 수 있음

    -   주의) sort() method는 arr에 있는 모든 elements를 string으로 변환한 후 정렬함
    -   따라서, 숫자 정렬을 하기 위해서는 comparator function을 이용해야 함

        ```js
        const data = [5, 10, 4, 3];

        data.sort(); // [10, 3, 4, 5]
        ```

-   `arr.sort((a, b) => {})`일 경우

    -   comparator function이 `음수`을 반환하면, `(a, b)`순대로 정렬함
    -   comparator function이 `양수`을 반환하면, `(b, a)`순대로 정렬함

-   '오름차순' 정렬 방법

    ```js
    const data = [5, 10, 4, 3];

    data.sort((a, b) => {
    	return a - b;
    }); // [3, 4, 5, 10]
    ```

## ▶ 258. Sorting Strings

-   아래와 같은 array of strings에 sort method를 적용해보자

    -   자동으로 대문자가 소문자보다 앞에 정렬됨

    ```js
    const data = ["t", "A", "B", "a", "b"];

    data.sort(); // ['A', 'B', 'a', 'b', 't']
    ```

-   대/소문자 상관없이 string을 '오름차순' 정렬하려면 어떻게 해야할까?

    -   `string.localeCompare()` method를 사용해보자

-   `referenceStr.localeCompare(compareString)`

    -   참조 문자열이 정렬 순으로 지정된 문자열 앞/뒤에 오는지 또는 동일한 문자열인지 나타내는 수치를 반환
    -   `compareString`: referenceStr가 비교되는 문자열
    -   반환값: 정수
        -   referenceStr < compareString인 경우, `음수` 반환
        -   referenceStr > compareString인 경우, `양수` 반환
        -   referenceStr == compareString인 경우, `0` 반환

    ```js
    const data = ["t", "A", "B", "a", "b"];

    data.sort((a, b) => {
    	return a.localeCompare(b);
    }); // ['a', 'A', 'b', 'B', 't']
    ```

## ▶ 259. Sorting Objects

-   objects를 정렬하기 위해 일단 object 내 비교하고자 하는 properties를 꺼내야 함
-   꺼낸 properties는 numbers나 strings가 될 수 있음

    ```js
    const data = [
    	{ name: "Onion", cost: 5, weight: 7 },
    	{ name: "Tomato", cost: 10, weight: 5 },
    	{ name: "Carrot", cost: 15, weight: 2 },
    ];
    ```

## ▶ 260. Object Sort Implementation

-   object 내에서 비교하고자 하는 properties를 꺼내 이를 기준으로 objects를 '오름차순' 정렬해보자

    -   getSortValue() function을 생성해 object 내에서 비교하고자 하는 properties를 꺼내자
    -   비교하고자 하는 properties가 number인지 string인지 확인한 후, 각각에 맞는 정렬 방법을 사용하자

    ```js
    function getSortValue(vegetable) {
    	return vegetable.weight;
    }

    data.sort((a, b) => {
    	const valueA = getSortValue(a);
    	const valueB = getSortValue(b);

    	if (typeof valueA === "string") {
    		return valueA.localeCompare(valueB);
    	} else {
    		return valueA - valueB;
    	}
    });

    // [
    //	{ name: "Onion", cost: 5, weight: 7 },
    //	{ name: "Tomato", cost: 10, weight: 5 },
    //	{ name: "Carrot", cost: 15, weight: 2 },
    // ];
    ```

-   'getSortValue()' 함수의 반환값만 비교하고자 하는 properties로 바꿔주면, 어떤 기준으로도 정렬 가능

    ```js
    function getSortValue(vegetable) {
    	return vegetable.name;
    }

    // [
    //  { name: "Carrot", cost: 15, weight: 2 },
    //	{ name: "Onion", cost: 5, weight: 7 },
    //	{ name: "Tomato", cost: 10, weight: 5 },
    // ];
    ```

    ```js
    function getSortValue(vegetable) {
    	return vegetable.cost / vegetable.weight;
    }

    // [
    //	{ name: "Onion", cost: 5, weight: 7 },
    //	{ name: "Tomato", cost: 10, weight: 5 },
    //	{ name: "Carrot", cost: 15, weight: 2 },
    // ];
    ```

## ▶ 261. Reversing Sort Order

-   이제 위 코드에서 일부만 수정해 '내림차순' 정렬을 해보자

    -   "-1"을 곱한 값을 반환하면 내림차순 정렬 가능

        ```js
        data.sort((a, b) => {
        	const valueA = getSortValue(a);
        	const valueB = getSortValue(b);

        	if (typeof valueA === "string") {
        		return valueA.localeCompare(valueB) * -1;
        	} else {
        		return (valueA - valueB) * -1;
        	}
        });
        ```

-   하드코딩으로 직접 '-1'을 곱해 정렬 순서를 바꾸는 대신, 'sortOrder' 변수를 하나 두어 해당 값에 따라 오름차순/내림차순 정렬하도록 조건을 생성해보자

    ```js
    const sortOrder = "desc";

    data.sort((a, b) => {
    	const valueA = getSortValue(a);
    	const valueB = getSortValue(b);

    	const reverseOrder = sortOrder === "asc" ? 1 : -1;

    	if (typeof valueA === "string") {
    		return valueA.localeCompare(valueB) * reverseOrder;
    	} else {
    		return (valueA - valueB) * reverseOrder;
    	}
    });
    ```

## ▶ 262. Optional Sorting

-   위에서 만든 reusable Table component가 'unsorted' table과 'sorted' table로 모두 사용 가능하게 하려면, 아래와 같은 사항들을 모두 고려해야 함

    -   sorting state가 있어야 함
    -   table header를 클릭했을 때 처리할 handler function이 있어야 함
        -   단, sorting이 가능한 header를 클릭한 경우
    -   header를 render해야 함
        -   단, sorting이 가능한 header의 경우 정렬 icons을 더해줘야 함
    -   sorting이 가능하다면, sorting logic을 통해 정렬
    -   rows를 render해야 함
    -   headers와 rows를 합쳐 table을 반환함

-   위 모든 사항들을 고려해 Table component를 만든다면, Table component 내 상당히 많은 코드를 넣어야할 것 같다
-   다른 좋은 방법은 없을까?

## ▶ 263. A Small Extra Feature

-   현재 `config` 변수는 'label'과 'render' properties만 존재
-   label property를 통해 Table의 header를 나타냄 (`<th>{column.label}</th>`)
-   하지만, header에 단순히 text 형태의 label이 아닌 다양한 형태로 넣고 싶을 땐 어떻게 해야할까?

    -   header에 어떻게 보여주고 싶은지 알려주는 function인 'header' property를 `config`에 추가하자
    -   column에 이 header property가 있으면 이를 사용해 header를 나타내주고, header property가 없으면 단순히 이전처럼 text 형태의 label을 사용해 header를 나타내주자

    ```js
    const config = [
        {
            header: optional func
            label: 'Score',
            render: (fruit) => fruit.score,
        },
    ];
    ```

## ▶ 264. Customizing Header Cells

> 자료: [022\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/022_-_table)

-   `config` 변수에 'header' property를 추가함으로써, row cell처럼 header cell도 customize할 수 있게 하자

    ```js
    function TablePage() {
        ...
        const config = [
            {
                label: 'Name',
                render: (fruit) => fruit.name,
            },
            {
                label: 'Color',
                render: (fruit) => <div className={`p-3 m-2 ${fruit.color}`} />,
            },
            {
                label: 'Score',
                render: (fruit) => fruit.score,
                header: () => <th className="bg-red-500">Score</th>,
            },
        ];
        ...
    }
    ```

    ```js
    function Table({ data, config, keyFn }) {
        const renderedHeaders = config.map((column) => {
            if (column.header) {
                return column.header();
            }

            return <th key={column.label}>{column.label}</th>;
        });
        ...
    }
    ```

## ▶ 265. React Fragments

> 자료: [023\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/023_-_table)

-   위 코드를 실행하면 key prop이 빠졌다는 에러가 뜨는 것을 확인할 수 있음
-   `column.header()`에 어떻게 key prop을 추가할 수 있을까?

    -   단순히 `<div key={column.label}>column.header()</div>`처럼 요소로 한번 더 감싸는 형식은 HTML의 기본 table 형식을 깨뜨리기 때문에 또다른 에러가 발생
    -   아래처럼 Echo component를 만들어 활용하면, key prop은 추가하되 DOM을 건드리지 않고 `column.header()`만 반환할 수 있음

    ```js
    function Echo({ children }) {
        return children
    }

    function Table({ data, config, keyFn }) {
        const renderedHeaders = config.map((column) => {
            if (column.header) {
                return <Echo key={column.label}>{column.header()}</Echo>;
            }

            return <th key={column.label}>{column.label}</th>;
        });
        ...
    }
    ```

-   React에선 Echo component처럼 단순한 기능을 하는 `Fragment` component를 제공해줌

    -   `Fragment` component를 이용하면 DOM element를 건드리지 않고 key prop을 추가할 수 있고, 서로 다른 child elements를 그룹화할 때 사용되기도 함

    ```js
    import { Fragment } from 'react';

    function Table({ data, config, keyFn }) {
        const renderedHeaders = config.map((column) => {
            if (column.header) {
                return <Fragment key={column.label}>{column.header()}</Fragment>;
            }

            return <th key={column.label}>{column.label}</th>;
        });
        ...
    }
    ```

## ▶ 266. The Big Reveal

-   Table component는 parent component로부터 data/config props를 받아야 함
-   데이터를 정렬하기 위해서는 'data'(array of objects), data에서 정렬할 값들을 추출해 줄 'function', 오름차순인지 내림차순인지 결정해주는 'sorting order' 세 가지가 필요함

    -   data에서 정렬할 값들을 추출해 줄 'sortValue' function을 config 내 정렬이 필요한 column object에 추가해보자

    ```js
    const config = [
        ...
        {
            label: 'Score',
            render: (fruit) => fruit.score,
            header: () => <th className="bg-red-500">Score</th>,
            sortValue: optional func
        },
    ];
    ```

-   Table component는 unsortable table이든 sortable table이든 모두 사용 가능해야 함

    -   Table component에 정렬 기능을 넣는 것은 component 내부에 코드를 상당히 복잡하게 만들 수 있어 추천하지 않음
    -   대신, 데이터를 정렬하고 click event handler를 추가해주는 `SortableTable` component를 따로 만들어 Table component에 정렬된 데이터를 넘겨주도록 하자
    -   TablePage --`data/config props`-→ SortableTable --`sortedData/config props`-→ Table
    -   `SortableTable` component 덕분에 Table component는 단순히 정렬된 데이터를 받아 화면에 table을 display 해주기만 하면 됨

-   `SortableTable` component의 역할

    -   config array에서 각 object를 순회하면서, 'sortValue' function이 있는지 확인
    -   'sortValue' function이 있는 column(object)은 sortable column(object)임
    -   sortable column(object)에 clickable header cell임을 나타내는'header' property 추가
    -   user가 clickable header 클릭 시, 데이터를 정렬함
    -   정렬된 데이터를 Table component에 넘겨줌

-   sortable column인 경우, `SortableTable` component에서 어차피 clickable header cell임을 나타내는'header' property 추가해주기 때문에 TablePage component에서 굳이 'header' property를 추가할 필요 없음

## ▶ 267. Adding SortableTable

> 자료: [025\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/025_-_table)

-   `SortableTable` component를 구현해보자

    -   1. SortableTable component을 생성하고, 모든 props을 Table component로 넘겨주자
    -   2. TablePage component는 Table component가 아닌 SortableTable component를 보여주도록하자
    -   3. TablePage component의 config 변수 내 일부 columns에 'sortValue' function을 추가하고, 'header' property는 삭제하자
    -   4. SortableTable component는 config column objects를 순회하며 'sortValue'가 있는지 찾아, 있으면 'header' function을 추가해주자
    -   5. 'header' function에 의해 반환되는 <th> 태그는 click event를 watch해야함
    -   6. user가 해당 <th>을 클릭했을 때, 데이터를 정렬하고 Table component로 정렬된 데이터를 전달해줘야 함

    ```js
    import SortableTable from '../components/SortableTable';

    function TablePage() {
        ...
        const config = [
            {
                label: 'Name',
                render: (fruit) => fruit.name,
                sortValue: (fruit) => fruit.name,
            },
            {
                label: 'Color',
                render: (fruit) => <div className={`p-3 m-2 ${fruit.color}`} />,
            },
            {
                label: 'Score',
                render: (fruit) => fruit.score,
                sortValue: (fruit) => fruit.score,
            },
        ];
        ...

        return (
            <div>
                <SortableTable data={data} config={config} keyFn={keyFn} />
            </div>
        );
    }
    ```

    ```js
    // components/sortableTable.js
    import Table from "./Table";

    function SortableTable(props) {
    	const { config } = props;

    	const updatedConfig = config.map((column) => {
    		if (!column.sortValue) {
    			return column;
    		}

    		return {
    			...column,
    			header: () => <th>{column.label} IS SORTABLE</th>,
    		};
    	});

    	return <Table {...props} config={updatedConfig} />;
    }

    export default SortableTable;
    ```

-   주의) config를 update할 때 config 원본을 바꿔서도 안되고 기존 column object을 그대로 가져와 사용해서도 안됨 (새로 object를 만들어 추가해줘야 함)

## ▶ 268. Watching for Header Cell Clicks

> 자료: [026\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/026_-_table)

-   'header' function에 의해 반환되는 <th> 태그에 click event listener를 달아주자

    ```js
    function SortableTable(props) {
        ...
        const handleClick = (label) => {
            console.log(label);
        };

        const updatedConfig = config.map((column) => {
            ...
            return {
                ...column,
                header: () => (
                    <th onClick={() => handleClick(column.label)}>
                        {column.label} IS SORTABLE
                    </th>
                ),
            };
        });
        ...
    }
    ```

## ▶ 269. Quick State Design

-   `SortableTable` component에 아래와 같은 두 개의 state를 둬야 함
    -   1. 정렬 방향에 대한 state 👉 `sortOrder` state (null, 'asc', 'desc')
    -   2. 정렬 기준인 column에 대한 state 👉 `sortBy` state (null, 'Name', 'Score')

## ▶ 270. Adding Sort State

> 자료: [028\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/028_-_table)

-   `sortOrder` state와 `sortBy` state를 생성해보자

    ```js
    import { useState } from 'react';

    function SortableTable(props) {
        const [sortOrder, setSortOrder] = useState(null);
        const [sortBy, setSortBy] = useState(null);

        const handleClick = (label) => {
            if (sortOrder === null) {
                setSortOrder('asc');
                setSortBy(label);
            } else if (sortOrder === 'asc') {
                setSortOrder('desc');
                setSortBy(label);
            } else if (sortOrder === 'desc') {
                setSortOrder(null);
                setSortBy(null);
            }
        };
        ...
    }
    ```

## ▶ 271. Yessssss, It Worked!

> 자료: [029\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/029_-_table)

-   `sortOrder` state와 `sortBy` state에 따라 정렬하는 코드를 작성해보자

    -   단, `sortOrder` state와 `sortBy` state가 모두 null이 아닐 때만 data를 정렬해야 함
    -   주의) 부모로부터 넘겨받은 data prop 원본을 변경하면 안됨. sort() method는 원본을 변경하기 때문에 data 복사본을 만들어 변경해야 함
    -   클릭한 column에서 sortValue function을 가져와 data에서 정렬할 값을 추출해내야 함

    ```js
    function SortableTable(props) {
        const { config, data } = props;
        ...

        let sortedData = data;
        if (sortOrder && sortBy) {
            const { sortValue } = config.find((column) => column.label === sortBy);
            sortedData = [...data].sort((a, b) => {
                const valueA = sortValue(a);
                const valueB = sortValue(b);

                const reverseOrder = sortOrder === 'asc' ? 1 : -1;

                if (typeof valueA === 'string') {
                    return valueA.localeCompare(valueB) * reverseOrder;
                } else {
                    return (valueA - valueB) * reverseOrder;
                }
            });
        }

        return (
            <div>
                {sortOrder} - {sortBy}
                <Table {...props} data={sortedData} config={updatedConfig} />
            </div>
        );
    }
    ```

## ▶ 272. Determining Icon Set

> 자료: [030\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/030_-_table)

-   `sortOrder` state와 `sortBy` state에 따라 header 앞에 icon이 나타나도록 하자

    -   조건에 따라 서로 다른 icon을 반환하는 `getIcons()` function을 생성해서 활용하자

    ```js
    function SortableTable(props) {
        ...
        const updatedConfig = config.map((column) => {
            ...
            return {
                ...column,
                header: () => (
                    <th onClick={() => handleClick(column.label)}>
                        {getIcons(column.label, sortBy, sortOrder)}
                        {column.label}
                    </th>
                ),
            };
        });
        ...
    }

    function getIcons(label, sortBy, sortOrder) {
        if (label !== sortBy) {
            return 'Show both icons';
        }

        if (sortOrder === null) {
            return 'show both icons';
        } else if (sortOrder === 'asc') {
            return 'show up icon';
        } else if (sortOrder === 'desc') {
            return 'show down icon';
        }
    }
    ```

## ▶ 273. Styling Header Cells

> 자료: [031\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/031_-_table)

-   string이 아닌 실제 아이콘이 나타나도록 수정하자

    ```js
    import { GoArrowSmallDown, GoArrowSmallUp } from 'react-icons/go';

    function SortableTable(props) {
        ...
        const updatedConfig = config.map((column) => {
            ...
            return {
                ...column,
                header: () => (
                    <th
                    className="cursor-pointer hover:bg-gray-100"
                    onClick={() => handleClick(column.label)}
                    >
                        <div className="flex items-center">
                            {getIcons(column.label, sortBy, sortOrder)}
                            {column.label}
                        </div>
                    </th>
                ),
            };
        });
        ...
    }

    function getIcons(label, sortBy, sortOrder) {
        if (label !== sortBy) {
            return (
                <div>
                    <GoArrowSmallUp />
                    <GoArrowSmallDown />
                </div>
            );
        }

        if (sortOrder === null) {
            return (
                <div>
                    <GoArrowSmallUp />
                    <GoArrowSmallDown />
                </div>
            );
        } else if (sortOrder === 'asc') {
            return (
                <div>
                    <GoArrowSmallUp />
                </div>
            );
        } else if (sortOrder === 'desc') {
            return (
                <div>
                    <GoArrowSmallDown />
                </div>
            );
        }
    }
    ```

## ▶ 274. Resetting Sort Order

> 자료: [032\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/032_-_table)

-   만약, Score column을 클릭한 상태에서 바로 Name column을 클릭하면 어떻게 될까?

    -   다른 column을 클릭했을 때에도 여전히 sortBy state가 Score을 가리키게 됨
    -   따라서, 현재 클릭한 column이 sortBy state와 같은지 다른지 비교해, 다르다면 sortBy state와 sortOrder state를 다시 설정해야함

    ```js
    function SortableTable(props) {
        ...
        const handleClick = (label) => {
            if (sortBy && label !== sortBy) {
                setSortOrder('asc');
                setSortBy(label);
                return;
            }
            ...
        };
        ...
        return <Table {...props} data={sortedData} config={updatedConfig} />;
    }
    ```

## ▶ 275. Table Wrapup

> 자료: [033\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/16._Getting_Clever_with_Data_Sorting/033_-_table)

-   TablePage component에서 config에 column object를 추가함으로써 자유롭게 '열'을 증가시킬 수 있음
-   TablePage component에서 data에 row object를 추가함으로써 자유롭게 '행'을 증가시킬 수 있음
-   unsortable table은 단순히 Table component를 사용하면 되고, sortable table은 SortableTable component를 사용하면 됨
