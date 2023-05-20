# ✔ '14. Creating Portals with ReactDOM' 이론 정리

## ▶ 233. Modal Component Overview

-   modal을 만들어보자

    -   'Open Modal' 버튼을 누르면 모달창이 뜨고, 모달창에서 'I Accept' 버튼을 누르거나 뒷 배경을 누르면 모달창이 사라지도록 구현하자

-   먼저, `Modal` component를 만들자

    ```js
    // components/Modal.js
    function Modal() {
    	return <div>modal!</div>;
    }

    export default Modal;
    ```

-   `ModalPage` component를 만들자

    ```js
    // pages/ModalPage.js
    import Modal from "../components/Modal";

    function ModalPage() {
    	return (
    		<div>
    			<Modal />
    		</div>
    	);
    }

    export default ModalPage;
    ```

-   user가 '/modal' path로 이동했을 때 ModalPage가 render되도록 App.js에서 route를 추가하자

    ```js
    import ModalPage from "./pages/ModalPage";

    function App() {
    	return (
    		<div className="container mx-auto grid grid-cols-6 gap-4 mt-4">
    			<Sidebar />
    			<div className="col-span-5">
    				...
    				<Route path="/modal">
    					<ModalPage />
    				</Route>
    			</div>
    		</div>
    	);
    }
    ```

-   Sidebar component에서 '/modal' path로 이동하는 link를 추가하자

    ```js
    function Sidebar() {
      const links = [
        ...
        { label: 'Modal', path: '/modal' },
      ];
      ...
    }
    ```

## ▶ 234. Toggling Visibility

> 자료: [002\_-_modal](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/14._Creating_Portals_with_ReactDOM/002_-_modal)

-   'Open Modal' 버튼을 누르면 모달창이 뜨도록 구현해보자
-   button과 `showModal` state는 Modal과 Parent component 중 어디에 두는 것이 좋을까?

    -   가정1) 만약 Modal에 둔다면, modal은 항상 버튼을 클릭했을 때만 나타남
    -   가정2) 만약 Parent에 둔다면, 버튼을 클릭했을 때뿐만 아니라 특정 상황에서도 modal을 띄우게 할 수 있음
    -   결론) 따라서, button과 `showModal` state는 Parent(현재 상황에선 ModalPage) component에 두는 것이 현명해 보임

    ```js
    import { useState } from "react";
    import Button from "../components/Button";

    function ModalPage() {
    	const [showModal, setShowModal] = useState(false);

    	const handleClick = () => {
    		setShowModal(true);
    	};

    	return (
    		<div>
    			<Button onClick={handleClick} primary>
    				Open Modal
    			</Button>
    			{showModal && <Modal />}
    		</div>
    	);
    }
    ```

-   모달창에서 'I Accept' 버튼을 누르거나 뒷 배경을 누르면 모달창이 사라지도록 구현하기 위해서, Modal component에 `showModal` state를 control할 수 있는 handler를 prop으로 전달해줘야 함

## ▶ 235. At First Glance, Easy!

> 자료: [003\_-_modal](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/14._Creating_Portals_with_ReactDOM/003_-_modal)

-   정확하게 modal을 구현하는 것은 생각보다 어려운 일임

    -   1. modal의 background가 전체 화면을 덮어야 함
    -   2. modal은 존재하는 모든 content를 덮어야 함

-   아래 코드는 modal과 유사하게 구현하긴 했으나, 실제 프로젝트에서는 사용되지 않는 modal 구현 방법임

    ```js
    function Modal() {
    	return (
    		<div>
    			<div className="absolute inset-0 bg-gray-300 opacity-80"></div>
    			<div className="absolute inset-40 p-10 bg-white">
    				I'm a modal!
    			</div>
    		</div>
    	);
    }
    ```

## ▶ 236. We're Lucky it Works At All!

-   위 modal 코드는 나중에 break되기 쉬운 부정확한 코드임
    -   non-static 'positioned parent'가 없기 때문에 잘 작동하는 것처럼 보이는 것일 뿐!
    -   만약 나중에 코드 수정을 통해 non-static 'positioned parent'를 가지게 된다면, modal background는 전체 화면이 아닌 positioned parent만을 덮게 될 것임

## ▶ 237. Fixing the Modal with Portals

-   위 modal 코드를 수정해보자
-   `ReactDOM.createPortal()` function을 이용해 modal html 부분을 현재 위치가 아닌, body 바로 안쪽으로 빼내도록 하자

    -   아래와 같이 body 바로 안쪽에 '.modal-container' div 태그를 두고 이 안에 modal html을 위치시키면, modal html은 절대 non-static 'positioned parent'를 가질 수 없음
    -   따라서, modal html은 HTML 문서를 기준으로 위치하게 되기 때문에 항상 전체 화면을 덮을 수 있음

