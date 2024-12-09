### **React: Redux Toolkit**

**Redux Toolkit (RTK)** — это официальная библиотека для управления состоянием в React-приложениях. Она упрощает работу с Redux за счёт минимизации шаблонного кода и добавления удобных утилит для создания стора, редьюсеров и асинхронных операций.

---

### **1. Почему Redux Toolkit?**

**Проблемы классического Redux:**
- Много шаблонного кода (action creators, типы действий).
- Сложная настройка и организация кода.
- Нет встроенной поддержки асинхронных операций.

**Решения, предлагаемые RTK:**
- Удобные утилиты для создания стора и редьюсеров.
- Встроенная поддержка асинхронных операций через `createAsyncThunk`.
- Интеграция с Immer.js для работы с неизменяемыми состояниями.

---

### **2. Основные функции Redux Toolkit**

#### **2.1 configureStore**
- Создаёт стор с предустановленными настройками.
- Автоматически добавляет middleware (например, для DevTools).

**Пример:**
```javascript
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
    reducer: {
        counter: counterReducer,
    },
});

export default store;
```

---

#### **2.2 createSlice**
- Упрощает создание редьюсеров и экшенов.
- Объединяет логику редьюсеров и экшенов в одном месте.

**Пример:**
```javascript
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
    name: 'counter',
    initialState: { value: 0 },
    reducers: {
        increment: (state) => {
            state.value += 1; // Immer.js позволяет безопасно изменять state
        },
        decrement: (state) => {
            state.value -= 1;
        },
        incrementByAmount: (state, action) => {
            state.value += action.payload;
        },
    },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

export default counterSlice.reducer;
```

---

#### **2.3 createAsyncThunk**
- Упрощает обработку асинхронных операций (например, запросы к API).
- Создаёт экшен, который автоматически обрабатывает состояния `pending`, `fulfilled`, и `rejected`.

**Пример:**
```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

export const fetchPosts = createAsyncThunk('posts/fetchPosts', async () => {
    const response = await axios.get('/api/posts');
    return response.data;
});
```

---

#### **2.4 createEntityAdapter**
- Упрощает управление сущностями (например, списки объектов).
- Предоставляет методы для добавления, удаления и обновления данных.

**Пример:**
```javascript
import { createEntityAdapter, createSlice } from '@reduxjs/toolkit';

const postsAdapter = createEntityAdapter();

const postsSlice = createSlice({
    name: 'posts',
    initialState: postsAdapter.getInitialState(),
    reducers: {
        addPost: postsAdapter.addOne,
        removePost: postsAdapter.removeOne,
        updatePost: postsAdapter.updateOne,
    },
});

export const { addPost, removePost, updatePost } = postsSlice.actions;

export default postsSlice.reducer;
```

---

### **3. Настройка Redux Toolkit**

#### **Шаг 1: Установка библиотеки**
```bash
npm install @reduxjs/toolkit react-redux
```

#### **Шаг 2: Создание стора**
```javascript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

const store = configureStore({
    reducer: {
        counter: counterReducer,
    },
});

export default store;
```

#### **Шаг 3: Подключение провайдера**
React-компоненты получают доступ к стору через `Provider`.

**Пример:**
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
);
```

---

### **4. Использование Redux Toolkit в компонентах**

#### **4.1 Чтение состояния с помощью `useSelector`**
Хук `useSelector` используется для извлечения данных из стора.

**Пример:**
```javascript
import { useSelector } from 'react-redux';

function CounterDisplay() {
    const count = useSelector((state) => state.counter.value);
    return <p>Count: {count}</p>;
}
```

#### **4.2 Отправка действий с помощью `useDispatch`**
Хук `useDispatch` используется для отправки экшенов.

**Пример:**
```javascript
import { useDispatch } from 'react-redux';
import { increment, decrement } from './counterSlice';

function CounterControls() {
    const dispatch = useDispatch();

    return (
        <div>
            <button onClick={() => dispatch(increment())}>Increment</button>
            <button onClick={() => dispatch(decrement())}>Decrement</button>
        </div>
    );
}
```

---

### **5. Пример полного приложения**

#### **Срез состояния (Slice):**
```javascript
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
    name: 'counter',
    initialState: { value: 0 },
    reducers: {
        increment: (state) => {
            state.value += 1;
        },
        decrement: (state) => {
            state.value -= 1;
        },
        reset: (state) => {
            state.value = 0;
        },
    },
});

export const { increment, decrement, reset } = counterSlice.actions;

export default counterSlice.reducer;
```

#### **Конфигурация стора:**
```javascript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

const store = configureStore({
    reducer: {
        counter: counterReducer,
    },
});

export default store;
```

#### **Компоненты:**

1. **CounterDisplay:**
   ```javascript
   import { useSelector } from 'react-redux';

   function CounterDisplay() {
       const count = useSelector((state) => state.counter.value);
       return <h1>Count: {count}</h1>;
   }

   export default CounterDisplay;
   ```

2. **CounterControls:**
   ```javascript
   import { useDispatch } from 'react-redux';
   import { increment, decrement, reset } from './counterSlice';

   function CounterControls() {
       const dispatch = useDispatch();

       return (
           <div>
               <button onClick={() => dispatch(increment())}>Increment</button>
               <button onClick={() => dispatch(decrement())}>Decrement</button>
               <button onClick={() => dispatch(reset())}>Reset</button>
           </div>
       );
   }

   export default CounterControls;
   ```

3. **App:**
   ```javascript
   import React from 'react';
   import CounterDisplay from './CounterDisplay';
   import CounterControls from './CounterControls';

   function App() {
       return (
           <div>
               <CounterDisplay />
               <CounterControls />
           </div>
       );
   }

   export default App;
   ```

---

### **6. Преимущества Redux Toolkit**

1. **Меньше шаблонного кода:**  
   Интеграция редьюсеров, экшенов и асинхронных операций в одну структуру.

2. **Поддержка асинхронности:**  
   `createAsyncThunk` упрощает работу с API.

3. **Интеграция с Immer.js:**  
   Позволяет безопасно изменять состояние через мутации.

4. **Удобная настройка:**  
   `configureStore` автоматически добавляет полезные middleware.

---

### **Рекомендуемые ресурсы**

1. **Официальная документация Redux Toolkit:**  
   [Redux Toolkit Docs](https://redux-toolkit.js.org/)

2. **React и Redux Toolkit:**  
   [React Redux Docs](https://react-redux.js.org/)

3. **Примеры приложений с RTK:**  
   [RTK Examples](https://redux-toolkit.js.org/introduction/examples)

---

### **Заключение**

Redux Toolkit делает управление состоянием в React-приложениях более простым, удобным и эффективным. Благодаря утилитам, таким как `createSlice` и `createAsyncThunk`, разработчики могут сократить время настройки и сосредоточиться на написании бизнес-логики. RTK рекомендуется как стандарт для новых проектов на Redux.
