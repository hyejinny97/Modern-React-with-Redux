# ✔ '18. Into the World of Reducers' 이론 정리

## ▶ 283. App Overview

-   `useReducer` hook

    -   Built-in React hook으로서, state를 관리하는 hook
    -   `useState`와 유사하게, component 내부에서 state를 정의함

-   앞으로의 강의 방향

    -   1. custom hooks을 사용하지 않고, `useState`만 사용해 CounterPage를 리팩토링
    -   2. `useReducer`을 사용해 CounterPage를 리팩토링

-   CounterPage 리팩토링 overview
    -   increment 버튼뿐만 아니라 decrement 버튼도 생성
    -   특정 값을 기존 count에 더하기 위해 입력값을 받는 form 생성

## ▶ 284. Adding the Form

> 자료: [002\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/002_-_red)

-   custom hooks을 사용하지 않고, `useState`만 사용해 CounterPage를 리팩토링
-   CounterPage component에 form을 생성하자

    ```js
    import Panel from "../components/Panel";

    function CounterPage({ initialCount }) {
    	const [count, setCount] = useState(initialCount);

    	const increment = () => {
    		setCount(count + 1);
    	};
    	const decrement = () => {
    		setCount(count - 1);
    	};

    	return (
    		<Panel className="m-3">
    			<h1 className="text-lg">Count is {count}</h1>
    			<div className="flex flex-row">
    				<Button onClick={increment}>Increment</Button>
    				<Button onClick={decrement}>Decrement</Button>
    			</div>

    			<form>
    				<label>Add a lot!</label>
    				<input
    					type="number"
    					className="p-1 m-3 bg-gray-50 border border-gray-300"
    				/>
    				<Button>Add it!</Button>
    			</form>
    		</Panel>
    	);
    }
    ```

## ▶ 285. More on the Form

> 자료: [003\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/003_-_red)

-   number input 값에 대한 state(valueToAdd)를 생성하고, change event를 watch하자
-   number input 값을 다룰 때 주의할 점

    -   1. 해당 입력값은 string으로 전환되어 저장되기 때문에, number 타입으로 다시 변경시켜줘야 함 👉 `parseInt()` 함수 사용
    -   2. 입력란에 숫자를 모두 지운 empty string 상태의 경우, number 타입으로 변환하면 NaN이 되는 문제 발생 👉 `Or operator (||)`를 사용해 parseInt('')인 경우 default 0을 반환
    -   3. 초기 입력란에 0을 지워도 사라지지 않고 남아있음 👉 `Or operator (||)`를 사용해 valueToAdd state가 0일 때, 입력란에 empty string을 반환

    ```js
    function CounterPage({ initialCount }) {
      ...
      const [valueToAdd, setValueToAdd] = useState(0);

      ...
      const handleChange = (event) => {
        const value = parseInt(event.target.value) || 0;

        setValueToAdd(value);
      };

      return (
        <Panel className="m-3">
          ...
          <form>
            <label>Add a lot!</label>
            <input
              value={valueToAdd || ""}
              onChange={handleChange}
              type="number"
              className="p-1 m-3 bg-gray-50 border border-gray-300"
            />
            <Button>Add it!</Button>
          </form>
        </Panel>
      );
    }
    ```

-   form이 submit되면 현재 count state에 입력값을 더해주고 입력란을 초기화해주자

    ```js
    function CounterPage({ initialCount }) {
      ...
      const handleSubmit = (event) => {
        event.preventDefault();

        setCount(count + valueToAdd);
        setValueToAdd(0);
      };

      return (
        <Panel className="m-3">
          ...
          <form onSubmit={handleSubmit}>
            ...
          </form>
        </Panel>
      );
    }
    ```

## ▶ 286. useReducer in Action

> 자료: [004\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/004_-_red)

-   `useReducer`을 사용해 위 CounterPage를 다시 리팩토링해보자
-   대게 하나의 component 내에는 useState나 useReducer 중 한 종류의 hook만 사용해 state를 정의함

### 🔹 `useState` vs `useReducer`

1. `useState`

    - component가 state를 필요로할 때 사용되는 hook
    - state를 정의함
    - 정의한 state가 변할 때 component가 rerender됨

2. `useReducer`

    - `useState`의 대안책으로서, 이 역시 component가 state를 필요로할 때 사용되는 hook
    - state를 정의함
    - 정의한 state가 변할 때 component가 rerender됨
    - 몇 가지 states가 서로 가깝게 연관되어 있을 때 사용하면 좋음
    - 미래 state value가 현재 state value에 의존할 때 사용하면 좋음

