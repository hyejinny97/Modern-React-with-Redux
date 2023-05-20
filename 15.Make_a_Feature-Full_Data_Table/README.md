# ✔ '15. Make a Feature-Full Data Table!' 이론 정리

## ▶ 243. Creating a Reusable table

> 자료: [001\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/001_-_table)

-   table을 만들어보자

    -   fruits, color, score에 대한 column을 가지는 table을 만들어보자
    -   score가 높은 순서대로 정렬하는 기능도 넣어보자

-   먼저, `Table` component를 만들자

    ```js
    // components/Table.js
    function Table() {
    	return <div>Table</div>;
    }

    export default Table;
    ```

-   `TablePage` component를 만들자

    ```js
    // pages/TablePage.js
    import Table from "../components/Table";

    function TablePage() {
    	return (
    		<div>
    			<Table />
    		</div>
    	);
    }

    export default TablePage;
    ```

-   user가 '/table' path로 이동했을 때 TablePage가 render되도록 App.js에서 route를 추가하자

    ```js
    import TablePage from "./pages/TablePage";

    function App() {
    	return (
    		<div className="container mx-auto grid grid-cols-6 gap-4 mt-4">
    			<Sidebar />
    			<div className="col-span-5">
    				...
    				<Route path="/table">
    					<TablePage />
    				</Route>
    			</div>
    		</div>
    	);
    }
    ```

-   Sidebar component에서 '/table' path로 이동하는 link를 추가하자

    ```js
    function Sidebar() {
      const links = [
        ...
        { label: 'Table', path: '/table' },
      ];
      ...
    }
    ```

## ▶ 244. Communicating Data to the Table

> 자료: [002\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/002_-_table)

-   Table component가 표를 만들기 위해선, parent component로부터 각 행의 데이터를 받아와야 함

    -   TablePage component는 Table component로 array of objects 형태의 데이터를 prop으로 넘겨줌

    ```js
    function TablePage() {
    	const data = [
    		{ name: "Orange", color: "bg-orange-500", score: 5 },
    		{ name: "Apple", color: "bg-red-500", score: 3 },
    		{ name: "Banana", color: "bg-yellow-500", score: 1 },
    		{ name: "Lime", color: "bg-green-500", score: 4 },
    	];

    	return (
    		<div>
    			<Table data={data} />
    		</div>
    	);
    }
    ```

    ```js
    function Table({ data }) {
    	return <div>{data.length}</div>;
    }
    ```

## ▶ 245. Reminder on Table HTML Structure

> 자료: [003\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/003_-_table)

-   plain HTML 테이블 구조: <table> > <thead>, <tbody> > <tr> > <th>, <td>

-   <thead> 부분을 먼저 작성해보자

    ```js
    function Table({ data }) {
    	return (
    		<table>
    			<thead>
    				<tr>
    					<th>Fruit</th>
    					<th>Color</th>
    					<th>Score</th>
    				</tr>
    			</thead>
    			<tbody></tbody>
    		</table>
    	);
    }
    ```

## ▶ 246. Building the Rows

-   map function을 사용해 <tbody> 부분도 채워보자

    ```js
    function Table({ data }) {
    	const renderedRows = data.map((fruit) => {
    		return (
    			<tr key={fruit.name}>
    				<td>{fruit.name}</td>
    				<td>{fruit.color}</td>
    				<td>{fruit.score}</td>
    			</tr>
    		);
    	});

    	return (
    		<table>
    			...
    			<tbody>{renderedRows}</tbody>
    		</table>
    	);
    }
    ```

## ▶ 247. Better Styling

> 자료: [005\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/005_-_table)

-   Table component를 스타일링해보자

    ```js
    function Table({ data }) {
      const renderedRows = data.map((fruit) => {
        return (
          <tr className="border-b" key={fruit.name}>
            <td className="p-3">{fruit.name}</td>
            <td className="p-3">
              <div className={`p-3 m-2 ${fruit.color}`}></div>
            </td>
            <td className="p-3">{fruit.score}</td>
          </tr>
        );
      });

      return (
        <table className="table-auto border-spacing-2">
          <thead>
            <tr className="border-b-2">
              ...
            </tr>
          </thead>
          <...
        </table>
      );
    }
    ```

-   지금까지 작성한 위 Table component는 재사용 가능하지 않음

## ▶ 248. Done! But It's Not Reusable

> 자료: [006\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/006_-_table)

-   Reusable Table component를 만들기 위해 고려해야할 조건
    -   1. rows 개수 👉 `map` function으로 해결 가능
    -   2. columns 개수
    -   3. columns 수는 object의 properties 수와 같지 않을 수 있음
    -   4. 일부 columns는 정렬 가능함
    -   5. columns는 다양한 types(string, number 등)을 정렬 가능해야함
    -   6. cells은 여러 properties를 계산한 결과값일 수 있음
    -   7. cells은 어떤 데이터든지 나타내야함 (ex) 이미지, 색깔)

