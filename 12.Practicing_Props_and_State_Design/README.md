# ✔ '12. Practicing Props and State Design' 이론 정리

## ▶ 191. Component Overview

-   Dropdown component를 만들어보자
    -   select 박스를 클릭하면 red, green, blue 세 가지 컬러를 선택할 수 있게 나타나게 됨

## ▶ 192. Designing the Props

-   재사용 가능한 Dropdown component를 만들어보자
-   Dropdown component는 리스트에 나열할 options을 props로 전달받아야 함
-   options prop은 어떤 자료형으로 구성되는 것이 좋을까?
-   선택1) list of plain stings

    -   나중에 내가 선택한 option이 무엇인지 비교해야할 때, 아래와 같이 긴 string을 가지고 비교를 하면 오타 가능성이 있으므로 버그 발생 확률이 높아짐

    ```js
    // Bad Idea!!
    ["I want mild", "I'd like spicy", "Give me extra spicy"];
    ```

    ```js
    if (selected === "Give me extra spicy") {
    	makeFoodSpicy();
    }
    ```

-   선택2) list of objects

    -   `label`: 화면에 나타낼 text
    -   `value`: 선택한 option이 무엇인지 비교하기 위해 사용될 text

    ```js
    // Good Idea!!
    [
    	{ label: "I want mild", value: "mild" },
    	{ label: "I'd like spicy", value: "spicy" },
    	{ label: "Give me extra spicy", value: "extra_spicy" },
    ];
    ```

    ```js
    if (selected.value === "spicy") {
    	makeFoodSpicy();
    }
    ```

## ▶ 193. Component Creation

> 자료: [003\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/003_-_dd)

-   components 폴더에 `Dropdown.js` 파일을 추가하자
-   App.js에서 Dropdown component에 options prop을 전달해주자

```js
function Dropdown({ options }) {
	return <div>Dropdown...</div>;
}

export default Dropdown;
```

```js
import Dropdown from "./components/Dropdown";

function App() {
	const options = [
		{ label: "Red", value: "red" },
		{ label: "Green", value: "green" },
		{ label: "Blue", value: "blue" },
	];

	return <Dropdown options={options} />;
}

export default App;
```

## ▶ 194. [Optional] More State Design

-   state design process (8단계)

    -   1 ~ 3단계: 어떤 state, event handler가 있어야 할까?
    -   4 ~ 7단계: state는 어떤 이름과 type을 가지는게 좋을까?
    -   8단계: state와 event handler는 어느 component에 두는 것이 좋을까?

-   state의 type은 number, boolean, string, array, object 등 모두 가능함
    -   하지만, 이상적으로 state의 type은 array나 object는 피하는게 좋음

### 🔹 Events + State Design Process

1. 사용자가 어떤 반응(클릭, 드래그 등)을 하면, 어떤 변화가 일어나는지 적어보자

    ```
    dropdown을 클릭한다.
    options list가 나타난다.
    option을 클릭한다.
    options list가 사라진다.
    클릭한 option이 박스에 나타난다.
    ```

2. 각 step을 'state'나 'event handler'로 카테고리화 해보자

    - state: 화면에서 변하는 것
    - event handler: 사용자의 행동

    ```
    dropdown을 클릭한다.             👉 event handler
    options list가 나타난다.         👉 state
    option을 클릭한다.               👉 event handler
    options list가 사라진다.         👉 state
    클릭한 option이 박스에 나타난다. 👉 state
    ```

3. 1**공통 step들을 묶고, 2**중복된 것을 제거하고, 3\_\_재정의 해보자

    ```
    1) 공통 step들을 묶자
    dropdown을 클릭한다.             👉 event handler

    option을 클릭한다.               👉 event handler

    options list가 나타난다.         👉 state
    options list가 사라진다.         👉 state

    클릭한 option이 박스에 나타난다. 👉 state
    ```

    ```
    2) 중복된 것을 제거하자
    dropdown을 클릭한다.             👉 event handler

    option을 클릭한다.               👉 event handler

    options list가 나타난다.         👉 state

    클릭한 option이 박스에 나타난다. 👉 state
    ```

    ```
    3) 재정의 해보자
    dropdown을 클릭한다.             👉 event handler

    option을 클릭한다.               👉 event handler

    menu가 open/closed된다.          👉 state

    한 option이 선택된다.            👉 state
    ```

