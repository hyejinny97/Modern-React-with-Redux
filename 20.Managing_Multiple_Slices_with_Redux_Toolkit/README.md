# ✔ '20. Managing Multiple Slices with Redux Toolkit' 이론 정리

## ▶ 320. Project Overview

-   앞으로 진행할 Car App에 대한 간단한 소개
    -   car name과 car value를 입력하고 submit하면, my car list에 추가됨
    -   my car list에 추가된 모든 차의 value를 합한 총 가격이 아래에 나타남
    -   my car list에 추가된 각 정보를 삭제할 수도 있음
    -   search 창에 my car list에 있는 car name 일부만 기입해도 해당 car 정보가 뜨게 됨
    -   car name 입력란에 my car list에 이미 있는 차 이름과 일부만 매치되도, 해당 car name이 bold 처리되어 강조됨

## ▶ 321. Adding Component Boilerplate

> 자료: [002\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/002_-_cars)

-   Car App에 필요한 '@reduxjs/toolkit', 'react-redux', 'bulma' 라이브러리 설치
-   CarForm, CarList, CarSearch, CarValue component 생성 후, App component에 import하기

## ▶ 322. Thinking About Derived State

-   Redux Store Design 방법

    -   1. app에서 어떤 state가 필요한지 모두 찾기
    -   2. 시간이 지나면서 각 state가 어떻게 변하는지 확인하기
    -   3. 비슷한 state는 한 그룹으로 묶기
    -   4. 각 그룹을 slice로 생성하기

1. 먼저, app에서 어떤 state가 필요한지 모두 찾자
    - name(string), cost(number), searchTerm(string), cars(array of object)
    - `Derived State`: 이미 존재하는 state를 사용해 계산할 수 있는 값 (ex) totalCost, matchedCars)
    - 따라서, Derived State는 굳이 state에 포함시키지 않아도 됨

## ▶ 323. Thinking About Redux Design

2. 시간이 지나면서 각 state가 어떻게 변하는지 확인하자

    - change name, change cost, change search term, add car, remove car
    - 위 각각은 결국 나중에 mini-reducer의 이름(또는 actions type)이 될 수 있음
    - 즉, changeName, changeCost, changeSearchTerm, addCar, removeCar mini-reducer

3. 비슷한 state는 한 그룹으로 묶자

    - 개발자마다 다양한 방식으로 묶을수 있음
    - 이 강의에서는 'state related to adding cars', 'state related to display list of cars' 두 그룹으로 나눔
        -   1. `state related to adding cars` 👉 name, cost
        -   2. `state related to display list of cars` 👉 searchTerm, cars

4. 각 그룹을 slice로 생성하자
    -   1. slice 1
        - 👉 (state) name, cost
        - 👉 (mini-reducer) changeName, changeCost
    -   2. slice 2
        - 👉 (state) searchTerm, cars
        - 👉 (mini-reducer) changeSearchTerm, addCar, removeCar

## ▶ 324. Adding the Form Slice

> 자료: [005\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/005_-_cars)

-   위에서 두 개로 나눈 slice의 각 이름을 정해보자

    -   slice 1 ⇒ formSlice
    -   slice 2 ⇒ carsSlice

-   formSlice를 먼저 작성해보자

    ```js
    // store/slices/formSlice.js
    import { createSlice } from "@reduxjs/toolkit";

    const formSlice = createSlice({
    	name: "form",
    	initialState: {
    		name: "",
    		cost: 0,
    	},
    	reducers: {
    		changeName(state, action) {
    			state.name = action.payload;
    		},
    		changeCost(state, action) {
    			state.cost = action.payload;
    		},
    	},
    });

    export const { changeName, changeCost } = formSlice.actions;
    export const formReducer = formSlice.reducer;
    ```

## ▶ 325. Maintaining a Collection with a Slice

> 자료: [006\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/006_-_cars)

-   carsSlice도 작성해보자

    -   addCars, removeCars mimi-reducer를 작성할 때, action.payload로 필요한 name, value 값이 넘겨져 온다고 가정하고 작성하자
    -   같은 name, value를 가진 car 정보가 중복되어 저장될 수 있기 때문에, cars state에 name, value 뿐만 아니라 동일한 정보를 구분할 수 있도록 id 값도 넣어줘야 함
    -   Redux-Toolkit은 랜덤으로 id를 만들어주는 `nanoid` function을 제공해 줌

    ```js
    // store/slices/carsSlice.js
    import { createSlice, nanoid } from "@reduxjs/toolkit";

    const carsSlice = createSlice({
    	name: "cars",
    	initialState: {
    		searchTerm: "",
    		cars: [],
    	},
    	reducers: {
    		changeSearchTerm(state, action) {
    			state.searchTerm = action.payload;
    		},
    		addCar(state, action) {
    			// Assumption:
    			// action.payload === { name: 'ab', cost: 140 }
    			state.cars.push({
    				name: action.payload.name,
    				cost: action.payload.cost,
    				id: nanoid(),
    			});
    		},
    		removeCar(state, action) {
    			// Assumption:
    			// action.payload === the id of the car we want to remove
    			const updated = state.cars.filter((car) => {
    				return car.id !== action.payload;
    			});
    			state.cars = updated;
    		},
    	},
    });

    export const { changeSearchTerm, addCar, removeCar } = carsSlice.actions;
    export const carsReducer = carsSlice.reducer;
    ```

