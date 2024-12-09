### **Управление состоянием в React: Локальное vs Глобальное**

В React-приложениях управление состоянием играет ключевую роль в построении интерактивных пользовательских интерфейсов. Состояние можно разделить на **локальное** и **глобальное** в зависимости от области его действия и характера данных, которые оно описывает.

---

### **1. Локальное состояние (Local State)**

#### **Что это?**
Локальное состояние управляется индивидуальным компонентом и доступно только внутри него. Оно используется для управления данными, которые затрагивают только данный компонент и не влияют на другие части приложения.

#### **Примеры локального состояния:**
1. Открытие/закрытие модального окна.
2. Поля ввода формы.
3. Временные фильтры или сортировка в компоненте.

---

#### **Как управлять локальным состоянием?**

**1. Хук `useState` (в функциональных компонентах):**
```javascript
import React, { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
}
```

**2. Метод `this.setState` (в классовых компонентах):**
```javascript
class Counter extends React.Component {
    state = { count: 0 };

    increment = () => {
        this.setState({ count: this.state.count + 1 });
    };

    render() {
        return (
            <div>
                <p>Count: {this.state.count}</p>
                <button onClick={this.increment}>Increment</button>
            </div>
        );
    }
}
```

---

#### **Когда использовать локальное состояние?**
1. Когда данные нужны только внутри одного компонента.
2. Когда нет необходимости делиться данными с другими компонентами.
3. Когда данные просты и не требуют сложной логики.

---

### **2. Глобальное состояние (Global State)**

#### **Что это?**
Глобальное состояние управляет данными, которые затрагивают несколько компонентов или большую часть приложения. Эти данные доступны из любого места, где они необходимы.

#### **Примеры глобального состояния:**
1. Авторизация пользователя.
2. Темы приложения (тёмная/светлая).
3. Текущий пользователь или корзина покупок в интернет-магазине.

---

#### **Как управлять глобальным состоянием?**

**1. Context API (встроенный инструмент React):**
React предоставляет `Context API` для управления глобальным состоянием без необходимости использования сторонних библиотек.

**Пример:**
```javascript
import React, { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');

    return (
        <ThemeContext.Provider value={{ theme, setTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

function ThemedComponent() {
    const { theme, setTheme } = useContext(ThemeContext);

    return (
        <div style={{ background: theme === 'light' ? '#fff' : '#333', color: theme === 'light' ? '#000' : '#fff' }}>
            <p>Current Theme: {theme}</p>
            <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>Toggle Theme</button>
        </div>
    );
}

function App() {
    return (
        <ThemeProvider>
            <ThemedComponent />
        </ThemeProvider>
    );
}
```

**2. Redux/Redux Toolkit (сторонняя библиотека):**
Redux используется для управления сложным глобальным состоянием, включая асинхронные операции и сложные данные.

**Пример:**
```javascript
import { configureStore, createSlice } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Срез состояния
const counterSlice = createSlice({
    name: 'counter',
    initialState: { value: 0 },
    reducers: {
        increment: (state) => { state.value += 1; },
        decrement: (state) => { state.value -= 1; },
    },
});

const store = configureStore({ reducer: { counter: counterSlice.reducer } });
const { increment, decrement } = counterSlice.actions;

function Counter() {
    const count = useSelector((state) => state.counter.value);
    const dispatch = useDispatch();

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => dispatch(increment())}>Increment</button>
            <button onClick={() => dispatch(decrement())}>Decrement</button>
        </div>
    );
}

function App() {
    return (
        <Provider store={store}>
            <Counter />
        </Provider>
    );
}
```

---

### **3. Сравнение локального и глобального состояния**

| **Критерий**         | **Локальное состояние**                             | **Глобальное состояние**                             |
|-----------------------|----------------------------------------------------|----------------------------------------------------|
| **Область действия** | Только внутри одного компонента                    | Доступно для нескольких компонентов               |
| **Сложность данных** | Простые данные                                     | Комплексные данные                                 |
| **Средства**         | `useState`, `this.state`, `useReducer`             | Context API, Redux, Zustand, MobX                 |
| **Пример**           | Счётчик, состояние формы                           | Авторизация, тема, данные пользователя            |
| **Использование**    | Локальная логика компонента                        | Общие данные, которые нужно разделять между частями приложения |

---

### **4. Когда использовать локальное vs глобальное состояние**

#### **Используйте локальное состояние:**
1. Когда данные относятся только к одному компоненту.
2. Когда изменения данных не влияют на другие компоненты.
3. Когда вам нужно простое управление (например, переключатель состояния).

#### **Используйте глобальное состояние:**
1. Когда данные нужно разделить между несколькими компонентами.
2. Когда состояние приложения может быть сложным (например, управление пользователем, корзиной).
3. Когда требуется централизованное управление состоянием и логикой.

---

### **5. Инструменты для управления состоянием**

| **Инструмент**   | **Локальное состояние** | **Глобальное состояние** |
|------------------|--------------------------|--------------------------|
| **useState**     | Да                      | Нет                      |
| **useReducer**   | Да                      | Возможно                 |
| **Context API**  | Нет                     | Да                       |
| **Redux Toolkit**| Нет                     | Да                       |
| **MobX**         | Нет                     | Да                       |
| **Zustand**      | Возможно                | Да                       |

---

### **6. Пример совместного использования локального и глобального состояния**

Часто локальное и глобальное состояние используются вместе.

**Пример:**
```javascript
import React, { createContext, useContext, useState } from 'react';

const AuthContext = createContext();

function AuthProvider({ children }) {
    const [user, setUser] = useState(null);

    return (
        <AuthContext.Provider value={{ user, setUser }}>
            {children}
        </AuthContext.Provider>
    );
}

function LoginForm() {
    const { setUser } = useContext(AuthContext);
    const [username, setUsername] = useState('');

    const handleLogin = () => {
        setUser({ name: username });
    };

    return (
        <div>
            <input
                type="text"
                value={username}
                onChange={(e) => setUsername(e.target.value)}
                placeholder="Enter username"
            />
            <button onClick={handleLogin}>Login</button>
        </div>
    );
}

function UserProfile() {
    const { user } = useContext(AuthContext);

    return <p>Welcome, {user ? user.name : 'Guest'}!</p>;
}

function App() {
    return (
        <AuthProvider>
            <LoginForm />
            <UserProfile />
        </AuthProvider>
    );
}
```

---

### **Рекомендуемые ресурсы**

1. **Официальная документация React:**  
   [React State Management](https://reactjs.org/docs/state-and-lifecycle.html)

2. **Redux Toolkit:**  
   [Redux Toolkit Docs](https://redux-toolkit.js.org/)

3. **Context API:**  
   [React Context Documentation](https://reactjs.org/docs/context.html)

---

### **Заключение**

- **Локальное состояние** идеально для управления данными, которые принадлежат одному компоненту.
- **Глобальное состояние** используется для данных, которые затрагивают несколько компонентов.
- Выбор между локальным и глобальным состоянием зависит от сложности приложения и необходимости делиться данными между компонентами.