4. mockup을 보면서 변하지 않는 것들은 제거해보자

5. 나머지 요소들을 간단한 text로 대체해보자

    ```
    menu closed, no option selected
    menu open, no option selected
    menu closed, 'A little spicy' option selected
    ```

6. 변화된 상황이라 가정하고, 4번과 5번 과정을 반복하자

7. 5번과 6번의 step들의 text를 반환하는 함수를 만든다고 생각해보자.이 함수에 component의 props와 다른 인자들을 넣어야 한다면 어떤 것을 넣는 것이 좋을까?

    - 넣어야하는 인자의 이름과 type을 state의 이름과 type으로 결정하면 됨
    - state
        - name: `selected`, type: option | null
        - name: `isOpen`, type: boolean
    - event
        - option 클릭 event handler name: `handleSelect`
        - dropdown 클릭 event handler name: `handleClick`

    ```js
    // Dropdown의 props
    const options = [
    	{ label: "Red", value: "red" },
    	{ label: "Green", value: "green" },
    	{ label: "Blue", value: "blue" },
    ];
    ```

    ```js
    // state 1) menu open/closed
    function myFunction(options /* ?? */) {}

    myFunction(options /* ?? */); // 'menu closed'
    myFunction(options /* ?? */); // 'menu open'
    ```

    ```js
    function myFunction(options, isOpen) {
    	if (isOpen) {
    		return "menu open";
    	} else {
    		return "menu closed";
    	}
    }

    myFunction(options, false); // 'menu closed'
    myFunction(options, true); // 'menu open'
    ```

    ```js
    // state 2) option selected
    function myFunction(options /* ?? */) {}

    myFunction(options /* ?? */); // 'no item selected'
    myFunction(options /* ?? */); // 'a little spicy'
    ```

    ```js
    function myFunction(options, selected) {
    	if (!selected) {
    		return "no item selected";
    	} else {
    		return selected.label;
    	}
    }

    myFunction(options, null); // 'no item selected'
    myFunction(options, options[1]); // 'a little spicy'
    ```

8. 각각의 state와 event handler는 어디에 위치하는 것이 좋을지 결정해보자

    - 현재 우리 project에서 `isOpen` state/`handleClick` event handler는 Dropdown component 이외의 다른 component에서는 필요하지 않으므로 Dropdown에 정의하는 것이 좋을 것 같음
    - 아래와 같은 장바구니 페이지를 생각했을 때, `selected` state/`handleSelected` event handler는 Dropdown component 이외의 다른 component에서도 필요하므로 parent component에 정의하는 것이 좋을 것 같음

    ```
    App → ShoppingCartPage → ProductList → Dropdown
                                         ↳ Dropdown
                           ↳ CartTotal
    ```

## ▶ 195. Finally... Implementation!

> 자료: [005\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/005_-_dd)

-   Dropdown component에 `isOpen` state와 `handleClick` event handler를 구현하자

    -   'Select...' 박스를 클릭할 때마다 options list가 나타나거나 사라지게 됨

    ```js
    function Dropdown({ options }) {
    	const [isOpen, setIsOpen] = useState(false);

    	const handleClick = () => {
    		setIsOpen(!isOpen);
    	};

    	const renderedOptions = options.map((option) => {
    		return <div key={option.value}>{option.label}</div>;
    	});

    	return (
    		<div>
    			<div onClick={handleClick}>Select...</div>
    			{isOpen && <div>{renderedOptions}</div>}
    		</div>
    	);
    }
    ```

## ▶ 196. Reminder on Event Handlers in Maps