## ▶ 326. Creating the Store

> 자료: [007\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/007_-_cars)

-   두 slices의 reducer를 담은 store을 작성해보자

    ```js
    // store/index.js
    import { configureStore } from "@reduxjs/toolkit";
    import {
    	carsReducer,
    	addCar,
    	removeCar,
    	changeSearchTerm,
    } from "./slices/carsSlice";
    import { formReducer, changeName, changeCost } from "./slices/formSlice";

    const store = configureStore({
    	reducer: {
    		cars: carsReducer,
    		form: formReducer,
    	},
    });

    export {
    	store,
    	changeName,
    	changeCost,
    	addCar,
    	removeCar,
    	changeSearchTerm,
    };
    ```

## ▶ 327. Form Values to Update State

> 자료: [008\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/008_-_cars)

-   CarForm component에서 car name input을 작성해보자

    ```js
    import { useDispatch, useSelector } from "react-redux";
    import { changeName } from "../store";

    function CarForm() {
    	const dispatch = useDispatch();
    	const name = useSelector((state) => {
    		return state.form.name;
    	});

    	const handleNameChange = (event) => {
    		dispatch(changeName(event.target.value));
    	};

    	return (
    		<div className="car-form panel">
    			<h4 className="subtitle is-3">Add Car</h4>
    			<form>
    				<div className="field-group">
    					<div className="field">
    						<label className="label">Name</label>
    						<input
    							className="input is-expanded"
    							value={name}
    							onChange={handleNameChange}
    						/>
    					</div>
    				</div>
    			</form>
    		</div>
    	);
    }

    export default CarForm;
    ```

## ▶ 328. Receiving the Cost

> 자료: [009\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/009_-_cars)

-   다음으로, car cost input을 작성해보자

    ```js
    import { changeName, changeCost } from '../store';

    function CarForm() {
    ...
    const { name, cost } = useSelector((state) => {
        return {
        name: state.form.name,
        cost: state.form.cost,
        };
    });
    ...
    const handleCostChange = (event) => {
        const carCost = parseInt(event.target.value) || 0;
        dispatch(changeCost(carCost));
    };

    return (
        <div className="car-form panel">
        <h4 className="subtitle is-3">Add Car</h4>
        <form>
            <div className="field-group">
                ...
                <div className="field">
                    <label className="label">Cost</label>
                    <input
                    className="input is-expanded"
                    value={cost || ''}
                    onChange={handleCostChange}
                    type="number"
                    />
                </div>
            </div>
        </form>
        </div>
    );
    }
    ```

## ▶ 329. Dispatching During the Form Submission

> 자료: [010\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/010_-_cars)

-   form이 submit됐을 때 cars state에 데이터가 추가되도록 하자

    ```js
    function CarForm() {
        ...
        const handleSubmit = (event) => {
            event.preventDefault();

            dispatch(addCar({ name, cost }));
        };

        return (
            <div className="car-form panel">
                <h4 className="subtitle is-3">Add Car</h4>
                <form onSubmit={handleSubmit}>
                    <div className="field-group">
                        ...
                        <div className="field">
                            <button className="button is-link">Submit</button>
                        </div>
                    </div>
                </form>
            </div>
        );
    }
    ```

## ▶ 330. Awkward Double Keys

> 자료: [011\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/011_-_cars)

-   FormList component에서 cars array를 가지고 오자

    -   entire state에서 해당 cars array를 가지고 오려면, `state.cars.cars`를 사용해야 함
    -   cars key를 두 번 중복되어 사용하는게 어색해 보이므로, carsSlice에서 cars key 이름을 data로 바꿔주자

    ```js
    const carsSlice = createSlice({
        name: 'cars',
        initialState: {
            searchTerm: '',
            data: [],
        },
        ...
    });
    ```

    ```js
    import { useSelector } from "react-redux";

    function CarList() {
    	const cars = useSelector((state) => {
    		return state.cars.data;
    	});

    	return <div>CarList</div>;
    }

    export default CarList;
    ```

## ▶ 331. Listing the Records

> 자료: [012\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/012_-_cars)

-   data state에서 받아온 데이터를 실제 화면에 나타내보자

    ```js
    function CarList() {
    ...
    const handleCarDelete = (car) => {
        // ...
    };

    const renderedCars = cars.map((car) => {
        return (
        <div key={car.id} className="panel">
            <p>
            {car.name} - ${car.cost}
            </p>
            <button
            className="button is-danger"
            onClick={() => handleCarDelete(car)}
            >
            Delete
            </button>
        </div>
        );
    });

    return (
        <div className="car-list">
        {renderedCars}
        <hr />
        </div>
    );
    }
    ```