-   CounterPage의 count/valueToAdd state를 `useReducer`를 사용해 생성해보자

    -   CounterPage component에서 increment/decrement 버튼을 클릭할 때마다 현재 count 기준으로 1씩 증가/감소하기 때문에 미래 count value는 현재 count state에 의존하고 있음
    -   또한, form을 submit할 때마다 count state는 valueToAdd state만큼 값이 더해지므로, count state와 valueToAdd state는 서로 가깝게 연관되어 있음

-   `useState`처럼 `useReducer`도 state variable(state), state를 바꿔줄 function(dispatch), state 초기값({count:initialCount, valueToAdd:0})을 지정하게 됨

    -   단, `useState`는 각각의 state를 서로 다른 변수에 정의하는 반면, `useReducer`는 component 내 모든 state를 하나의 object에 정의함
    -   만약 한 component 내 `useState`을 사용해 정의한 state가 매우 많이 존재한다면, 개발 도중 디버깅을 위해 state를 콘솔창에 로깅할 때 일일이 모든 state 이름을 적어야 함
    -   하지만 `useReducer`을 사용해 state를 정의했다면, 단순히 object 형태의 state 변수 하나만 콘솔창에 로깅하면 되서 편리함

    ```js
    // useState
    const [count, setCount] = useState(initialCount);
    const [valueToAdd, setValueToAdd] = useState(0);
    ```

    ```js
    // useReducer
    const [state, dispatch] = useReducer(reducer, {
    	count: initialCount,
    	valueToAdd: 0,
    });
    ```

    ```js
    import { useReducer } from "react";

    const reducer = (state, action) => {};

    function CounterPage({ initialCount }) {
      const [state, dispatch] = useReducer(reducer, {
        count: initialCount,
        valueToAdd: 0,
      });

      console.log(state);
      // {count: 10, valueToAdd: 0}

      ...
    }
    ```

## ▶ 287. Rules of Reducer Functions

> 자료: [005\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/005_-_red)

-   `useState`에 의해 state가 update되는 과정

    -   1. 'setter function'에 state를 변경하고자 하는 값을 인자로 넣어 호출
    -   2. component가 rerender됨
    -   3. state가 변경됨

-   `useReducer`에 의해 state가 update되는 과정

    -   1. 'dispatch function'에 state를 변경하고자 하는 값을 인자로 넣어 호출
        -   dispatch 이름은 다른 이름으로 변경 불가
    -   2. dispatch는 reducer function을 찾아, 'object 형태의 현재 state'와 '인자로 받은 값'을 reducer의 인자로 넣어 호출
    -   3. reducer는 '현재 state'와 '변경하고자 하는 action'을 인자로 받아 **새로운 state**를 반환하게 됨
        -   reducer의 state와 action 인자명은 다른 이름으로 변경 가능(convention)
    -   4. component가 rerender됨
    -   5. state가 변경됨

-   dispatch function에는 인자를 넣지 않거나 하나를 넣을 수 있음
    -   만약, 두 개 이상의 인자를 넣었다면 React는 첫번째 하나의 인자만 가져와 reducer function의 action에 할당해줌

### 🔹 Reducer function에 대한 Rules

1. 어떤 것을 반환하든지, **반환값이 바로 새로운 state**가 됨
2. 아무것도 반환하지 않는다면, state는 undefined가 됨
3. reducer function 안에서 async/await, requests, promises를 사용할 수 없고, 외부 변수에 접근해서도 안됨
4. 인자로 받은 current state object 원본을 절대 직접 수정해서는 안됨

    - current state object 복사한 후, 새로운 object에 추가해 값을 변경해줘야 함

    ```js
    // Bad Code!
    const reducer = (state, action) => {
    	state.count = state.count + 1;
    	return state;
    };
    ```

    ```js
    // Good Code ('count'를 update)
    const reducer = (state, action) => {
    	return {
    		...state,
    		count: state.count + 1,
    	};
    };
    ```

    ```js
    // Good Code ('valueToAdd'를 update)
    const reducer = (state, action) => {
    	return {
    		...state,
    		valueToAdd: state.valueToAdd + 1,
    	};
    };
    ```

    ```js
    // Good Code (state reset)
    const reducer = (state, action) => {
    	return {
    		state: 0,
    		count: 0,
    	};
    };
    ```

