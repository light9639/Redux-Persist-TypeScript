# 💾 Redux-Persist를 이용하여 만든 Counter 예제 파일.
:octocat: 바로 가기 : https://light9639.github.io/Redux-Persist-TypeScript/

![light9639 github io_Redux-Persist-TypeScript_](https://user-images.githubusercontent.com/95972251/230108530-890a8168-553a-444e-b2c6-1ba27d9ae27f.png)

:sparkles: 💾 Redux-Persist를 이용하여 만든 Counter 예제 파일. :sparkles:
## :tada: React 프로젝트 생성
- React 생성
```bash
npm create-react-app my-app
# or
yarn create react-app my-app
```

- vite를 이용하여 프로젝트를 생성하려면
```bash
npm create vite@latest
# or
yarn create vite
```
- 터미널에서 실행 후 프로젝트 이름 만든 후 React 선택, Typescirpt-SWC 선택하면 생성 완료.
## 🏍️ redux, redux-persist, @reduxjs/toolkit 라이브러리 설치
- `redux`, `redux-persist`, `@reduxjs/toolkit` 라이브러리 설치하기
```bash
$ npm install react-redux redux-persist @reduxjs/toolkit
# or
$ yarn add react-redux redux-persist @reduxjs/toolkit
```
## ✒️ App.tsx, main.tsx 수정 및 작성
### ⚡ App.tsx
- `Counter.tsx`를 `import`한 뒤 스타일링하여 작성한다.
```typescript
import React from 'react';
import logo from '../public/logo512.png';
import Counter from './counter/Counter';
import './App.css';

export default function App(): JSX.Element {
  return (
    <div className="App">
      <div className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <Counter />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <span>
          <span>Learn </span>
          <a
            className="App-link"
            href="https://reactjs.org/"
            target="_blank"
            rel="noopener noreferrer"
          >
            React
          </a>
          <span>, </span>
          <a
            className="App-link"
            href="https://redux.js.org/"
            target="_blank"
            rel="noopener noreferrer"
          >
            Redux
          </a>
          <span>, </span>
          <a
            className="App-link"
            href="https://redux-toolkit.js.org/"
            target="_blank"
            rel="noopener noreferrer"
          >
            Redux Toolkit
          </a>
          ,<span> and </span>
          <a
            className="App-link"
            href="https://react-redux.js.org/"
            target="_blank"
            rel="noopener noreferrer"
          >
            React Redux
          </a>
        </span>
      </div>
    </div>
  );
}
```
### ⚡ main.tsx
- 리덕스에서 `Provider`를 `import`한 뒤 밑에 나올 `store.ts`에서 `store`를 가져온다. 이후 `<Provider store={store}></Provider>`로 `<App />`를 감싸면 리덕스를 사용할 수 있다.
- 마찬가지로 `store`를 가져온 후 `let persistor = persistStore(store);`로 감싼다.
- 이후 `<PersistGate loading={null} persistor={persistor}></PersistGate>`을 사용하면 변수명이 로컬스토리지에 저장된다.
```typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'
import store from './app/store';
import { Provider } from 'react-redux';
import { PersistGate } from 'redux-persist/integration/react';
import { persistStore } from 'redux-persist';

let persistor = persistStore(store);

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <React.StrictMode>
    <Provider store={store}>
      <PersistGate loading={null} persistor={persistor}>
        <App />
      </PersistGate>
    </Provider>
  </React.StrictMode>,
)
```
## ✒️ store.ts, Counter.tsx, Counter.module.css, counterSlice.ts 수정 및 작성
### ⚡ store.ts
- 리덕스 툴킷을 사용하다가 새로고침 시 리덕스의 `state`가 사라지는 것을 방지하기 위해 로컬스토리지에 자동으로 저장하는 `Redux-Persist` 라이브러리를 사용한다.
- 밑의 내용과 같이 작성하고 `main.tsx`에  `import`하여 사용하면 로컬스토리지에 자동으로 저장되어, 새로고침을 해도 `state` 값이 유지된다.
```typescript
import {configureStore} from '@reduxjs/toolkit';
import storage from 'redux-persist/lib/storage';
import {combineReducers} from "redux";
import { persistReducer } from 'redux-persist';

import counterReducer from '../counter/counterSlice';

const reducers = combineReducers({
    counter: counterReducer
});

const persistConfig = {
    key: 'root',
    storage
};

const persistedReducer = persistReducer(persistConfig, reducers);

const store = configureStore({
    reducer: persistedReducer,
});

export default store;
```
### ⚡ Counter.tsx
- `counterSlice.ts`에서 사용할 리덕스 툴킷 함수들을 `import`하여 사용한다.
```typescript
import React, { useState } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import {
    decrement,
    increment,
    incrementByAmount,
    incrementAsync,
    selectCount,
} from './counterSlice';
import styles from './Counter.module.css';

export default function Counter(): JSX.Element {
    const count = useSelector(selectCount);
    const dispatch: any = useDispatch();
    const [incrementAmount, setIncrementAmount] = useState('2');

    return (
        <div>
            <div className={styles.row}>
                <button
                    className={styles.button}
                    aria-label="Increment value"
                    onClick={() => dispatch(increment())}
                >
                    +
                </button>
                <span className={styles.value}>{count}</span>
                <button
                    className={styles.button}
                    aria-label="Decrement value"
                    onClick={() => dispatch(decrement())}
                >
                    -
                </button>
            </div>
            <div className={styles.row}>
                <input
                    className={styles.textbox}
                    aria-label="Set increment amount"
                    value={incrementAmount}
                    onChange={e => setIncrementAmount(e.target.value)}
                />
                <button
                    className={styles.button}
                    onClick={() =>
                        dispatch(incrementByAmount(Number(incrementAmount) || 0))
                    }
                >
                    Add Amount
                </button>
                <button
                    className={styles.asyncButton}
                    onClick={() =>
                        dispatch(incrementAsync(Number(incrementAmount) || 0))
                    }
                >
                    Add Async
                </button>
            </div>
        </div>
    );
}
```
### ⚡ Counter.module.css
- `Counter.tsx`의 스타일을 작성한다.
```css
.row {
    display: flex;
    align-items: center;
    justify-content: center;
}

.row:not(:last-child) {
    margin-bottom: 16px;
}

.value {
    font-size: 78px;
    padding-left: 16px;
    padding-right: 16px;
    margin-top: 2px;
    font-family: 'Courier New', Courier, monospace;
}

.button {
    appearance: none;
    background: none;
    font-size: 32px;
    padding-left: 12px;
    padding-right: 12px;
    outline: none;
    border: 2px solid transparent;
    color: rgb(112, 76, 182);
    padding-bottom: 4px;
    padding-top: 0px;
    cursor: pointer;
    background-color: rgba(112, 76, 182, 0.1);
    border-radius: 2px;
    transition: all 0.15s;
}

.textbox {
    font-size: 32px;
    padding: 2px;
    width: 64px;
    text-align: center;
    margin-right: 8px;
}

.button:hover,
.button:focus {
    border: 2px solid rgba(112, 76, 182, 0.4);
}

.button:active {
    background-color: rgba(112, 76, 182, 0.2);
}

.asyncButton {
    composes: button;
    position: relative;
    margin-left: 8px;
}

.asyncButton:after {
    content: "";
    background-color: rgba(112, 76, 182, 0.15);
    display: block;
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    opacity: 0;
    transition: width 1s linear, opacity 0.5s ease 1s;
}

.asyncButton:active:after {
    width: 0%;
    opacity: 1;
    transition: 0s
}
```
### ⚡ counterSlice.ts
- 리덕스 툴킷을 사용하여 `counter`예제 함수 작성.
```typescript
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import store from "../app/store";

interface CounterState {
    value: number;
}

const initialState: CounterState = { value: 0 };

export const counterSlice = createSlice({
    name: 'counter',
    initialState,
    reducers: {
        increment: state => {
            state.value += 1;
        },
        decrement: state => {
            state.value -= 1;
        },
        incrementByAmount: (state, action: PayloadAction<number>) => {
            state.value += action.payload;
        },
    },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;
export type RootState = ReturnType<typeof store.getState>
export type AppDispatch = typeof store.dispatch;

export const incrementAsync = (amount: number) => (dispatch: AppDispatch) => {
    setTimeout(() => {
        dispatch(incrementByAmount(amount));
    }, 1000);
};

export const selectCount = (state: RootState) => state.counter.value;

export default counterSlice.reducer;
```