> 자료: [006\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/006_-_dd)

-   'Select...' 박스뿐만 아니라 options를 클릭할 때도 option list가 closed되어야 함

    -   이때, 클릭한 option에 대한 정보도 같이 넘겨줘야 함

    ```js
    function Dropdown({ options }) {
      ...
      const handleOptionClick = (option) => {
        // CLOSE DROPDOWN
        setIsOpen(false);
        // WHAT OPTION DID THE USER CLICK ON???
        console.log(option);
      };

      const renderedOptions = options.map((option) => {
        return (
          <div onClick={() => handleOptionClick(option)} key={option.value}>
            {option.label}
          </div>
        );
      });
      ...
    }
    ```

## ▶ 197. Dropdown as a Controlled Component

-   Text inputs를 controlled component로 처리하는 것처럼, dropdown도 controlled component로 처리해야함

## ▶ 198. Controlled Component Implementation

> 자료: [008\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/008_-_dd)

-   App component에 `selection` state와 `handleSelect` event handler를 생성하자
-   Dropdown component로 `selection`과 `handleSelect`를 전달해주자
-   options을 클릭하면, 클릭한 option을 handleSelect의 인자로 전달해 `selection` state를 변경시키고 그 값에 따라 박스의 text를 변화시키자

    ```js
    function App() {
      const [selection, setSelection] = useState(null);

      const handleSelect = (option) => {
        setSelection(option);
      };
      ...
      return (
        <Dropdown options={options} selection={selection} onSelect={handleSelect} />
      );
    }
    ```

    ```js
    function Dropdown({ options, selection, onSelect }) {
      ...
      const handleOptionClick = (option) => {
        // CLOSE DROPDOWN
        setIsOpen(false);
        // WHAT OPTION DID THE USER CLICK ON???
        onSelect(option);
      };
      ...
      let content = 'Select...';
      if (selection) {
        content = selection.label;
      }

      return (
        <div>
          <div onClick={handleClick}>{content}</div>
          {isOpen && <div>{renderedOptions}</div>}
        </div>
      );
    }
    ```

## ▶ 199. Existence Check Helper

> 자료: [009\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/009_-_dd)

-   `?.` optional chaining 연산자와 `||` 연산자를 사용해 위 Dropdown component에서 content 값을 구현하는 부분의 코드를 리팩토링해보자

    ```js
    function Dropdown({ options, selection, onSelect }) {
      ...
      return (
        <div>
          <div onClick={handleClick}>{selection?.label || 'Select...'}</div>
          {isOpen && <div>{renderedOptions}</div>}
        </div>
      );
    }
    ```

## ▶ 200. Community Convention with Props Names

> 자료: [010\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/010_-_dd)

-   보통 input, checkbox, radio buttons, slider(range), dropdown 등과 같은 form control components는 비슷한 패턴의 props name을 지음 (by convention)

    -   current value에 대한 prop name 👉 `value`
    -   value changed에 대한 prop name 👉 `onChange`

    ```js
    function App() {
      ...
      return (
        <Dropdown options={options} value={selection} onChange={handleSelect} />
      );
    }
    ```

    ```js
    function Dropdown({ options, value, onChange }) {
      ...
    }
    ```

## ▶ 202. Adding Styling

> 자료: [012\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/012_-_dd)

-   tailwindCSS를 사용해서 스타일링해보자
-   아래 각 요소의 className에 사용된 css는 너무 길 뿐만 아니라 중복된 것들이 많이 존재하는 것을 볼 수 있음

    ```js
    import { GoChevronDown } from 'react-icons/go';

    function Dropdown({ options, value, onChange }) {
      ...
      const renderedOptions = options.map((option) => {
        return (
          <div
            className="hover:bg-sky-100 rounded cursor-pointer p-1"
            onClick={() => handleOptionClick(option)}
            key={option.value}
          >
            {option.label}
          </div>
        );
      });

      return (
        <div className="w-48 relative">
          <div
            className="flex justify-between items-center cursor-pointer border rounded p-3 shadow bg-white w-full"
            onClick={handleClick}
          >
            {value?.label || 'Select...'}
            <GoChevronDown className="text-lg" />
          </div>
          {isOpen && (
            <div className="absolute top-full border rounded p-3 shadow bg-white w-full">
              {renderedOptions}
            </div>
          )}
        </div>
      );
    }
    ```