## ▶ 288. Understanding Action Objects

> 자료: [006\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/006_-_red)

-   state object를 바꾸려면 항상 dispatch function을 호출해야 함

    -   즉, increment 버튼을 클릭하든지 number input을 change하든지 state를 바꾸려면 dispatch function을 호출해야 함

-   따라서, dispatch function을 통해 reducer function에 어떤 state를 어떻게 변화시키고자 하는지를 전달해야 함

    -   이를 해결할 수 있는 수많은 방법들이 존재
    -   그 중 React community가 자주 사용하는 communication convention이 존재하는데, 이 방법을 사용해 reducer function에게 필요한 정보를 전달하고 함

-   state를 변화시키고 싶을 때, dispatch function을 호출해 `action` object를 전달해주자

    -   `action` object는 `type` property와 `payload` property(optional)를 포함함
    -   `type` property: string 값을 지니고, reducer에게 어떤 state를 어떤 방식으로 update하고 싶은지 알려줌
    -   `payload` property: reducer에게 특정 값을 전달해 줌
    -   `type`, `payload` properties명은 다른 이름으로 변경 가능 (convention)
    -   이렇게 `action` object를 전달하는 방식은 흔한 community convention일 뿐, React에서 특별하게 작동하는 요소가 아님

    ```js
    const reducer = (state, action) => {
      if (action.type === 'increment') {
        return {
          ...state,
          count: state.count + 1,
        };
      }

      if (action.type === 'change-value-to-add') {
        return {
          ...state,
          valueToAdd: action.payload,
        };
      }

      return state;
    };

    function CounterPage({ initialCount }) {
      const [state, dispatch] = useReducer(reducer, {
        count: initialCount,
        valueToAdd: 0,
      });

      const increment = () => {
        dispatch({
          type: 'increment',
        });
      };
      ...
      const handleChange = (event) => {
        const value = parseInt(event.target.value) || 0;

        dispatch({
          type: 'change-value-to-add',
          payload: value,
        });
      };
      ...
    }
    ```

## ▶ 289. Constant Action Types

> 자료: [007\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/007_-_red)

-   위 코드에서처럼 action object의 type property를 string 형식으로 직접 작성하면, 오타 확률 때문에 추후에 버그를 일으킬 수도 있음
-   따라서, type을 직접 작성하는 대신 변수에 값을 할당하여 활용하는 방식을 이용해보자

    -   string을 직접 작성할 때와 달리, 변수명을 잘못 작성한 경우에는 해당 변수명이 존재하지 않는다는 error를 던져주어 쉽게 디버깅 가능함
    -   아래처럼 변수명을 모두 대문자로 적은 이유는, 값이 변하지 않는 변수임을 암시적으로 전달하기 위함임 (convention)

    ```js
    const INCREMENT_COUNT = 'increment';
    const SET_VALUE_TO_ADD = 'change_value_to_add';

    const reducer = (state, action) => {
      if (action.type === INCREMENT_COUNT) {
        ...
      }

      if (action.type === SET_VALUE_TO_ADD) {
        ...
      }

      return state;
    };

    function CounterPage({ initialCount }) {
      ...
      const increment = () => {
        dispatch({
          type: INCREMENT_COUNT,
        });
      };
      ...
      const handleChange = (event) => {
        const value = parseInt(event.target.value) || 0;

        dispatch({
          type: SET_VALUE_TO_ADD,
          payload: value,
        });
      };
      ...
    }
    ```

## ▶ 290. Refactoring to a Switch

> 자료: [008\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/008_-_red)

-   reducer function 내부에서 if문을 사용해 조건을 선별하는 것이 아닌, switch문을 사용하는 방법으로 리팩토링해보자

    ```js
    const reducer = (state, action) => {
    	switch (action.type) {
    		case INCREMENT_COUNT:
    			return {
    				...state,
    				count: state.count + 1,
    			};
    		case SET_VALUE_TO_ADD:
    			return {
    				...state,
    				valueToAdd: action.payload,
    			};
    		default:
    			return state;
    	}
    };
    ```

## ▶ 291. Adding New State Updates

> 자료: [009\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/009_-_red)

