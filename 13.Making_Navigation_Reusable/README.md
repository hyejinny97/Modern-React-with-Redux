# ✔ '13. Making Navigation Reusable' 이론 정리

## ▶ 215. Traditional Browser Navigation

-   Traditional Navigation 원칙: 서버로 request를 보내 새로운 HTML document를 response 받아 화면을 새로 로드함
-   Standard Browser Behavior: 브라우저가 새로운 HTML document를 로드할 때, 기존의 JS 변수나 코드가 모두 사라지게 됨

    -   JS나 React 등을 사용하지 않은 'traditional HTML focused app'에서는 큰 문제가 되지 않음
    -   반면, 'React app'에서는 서버로 request하는 횟수가 많아진다는 문제가 발생할 수 있음

-   ex) Traditional Navigation 원칙을 따라 blog posts나 image list를 보여주는 app이 있다고 가정

    -   'traditional HTML focused app'의 경우, 화면에 blog posts를 보여주기 위해 `posts.html` 하나, image list를 보여주기 위해선 `images.html` 하나만 response 받게 됨 👉 1 request로 user는 빠르게 결과를 받아볼 수 있음
    -   'React app'의 경우, blog posts를 보여주기 위해 `index.html`/`bundle.js`/`posts data`(API) 3번의 request가 필요하고, image list를 보여주기 위해서 또한 `index.html`/`bundle.js`/`images data`(API) 3번의 request가 필요하게 됨 👉 많은 request를 요하게 됨

-   따라서, 'React app'에서는 Traditional Navigation 원칙이 아닌 새로운 Navigation 이론이 필요함

## ▶ 216. Theory of Navigation in React

### 🔹 React에서 Navigation이 작동하는 방식

1. our app에 처음 들어온 경우

    - 주소창에 our address를 쳐서 들어온 경우이거나, 다른 웹사이트에 있다가 링크를 통해 our app에 들어온 경우를 의미함
    - slash `/` 뒤에 어떤 경로이든지 항상 서버에서 `index.html`/`bundler.js` 파일을 보내줌
    - 이후 app이 로드될 때, react는 주소창을 확인해 어떤 content를 보여줘야할지 결정하게 됨

2. user가 our app에 있는 상태에서, 링크를 클릭하거나 'back' button을 누른 경우

    - 일단 브라우저의 default page-switching behavior를 막음
    - user가 어디로 이동하려는지 확인함
    - user가 다른 페이지로 이동했다고 느낄 수 있게, 화면에 content를 update함
    - user가 다른 페이지로 이동했다고 느낄 수 있게, 주소창을 update함

-   즉, React에서의 Navigation은 서버에 request를 보내 새로운 HTML 파일을 받는 것이 아니라, 화면과 주소창을 update해주는 방식으로 작동함

-   React에서의 Navigation 이점: 다른 경로로 이동하더라도, JS 파일이 더이상 reset되지 않기 때문에 기존 데이터(state)가 남아있게 되어 'back' button을 눌러 이전 페이지로 돌아갈 때 또다시 request를 보내 데이터를 받지 않아도 됨 👉 화면이 빠르게 update됨
    -   단, 데이터(state)가 component가 아닌 context와 같이 외부에 저장된 경우 지워지지 않고 남아있을 수 있음

## ▶ 217. Extracting the DropdownPage

> 자료: [003\_-_nav](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/13._Making_Navigation_Reusable/003_-_nav)

-   user에게 links를 제공해주는 'SideBar' component가 필요하고, 각 link를 클릭했을 때 ButtonPage/AccordionPage/DropdownPage 등 화면에 Page component를 update해야 함

    ```
    App → SideBar
        ↳ ButtonPage
        ↳ AccordionPage
        ↳ DropdownPage
    ```

## ▶ 218. Answering Critical Questions

-   어떻게 React Navigation 작동 방식에 따라 구현할 수 있을까?

-   1-1) slash `/` 뒤에 어떤 경로이든지 항상 서버에서 `index.html`/`bundler.js` 파일을 보내줌

    -   👉 Create React App이 자동으로 해줌

-   1-2) 이후 app이 로드될 때, react는 주소창을 확인해 어떤 content를 보여줘야할지 결정하게 됨

    -   질문) 어떻게 주소창을 확인할 수 있을까?
    -   질문) 구체적으로 주소창의 어느 부분을 확인해야할까?
    -   👉 port나 domain 부분 뒤의 path 경로를 확인해야함
    -   👉 `window.location.pathname`을 통해 path 경로 확인 가능

## ▶ 219. The PushState Function

-   2-4) user가 다른 페이지로 이동했다고 느낄 수 있게, 주소창을 update함
    -   질문) 어떻게 주소창을 update할 수 있을까?
    -   방법1) `window.location = 'http://localhost:3000/dropdown';`
    -   방법2) `window.history.pushState({}, '', '/dropdown');`
    -   👉 `window.location` 방법은 page refresh가 일어나기 때문에 좋지 못한 방법임
    -   👉 `window.history.pushState()` 방법은 page refresh 없이 주소창을 바꿔주므로 이 방법을 선택하는 것이 좋음

## ▶ 220. Handling Link Clicks