## ▶ 203. The Panel Component

-   위 dropdown component 내 일부 클래스에서 'border rounded p-3 shadow bg-white w-full'이 중복되는데, 이 부분은 panel을 나타내는 부분임
-   이 부분만 따로 떼어내어 panel component를 생성해 아래처럼 재사용할 수 있게 하자

    ```js
    // ex1
    <Panel>
    	<h1>Hi there!</h1>
    </Panel>
    ```

    ```js
    // ex2
    <Panel>
    	<label>Are you sure?</label>
    	<button>Yes</button>
    </Panel>
    ```

### 🔹 Reusable Presentation Components 만드는 법

1. JSX elements를 반환하는 새로운 component를 만든다
2. component가 `children` prop을 받아 적용한다
3. component가 추가 `className`을 받아 기존의 className과 병합한다
4. component가 추가 `props`를 받아 적용한다

## ▶ 203. The Panel Component

> 자료: [014\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/014_-_dd)

-   panel component를 만들고 dropdown component에 활용해보자

    ```js
    // Panel.js
    import classNames from "classnames";

    function Panel({ children, className, ...rest }) {
    	const finalClassNames = classNames(
    		"border rounded p-3 shadow bg-white w-full",
    		className
    	);

    	return (
    		<div {...rest} className={finalClassNames}>
    			{children}
    		</div>
    	);
    }

    export default Panel;
    ```

    ```js
    // Dropdown.js
    import Panel from './Panel';

    function Dropdown({ options, value, onChange }) {
      ...
      return (
        <div className="w-48 relative">
          <Panel
            className="flex justify-between items-center cursor-pointer"
            onClick={handleClick}
          >
            {value?.label || 'Select...'}
            <GoChevronDown className="text-lg" />
          </Panel>
          {isOpen && <Panel className="absolute top-full">{renderedOptions}</Panel>}
        </div>
      );
    }
    ```

-   panel component와 같이 Reusable Presentation Components를 만들면, app 전반에 consistence styling을 줄 수 있다는 이점이 있음

## ▶ 205. A Challenging Extra Feature

-   App.js에서 또다른 Dropdown component를 사용할 경우, 두 개의 dropdown 중 하나에서 option을 선택하면 다른 dropdown도 선택한 그 option이 나타나게 된다

    -   하나의 selection state가 두 Dropdown component에 영향을 주기 때문

-   user가 화면에서 Dropdown component 밖의 아무 지점을 클릭했을 때도, Dropdown의 option list가 닫히도록 해보자 (`isOpen` state ⇒ false)
    -   plain JS에서는 어떻게 구현할 수 있을까?
        -   Document-wide click handlers
        -   Event capturing/bubbling
        -   Checking element inclusion
    -   React에서는 어떻게 구현할 수 있을까?
        -   React's `useEffect` hook
        -   React's `useRef` hook

## ▶ 206. Document-Wide Click Handlers

-   아래 코드를 통해 user가 화면의 어느 지점을 클릭했는지 알 수 있음

    ```js
    const handleClick = (e) => {
    	console.log(e.target);
    };

    document.addEventListener("click", handleClick);
    ```

## ▶ 207. Event Capture and Bubbling