## ▶ 249. Here's the Idea

-   TablePage component가 Table component로 prop을 넘겨줄 때, 각 '행'의 정보를 담은 `data` prop(array of objects)뿐만 아니라 각 '열'의 정보를 담은 `config` prop(array of objects)도 같이 넘겨주자
-   `config` prop은 각 column명과 `data`를 어떻게 render할 지에 대한 정보를 담고 있음

    ```js
    [
      {
        label: 'Name',
        render: (fruit) => fruit.name,
      },
      {
        label: 'Color',
        render: (fruit) => <div className={`p-2 m-3 ${fruit.color}`} />,
      },
      {
        label: 'Score',
        render: (fruit) => fruit.score,
        sort: (a, b) => /* code to implement sorting */
      },
    ]
    ```

## ▶ 250. Dynamic Table Headers

> 자료: [008\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/008_-_table)

-   `config` prop을 생성해 Table component로 넘겨주자

    ```js
    function TablePage() {
      ...
      const config = [
        { label: "Name of Fruit" },
        { label: "Color" },
        { label: "Score" },
      ];

      return (
        <div>
          <Table data={data} config={config} />
        </div>
      );
    }
    ```

-   `config` prop을 받아 <thead> 부분을 다시 리팩토링하자

    ```js
    function Table({ data, config }) {
      const renderedHeaders = config.map((column) => {
        return <th key={column.label}>{column.label}</th>;
      });
      ...

      return (
        <table className="table-auto border-spacing-2">
          <thead>
            <tr className="border-b-2">{renderedHeaders}</tr>
          </thead>
          <tbody>{renderedRows}</tbody>
        </table>
      );
    }
    ```

## ▶ 252. Fixed Cell Values

> 자료: [010\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/010_-_table)

-   `config` prop에 render property를 추가해 Table component로 넘겨주자

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
          render: (fruit) => fruit.color,
        },
        {
          label: 'Score',
          render: (fruit) => fruit.score,
        },
      ];
      ...
    }
    ```

-   `config` prop을 받아 renderedRows를 다시 리팩토링하자

    ```js
    function Table({ data, config }) {
      ...
      const renderedRows = data.map((fruit) => {
        return (
          <tr className="border-b" key={fruit.name}>
            <td className="p-3">{config[0].render(fruit)}</td>
            <td className="p-3">{config[1].render(fruit)}</td>
            <td className="p-3">{config[2].render(fruit)}</td>
          </tr>
        );
      });
      ...
    }
    ```

## ▶ 253. Nested Maps

> 자료: [011\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/011_-_table)

-   위 renderedRows 코드를 다시 리팩토링해보자

    -   data mapping function안에 또 config mapping function을 만들자

    ```js
    function Table({ data, config }) {
      ...
      const renderedRows = data.map((fruit) => {
        const renderedCells = config.map((column) => {
          return (
            <td className="p-2" key={column.label}>
              {column.render(fruit)}
            </td>
          );
        });

        return (
          <tr className="border-b" key={fruit.name}>
            {renderedCells}
          </tr>
        );
      });
      ...
    }
    ```

-   이렇게 table을 구현함으로써, 아래처럼 특정 column을 추가하고자할 때 단순히 config array에 해당 column에 대한 object만 추가해주면 됨

    ```js
    const config = [
    	{
    		label: "Name",
    		render: (fruit) => fruit.name,
    	},
    	{
    		label: "Color",
    		render: (fruit) => fruit.color,
    	},
    	{
    		label: "Score",
    		render: (fruit) => fruit.score,
    	},
    	{
    		label: "Score Squared",
    		render: (fruit) => fruit.score ** 2,
    	},
    ];
    ```

## ▶ 254. Fixing the Color

> 자료: [012\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/012_-_table)

-   string 형태의 fruit color가 아닌 실제 색깔이 나타나도록 수정하자

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
        },
      ];
      ...
    }
    ```

## ▶ 255. Adding a Key Function

> 자료: [013\_-_table](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/15._Make_a_Feature-Full_Data_Table/013_-_table)

-   Table component에서 'fruit.name'을 사용해서 각 행의 key prop을 만드는 것은 reusable하지가 않음

    -   대신, parent에서 Key function을 만들어 직접 각 행의 key 값을 전달해주자

    ```js
    function TablePage() {
      ...
      const keyFn = (fruit) => {
        return fruit.name;
      };

      return (
        <div>
          <Table data={data} config={config} keyFn={keyFn} />
        </div>
      );
    }
    ```

    ```js
    function Table({ data, config, keyFn }) {
      ...
      const renderedRows = data.map((rowData) => {
        ...
        return (
          <tr className="border-b" key={keyFn(rowData)}>
            {renderedCells}
          </tr>
        );
      });
      ...
    }
    ```