-   `ReactDOM.createPortal(child, container)`

    -   parent component 바깥의 DOM 요소에 child component를 렌더링해 줌
    -   `child`: element, string, fragment 등 container에 렌더링할 요소
    -   `container`: child를 렌더링할 DOM element

    ```html
    <!-- html -->
    <body>
    	<noscript>You need to enable JavaScript to run this app.</noscript>
    	<div id="root"></div>
    	<div class="modal-container"></div>
    </body>
    ```

    ```js
    import ReactDOM from "react-dom";

    function Modal() {
    	return ReactDOM.createPortal(
    		<div>
    			<div className="absolute inset-0 bg-gray-300 opacity-80"></div>
    			<div className="absolute inset-40 p-10 bg-white">
    				I'm a modal!
    			</div>
    		</div>,
    		document.querySelector(".modal-container")
    	);
    }
    ```

## ▶ 238. Closing the Modal

-   modal 뒤의 gray background를 클릭하면 modal이 사라지게 해보자

    ```js
    function ModalPage() {
        ...
        const handleClose = () => {
            setShowModal(false);
        };

        return (
            <div>
                <Button onClick={handleClick} primary>
                    Open Modal
                </Button>
                {showModal && <Modal onClose={handleClose} />}
            </div>
        );
    }
    ```

    ```js
    function Modal({ onClose }) {
    	return ReactDOM.createPortal(
    		<div>
    			<div
    				onClick={onClose}
    				className="absolute inset-0 bg-gray-300 opacity-80"
    			></div>
    			<div className="absolute inset-40 p-10 bg-white"></div>
    		</div>,
    		document.querySelector(".modal-container")
    	);
    }
    ```

## ▶ 239. Customizing the Modal

> 자료: [007\_-_modal](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/14._Creating_Portals_with_ReactDOM/007_-_modal)

-   modal 창 안에는 text 부분이랑 control buttons 부분을 넣을 수 있는데, 직접 Modal component 안에서 하드코딩하는 것보다 parent로부터 각 부분을 props로 받아 넣어주는 것이 나중에 재사용하기 좋은 component임

    -   따라서, ModalPage component로부터 `children`, `actionBar` props를 받아 넣어보자

    ```js
    function ModalPage() {
        ...
        const actionBar = (
            <div>
                <Button onClick={handleClose} primary>
                    I Accept
                </Button>
            </div>
        );

        const modal = (
            <Modal onClose={handleClose} actionBar={actionBar}>
                <p>Here is an important agreement for you to accept</p>
            </Modal>
        );

        return (
            <div>
                <Button onClick={handleClick} primary>
                    Open Modal
                </Button>
                {showModal && modal}
            </div>
        );
    }
    ```

    ```js
    function Modal({ onClose, children, actionBar }) {
    	return ReactDOM.createPortal(
    		<div>
    			...
    			<div className="absolute inset-40 p-10 bg-white">
    				{children}
    				{actionBar}
    			</div>
    		</div>,
    		document.querySelector(".modal-container")
    	);
    }
    ```

## ▶ 240. Additional Styling

> 자료: [008\_-_modal](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/14._Creating_Portals_with_ReactDOM/008_-_modal)

-   Modal component를 스타일링해보자

    ```js
    function Modal({ onClose, children, actionBar }) {
    	return ReactDOM.createPortal(
    		<div>
    			...
    			<div className="absolute inset-40 p-10 bg-white">
    				<div className="flex flex-col justify-between h-full">
    					{children}
    					<div className="flex justify-end">{actionBar}</div>
    				</div>
    			</div>
    		</div>,
    		document.querySelector(".modal-container")
    	);
    }
    ```

## ▶ 241. One Small Bug

> 자료: [009\_-_modal](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/14._Creating_Portals_with_ReactDOM/009_-_modal)

-   페이지 길이가 화면 높이보다 길 때 스크롤을 사용하여 내릴 수 있는데, 이때 모달창이 떠있는 상태에서도 스크롤을 내릴 수 있어 모달창이랑 gray background가 위로 올라가버리는 문제가 발생함
    -   따라서, 모달창이 떠 있는 상태에서는 스크롤할 수 없게 처리해주는 것이 필요

## ▶ 242. Modal Wrapup

> 자료: [010\_-_modal](https://github.com/hyejinny97/Modern-React-with-Redux/tree/master/14._Creating_Portals_with_ReactDOM/010_-_modal)

-   모달창이 떠 있는 상태에서 body element에 `overflow: hidden`를 주면 스크롤할 수 없게 됨
-   모달창이 사라질 때 다시 body element에 `overflow: hidden`를 없애면 스크롤 가능하게 할 수 있음

-   `useEffect` hook을 사용해 Modal component가 처음 렌더링될 때 body element에 `overflow: hidden`를 넣어주고, Modal component가 삭제될 때 `overflow: hidden`를 없애보자

    ```js
    import { useEffect } from 'react';

    function Modal({ onClose, children, actionBar }) {
        useEffect(() => {
            document.body.classList.add('overflow-hidden');

            return () => {
            document.body.classList.remove('overflow-hidden');
            };
        }, []);
        ...
    }
    ```