-   event가 발생했을 때, 브라우저는 `Capture Phase` → `Target Phase` → `Bubble Phase` 세 단계 순서대로 호출할 event handler를 찾게 됨

    -   `Capture Phase`: 브라우저는 event가 일어난 target 요소의 최상단 조상 요소에서부터 아래 요소로 내려가면서 event handler를 찾게 됨
    -   `Target Phase`: 브라우저는 event가 일어난 바로 그 target 요소에서 event handler를 찾게 됨
    -   `Bubble Phase`: 브라우저는 event가 일어난 target 요소의 부모 요소에서부터 위 요소로 올라가면서 event handler를 찾게 됨

-   아래와 같이 body > div > button element가 있고, button에 click event가 일어난다고 가정해보자

    -   첫 번째) `Capture Phase`: body → div
    -   두 번째) `Target Phase`: button
    -   세 번째) `Bubble Phase`: div → body

    ```html
    <body>
    	<div>
    		<button>Click Me!</button>
    	</div>
    </body>
    ```

-   `addEventListener()` 함수의 세 번째 인자는 bubble/capture를 조절함

    -   `element.addEventListener(event, handler, capture phase)`
    -   기본값: `capture phase = false`
    -   보통은 capture phase가 아닌 bubble phase를 탐색하는 경우가 많음
    -   하지만, 현재 Dropdown project에선 capture phase를 탐색할 예정

## ▶ 208. Putting it All Together

-   Document-wide click handlers, Event capturing/bubbling, Checking element inclusion을 사용해 user가 화면에서 Dropdown component 밖의 지점을 클릭했는지 확인해보자

    -   document에 click event listener를 달 때, 반드시 `capture phase = true`이어야 함

    ```js
    const dropdown = document.querySelector(".w-48");

    const handleClick = (e) => {
    	if (dropdown.contains(e.target)) {
    		console.log("Inside dropdown");
    	} else {
    		console.log("OUTSIDE Dropdown");
    	}
    };

    document.addEventListener("click", handleClick, true);
    ```

## ▶ 209. Why a Capture Phase Handler?

-   가정) document에 capture phase가 아닌 bubbling phase에 click event listener를 달아보자

    -   문제점) option list에 option을 클릭하면, 콘솔창에 "Inside dropdown"가 아닌 "OUTSIDE Dropdown"가 뜨는 것을 확인할 수 있다

-   이유) 각 option에 연결된 click event listener가 먼저 호출되어 dropdown이 close된 후, document에 연결된 click event listener가 호출되기 때문에 `e.target`은 dropdown 바깥 요소가 됨

    -   1. user가 option을 클릭
    -   2. 브라우저가 option div에 연결된 click event handler를 발견
    -   3. react가 이 click event handler를 호출
    -   4. `isOpen` state가 false로 update되지만, 아직 react는 rerender하지 않음
    -   5. 일정 시간이 흐른 후, react는 마침내 component를 rerender함
    -   6. 좀 더 시간이 흐른 후, 브라우저가 document에 연결된 click event listener를 발견하고 호출

    ```
    <- Capture -><- Target -><- Bubble ->
    body → div → option div → div → body
                    👇              👇
            Click Event Handler    Click Event Handler
    ```

-   `performance.now()`를 사용하면, `isOpen` state가 false로 update된 후 component rerender가 document에 연결된 click event listener 호출보다 먼저 일어난다는 것을 알 수 있음

-   따라서, 제대로 작동하기 위해선 document의 capture phase에 click event listener를 달아야 함

    ```
    <- Capture -><- Target -><- Bubble ->
    body → div → option div → div → body
     👇                  👇
    Click Event Handler  Click Event Handler
    ```

## ▶ 210. Reminder on the useEffect Function

> 자료: [020\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/020_-_dd)

-   React에서도 user가 화면에서 Dropdown component 밖의 지점을 클릭했는지 여부를 구현하는 코드를 작성해보자

    -   `useEffect` hook을 사용해 첫번째 render될 때만 document에 click event listener가 연결되도록 하자

    ```js
    import { useState, useEffect } from 'react';

    function Dropdown({ options, value, onChange }) {
      ...
      useEffect(() => {
        const handler = (event) => {
          console.log(event.target);
        };

        document.addEventListener('click', handler, true);
      }, []);
      ...
    }
    ```