-   decrement 버튼 클릭과 form submit에 대한 state 변경도 처리해주자

    -   새로운 constant action type string 추가
    -   dispatch function 호출
    -   reducer function에 새로운 case statement 추가

    ```js
    const DECREMENT_COUNT = 'decrement';
    const ADD_VALUE_TO_COUNT = 'add_value_to_count';

    const reducer = (state, action) => {
      switch (action.type) {
        ...
        case DECREMENT_COUNT:
          return {
            ...state,
            count: state.count - 1,
          };
        case ADD_VALUE_TO_COUNT:
          return {
            ...state,
            count: state.count + state.valueToAdd,
            valueToAdd: 0,
          };
        ...
      }
    };

    function CounterPage({ initialCount }) {
      ...
      const decrement = () => {
        dispatch({
          type: DECREMENT_COUNT,
        });
      };
      const handleSubmit = (event) => {
        event.preventDefault();

        dispatch({
          type: ADD_VALUE_TO_COUNT,
        });
      };
      ...
    }
    ```

## ▶ 292. A Few Design Considerations Around Reducers

-   보통 reducer function에 많은 logic을 넣는 반면, dispatch function에는 간단하게 넣음

    -   이유) 하나의 reducer에 여러 dispatch function을 동일한 type으로 호출한다고 가정했을 때, dispatch에 많은 logic을 중복되게 넣는 것은 추후에 버그가 발생할 가능성을 높임
    -   따라서, 동일한 type을 가진 여러 dispatch가 같은 action을 하기 원한다면 dispatch에 인자로 넣어주는 action object를 가능한 simple하게 두는 것이 좋음
    -   대신, reducer는 매우 구체적인 state 변경 logics을 가지고 있어야 함

-   아래 코드와 같이 dispatch action object에 많은 정보를 담는 것은 추천하지 않음!

    ```js
    // Dispatch
    dispatch({
    	type: SET_COUNT_AND_RESET_VALUETOADD,
    	payload: state.count + state.valueToAdd,
    });
    ```

    ```js
    // Reducer
    case SET_COUNT_AND_RESET_VALUETOADD:
      return {
        ...state,
        count: action.payload,
        valueToAdd: 0,
      }
    ```

## ▶ 293. Introducing Immer

> 자료: [011\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/011_-_red)

-   `immer` 라이브러리를 사용하면, reducer 내부에서 state object 원본을 직접 변경(mutate)해도 괜찮음

    -   `immer` 라이브러리가 state mutation을 자동으로 인식해서 새로운 state를 생성해 React에게 알려줌

-   `immer` 라이브러리를 사용하지 않는 'Normal Reducer'의 경우

    -   state object를 직접 변경해서는 안됨
    -   새로운 state를 반환해줘야 함

    ```js
    const reducer = (state, action) => {
    	switch (action.type) {
    		case INCREMENT_COUNT:
    			return {
    				...state,
    				count: state.count + 1,
    			};
    		default:
    			return state;
    	}
    };
    ```

-   `immer` 라이브러리를 사용하는 'Reducer'의 경우

    -   state object를 직접 변경해도 괜찮음
    -   return 키워드 뒤에 반환값을 적지 않아도 됨
    -   단, switch ~ case문에서 return 키워드는 반드시 작성해야함

    ```js
    const reducer = (state, action) => {
    	switch (action.type) {
    		case INCREMENT_COUNT:
    			state.count = state.count + 1;
    			return;
    		default:
    			return;
    	}
    };
    ```

-   아래 명령어를 사용해 `immer` 라이브러리를 설치하자

    ```bash
    $ npm install immer
    ```

## ▶ 294. Immer in Action

> 자료: [012\_-_red](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/18._Into_the_World_of_Reducers/012_-_red)

-   `immer` 라이브러리를 사용해 Reducer를 리팩토링해보자

    -   `immer` 라이브러리에서 produce function을 import해 reducer를 감싸주면 사용 가능

    ```js
    import produce from 'immer';

    const reducer = (state, action) => {
      switch (action.type) {
        case INCREMENT_COUNT:
          state.count = state.count + 1;
          return;
        case DECREMENT_COUNT:
          state.count = state.count - 1;
          return;
        case ADD_VALUE_TO_COUNT:
          state.count = state.count + state.valueToAdd;
          state.valueToAdd = 0;
          return;
        case SET_VALUE_TO_ADD:
          state.valueToAdd = action.payload;
          return;
        default:
          return;
      }
    };

    function CounterPage({ initialCount }) {
      const [state, dispatch] = useReducer(produce(reducer), {
        count: initialCount,
        valueToAdd: 0,
      });
      ...
    }
    ```