-   2-1) 브라우저의 default page-switching behavior를 막음

    -   질문) user가 'link'를 클릭하는 것을 어떻게 감지할 수 있을까?
    -   👉 `e.preventDefault()`가 포함된 Link component를 만들어, our app 외부를 이동하는 link는 plain a tag를 사용하는 반면, our app 내부를 navigation하는 link에는 이 Link component를 사용하자

    ```js
    function Link({ to }) {
    	const handleClick = (e) => {
    		e.preventDefault();

    		console.log(`User navigating to: ${to}`);
    	};

    	return (
    		<a onClick={handleClick} href={to}>
    			Click
    		</a>
    	);
    }
    ```

## ▶ 221. Handling Back:Forward Buttons

-   2-1) 브라우저의 default page-switching behavior를 막음

    -   질문) Back/Forward Button을 클릭했을 때 발생되는 default page-switching behavior를 어떻게 막을 수 있을까?
    -   👉 Browser History는 stack 형식으로 지나온 url을 기록하게 됨
    -   👉 our app을 navigation할 때, `window.history.pushState()`을 사용해 주소창을 바꿔주는 방식을 사용했다면 Back/Forward Button을 클릭해도 page refresh 없이 이전/다음 페이지로 이동 가능함
    -   👉 단, `pushState()`에 의해 추가된 path가 아닌 url을 직접 타이핑한 경우에는 page refresh가 발생함
    -   질문) user가 'Back/Forward Button'을 클릭하는 것을 어떻게 감지할 수 있을까?
    -   👉 `window.history.pushState()`을 사용해 주소창을 변경했다면, window에서 일어나는 `popstate` event를 watch함으로써 감지할 수 있음
    -   👉 단, `pushState()`에 의해 추가된 path가 아닌 url을 직접 타이핑한 경우에는 `popstate` event가 발생하지 않음

    ```
    Browser History: localhost:3000 ⇆ localhost:3000/accordion ⇆ /a1 ⇆ /b2
    ```

    ```js
    window.addEventListener("popstate", () =>
    	console.log(`I'm at ${window.location.pathname}`)
    );
    ```

## ▶ 222. Navigation Context

> 자료: [008\_-_nav](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/13._Making_Navigation_Reusable/008_-_nav)

-   2-3) user가 다른 페이지로 이동했다고 느낄 수 있게, 화면에 content를 update함

    -   질문) 어떻게 현재 path에 맞는 content를 update할 수 있을까?
    -   👉 일단, Navigation Provider를 만들어 그 안에 `currentPath` state를 생성하자
    -   👉 Navigation Context에는 `currentPath` state이 담기게 됨

    ```
    NavigationProvider → App → SideBar
                             ↳ ButtonPage
                             ↳ AccordionPage
                             ↳ DropdownPage
    ```

-   `src` 폴더 안에 `context` 폴더를 만들고 `Navigation.js` 파일을 생성하자

    ```js
    // context/Navigation.js
    import { createContext } from "react";

    const NavigationContext = createContext();

    function NavigationProvider({ children }) {
    	return (
    		<NavigationContext.Provider value={{}}>
    			{children}
    		</NavigationContext.Provider>
    	);
    }

    export { NavigationProvider };
    export default NavigationContext;
    ```

-   index.js 파일에서 App component를 NavigationProvider로 감싸, 하위 component들이 NavigationContext에 접근할 수 있게 하자

    ```js
    // index.js
    import { NavigationProvider } from './context/navigation';

    ...
    root.render(
        <NavigationProvider>
            <App />
        </NavigationProvider>
    );
    ```

## ▶ 223. Listening to Forward and Back Clicks

> 자료: [009\_-_nav](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/13._Making_Navigation_Reusable/009_-_nav)

-   currentPath state를 생성하자

    -   currentPath state의 초기값은 최초 로드 시의 url path이어야 함

    ```js
    import { createContext, useState } from 'react';

    function NavigationProvider({ children }) {
        const [currentPath, setCurrentPath] = useState(window.location.pathname);
        ...
    }
    ```

-   Forward/Back buttons를 클릭한 경우, currentPath state를 update해주자

    ```js
    import { createContext, useState, useEffect } from 'react';

    function NavigationProvider({ children }) {
        ...
        useEffect(() => {
            const handler = () => {
                setCurrentPath(window.location.pathname);
            };
            window.addEventListener('popstate', handler);

            return () => {
                window.removeEventListener('popstate', handler);
            };
        }, []);
        ...
    }
    ```

## ▶ 224. Programmatic Navigation

> 자료: [010\_-_nav](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/13._Making_Navigation_Reusable/010_-_nav)

-   user가 직접 link를 클릭하거나 back/forward buttons을 클릭해서 이동하는 'User-triggered Navigation'도 있지만, 은행 웹사이트처럼 일정 시간동안 활동이 없으면 자동으로 로그인 페이지로 넘어가는 'Programmatic Navigation'도 있음
-   Programmatic Navigation: 프로그래밍된 code에 의해 자동으로 user를 다른 페이지로 이동(navigate)시킴
-   Navigate Provider에 Programmatic Navigation을 하는 `navigate` function을 추가시켜보자

    -   `pushState()`을 사용해 주소창을 update해야함
    -   `currentPath` state도 update해야함

    ```js
    function NavigationProvider({ children }) {
        ...
        const navigate = (to) => {
            window.history.pushState({}, '', to);
            setCurrentPath(to);
        };
        ...
    }
    ```

-   NavigationContext에 `currentPath` state와 `navigate` function을 공유해서 다른 components에서 사용 가능하게 하자

    ```js
    function NavigationProvider({ children }) {
        ...
        return (
            <NavigationContext.Provider value={{ currentPath, navigate }}>
                {children}
            </NavigationContext.Provider>
        );
    }
    ```