## ▶ 332. Deleting Records

> 자료: [013\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/013_-_cars)

-   delete 버튼을 누르면 해당 car 정보가 state에서 삭제되게 하자

    ```js
    import { useSelector, useDispatch } from 'react-redux';
    import { removeCar } from '../store';

    function CarList() {
    const dispatch = useDispatch();
    ...
    const handleCarDelete = (car) => {
        dispatch(removeCar(car.id));
    };
    ...
    }
    ```

## ▶ 334. Adding Styling

> 자료: [014\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/014_-_cars)

-   src 폴더에 style.css를 추가해 스타일을 적용해보자

    ```js
    // src/index.js
    import 'bulma/css/bulma.css';
    import './styles.css';
    ...
    ```

## ▶ 335. Form Reset on Submission

-   form을 submit해 car list에 새로운 car을 추가하고 하면, 자동으로 form input란을 초기화시켜줘야 함
-   form을 submit할 때 여러 개의 dispatch를 호출해 직접 changeName(''), changeCost(0)을 작성함으로써 초기화시켜줄 순 있지만, Redux에서 dispatch를 여러 번 호출하는 것은 좋지 못한 방식임
-   대신, Form reducer가 'cars/addCar' action을 watch하고 있다가 해당 action으로 dispatch가 보내질 때마다 name과 cost state를 초기화해보자

## ▶ 336. Reminder on ExtraReducers

> 자료: [016\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/016_-_cars)

-   formSlice에서 `extraReducers` property를 추가해 'cars/addCar' action을 watch해보자

    ```js
    import { addCar } from './carsSlice';

    const formSlice = createSlice({
    ...
    extraReducers(builder) {
        builder.addCase(addCar, (state, action) => {
        state.name = '';
        state.cost = 0;
        });
    },
    });
    ```

## ▶ 337. Adding a Searching Input

> 자료: [017\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/017_-_cars)

-   CarSearch component에서 search input을 작성해보자

    ```js
    import { useDispatch, useSelector } from "react-redux";
    import { changeSearchTerm } from "../store";

    function CarSearch() {
    	const dispatch = useDispatch();
    	const searchTerm = useSelector((state) => {
    		return state.cars.searchTerm;
    	});

    	const handleSearchTermChange = (event) => {
    		dispatch(changeSearchTerm(event.target.value));
    	};

    	return (
    		<div className="list-header">
    			<h3 className="title is-3">My Cars</h3>
    			<div className="search field is-horizontal">
    				<label className="label">Search</label>
    				<input
    					className="input"
    					value={searchTerm}
    					onChange={handleSearchTermChange}
    				/>
    			</div>
    		</div>
    	);
    }

    export default CarSearch;
    ```

## ▶ 338. Derived State in useSelector

> 자료: [018\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/018_-_cars)

-   cars data state에서 search input에 입력되는 문자와 부분적으로 일치하는 car 정보만 화면에 나타나게 하자

    -   useSelector function의 인자인 selector function에서 바로 filtered data를 반환하게 하자

    ```js
    function CarList() {
    ...
    const cars = useSelector(({ cars: { data, searchTerm } }) => {
        return data.filter((car) =>
        car.name.toLowerCase().includes(searchTerm.toLowerCase())
        );
    });
    ...
    }
    ```

## ▶ 339. Total Car Cost

> 자료: [019\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/019_-_cars)

-   CarValue component를 작성해보자

    -   search input에 입력되는 문자와 부분적으로 일치하는 car list만 화면에 나타나게 되는데, 이렇게 filtered car list만 값을 모두 합해보자

    ```js
    import { useSelector } from "react-redux";

    function CarValue() {
    	const totalCost = useSelector(({ cars: { data, searchTerm } }) =>
    		data
    			.filter((car) =>
    				car.name.toLowerCase().includes(searchTerm.toLowerCase())
    			)
    			.reduce((acc, car) => acc + car.cost, 0)
    	);

    	return <div className="car-value">Total Cost: ${totalCost}</div>;
    }

    export default CarValue;
    ```

## ▶ 340. Highlighting Existing Cars

> 자료: [020\_-_cars](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/20.Managing_Multiple_Slices_with_Redux_Toolkit/020_-_cars)

-   cars data state에 form에서 작성한 name과 부분적으로 일치하는 car가 있으면 볼드 처리해보자

    ```js
    function CarList() {
        ...
        const { cars, name } = useSelector(({ form, cars: { data, searchTerm } }) => {
            ...
            return {
                cars: filteredCars,
                name: form.name,
            };
        });

        ...
        const renderedCars = cars.map((car) => {
            // DECIDE IF THIS CAR SHOULD BE BOLD
            const bold = name && car.name.toLowerCase().includes(name.toLowerCase());

            return (
            <div key={car.id} className={`panel ${bold && 'bold'}`}>
                ...
            </div>
            );
        });
        ...
    }
    ```