## ▶ 211. Reminder on useEffect Cleanup

> 자료: [021\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/021_-_dd)

-   나중에 Dropdown component가 제거될 때, document에 연결된 click event listener가 같이 삭제되도록 하기 위해 useEffect cleanup을 활용하자

    -   1st render: useEffect function 호출됨, cleanup function 반환 (return)
    -   component가 제거될 때: 이전에 반환한 cleanup function을 호출 (call)

    ```js
    useEffect(() => {
    	// ...do something...

    	const cleanUp = () => {};

    	return cleanUp;
    }, []);
    ```

    ```js
    function Dropdown({ options, value, onChange }) {
      ...
      useEffect(() => {
        const handler = (event) => {
          console.log(event);
        };

        document.addEventListener('click', handler, true);

        return () => {
          document.removeEventListener('click', handler);
        };
      }, []);
      ...
    }
    ```

## ▶ 212. Issues with Element References

-   Dropdown component를 한번에 여러 개 사용한다고 가정해보자

    -   첫 번째 render때 각 Dropdown component의 useEffect가 실행되어 component 개수만큼 document click event listener가 호출되게 됨
    -   위 plain JS일 때의 코드에서처럼 단순히 `const dropdown = document.querySelector(".w-48");`을 사용하게 되면, 모든 Dropdown component가 첫 번째 dropdown만을 참조하게 되는 문제가 발생
    -   그러면, 첫번째를 제외한 두번째 이상의 모든 Dropdown component들은 본인의 dropdown을 클릭해도 outside로 인식하게 됨

-   따라서 각 Dropdown component마다 자신의 element를 참조하게 하여, 자신의 Dropdown component에서 click event가 일어났는지 알게 하는 것이 중요

## ▶ 213. useRef in Action

> 자료: [023\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/023_-_dd)

-   각 Dropdown component마다 자신의 element를 참조하게 하기 위해서 `useRef` hook을 사용해보자

### 🔹 `useRef` hook

-   특정 component가 자신이 생성한 DOM element를 참조(reference)할 수 있게 해주는 function
-   대게 DOM element를 참조하기 위해 사용되지만, 드물게 어떤 value를 참조하기 위해서 사용되기도 함

-   `useRef` hook 실행 방법

    -   1. component 상단부에 `useRef` hook을 호출하여 'ref' 변수에 할당한다.
    -   2. 참조하고자하는 JSX element에 `ref` prop을 사용해 위에서 생성한 'ref' 변수를 값으로 넣어준다.
    -   3. `ref.current`를 통해 참조한 DOM element에 접근 할 수 있다.

    ```js
    import { useState, useEffect, useRef } from 'react';

    function Dropdown({ options, value, onChange }) {
      const divEl = useRef();

      useEffect(() => {
        const handler = (event) => {
          console.log(divEl);         // {current: div.w-48.relative}
          console.log(divEl.current); // <div ref={divEl} className="w-48 relative">...</div>
        };

        document.addEventListener('click', handler, true);

        return () => {
          document.removeEventListener('click', handler);
        };
      }, []);
      ...
      return (
        <div ref={divEl} className="w-48 relative">
          ...
        </div>
      );
    }
    ```

## ▶ 214. Checking Click Location

> 자료: [024\_-_dd](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/12.Practicing_Props_and_State_Design/024_-_dd)

-   user가 자신의 dropdown 밖의 지점을 클릭할 경우, 자신의 dropdown을 close하는 코드를 마저 구현하자

    ```js
    function Dropdown({ options, value, onChange }) {
      const divEl = useRef();

      useEffect(() => {
        const handler = (event) => {
          if (!divEl.current) return;

          if (!divEl.current.contains(event.target)) {
            setIsOpen(false);
          }
        };

        document.addEventListener('click', handler, true);

        return () => {
          document.removeEventListener('click', handler);
        };
      }, []);
      ...
    }
    ```
