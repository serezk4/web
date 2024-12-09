### **React: Компоненты, Локальное состояние, и Пропсы**

**React-компоненты**, **локальное состояние**, и **пропсы (props)** являются основными строительными блоками React-приложений. Эти концепции помогают разделять интерфейс на независимые, переиспользуемые части с чётким управлением данными.

---

### **1. Компоненты**

#### **1.1 Что такое компонент?**
Компоненты — это функции или классы, которые возвращают React-элементы. Они описывают, как часть интерфейса должна выглядеть и вести себя. Компоненты могут быть:
- **Функциональными** (предпочтительны в современных приложениях).
- **Классовыми** (использовались до появления React-хуков).

#### **1.2 Типы компонентов**

1. **Функциональные компоненты**
   - Это JavaScript-функции, которые принимают параметры (props) и возвращают элементы JSX.

   **Пример:**
   ```javascript
   function Greeting(props) {
       return <h1>Hello, {props.name}!</h1>;
   }
   ```

2. **Классовые компоненты**
   - Это классы, которые наследуют `React.Component` и содержат метод `render`.

   **Пример:**
   ```javascript
   class Greeting extends React.Component {
       render() {
           return <h1>Hello, {this.props.name}!</h1>;
       }
   }
   ```

#### **1.3 Особенности компонентов**
- Компоненты могут быть **контейнерными** (управляющими данными) и **презентационными** (отвечающими за внешний вид).
- Каждый компонент имеет свой жизненный цикл (для классовых компонентов) или использует хуки (`useEffect`, `useState`).

---

### **2. Пропсы (Props)**

#### **2.1 Что такое пропсы?**
Пропсы — это входные данные, передаваемые компонентам. Они неизменяемы внутри компонента (только для чтения).

#### **2.2 Как работают пропсы?**
- Родительский компонент передаёт данные дочернему компоненту через атрибуты.

**Пример:**
```javascript
function Greeting(props) {
    return <h1>Hello, {props.name}!</h1>;
}

function App() {
    return <Greeting name="Alice" />;
}
```

#### **2.3 Дефолтные значения пропсов**
Если пропс не передан, можно задать значение по умолчанию.

**Пример:**
```javascript
function Greeting({ name = "Guest" }) {
    return <h1>Hello, {name}!</h1>;
}
```

---

#### **2.4 Валидация пропсов**
React поддерживает проверку типов пропсов через библиотеку `prop-types`.

**Пример:**
```javascript
import PropTypes from 'prop-types';

function Greeting({ name }) {
    return <h1>Hello, {name}!</h1>;
}

Greeting.propTypes = {
    name: PropTypes.string.isRequired,
};
```

---

### **3. Локальное состояние (State)**

#### **3.1 Что такое состояние (state)?**
Состояние — это изменяемые данные, которые управляют поведением компонента. В отличие от пропсов, состояние:
- Хранится внутри компонента.
- Может изменяться через методы (`setState` или `useState`).

---

#### **3.2 Использование состояния в функциональных компонентах**

С помощью хука `useState` можно добавить локальное состояние.

**Пример:**
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

#### **3.3 Использование состояния в классовых компонентах**

В классовых компонентах состояние объявляется в конструкторе.

**Пример:**
```javascript
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0 };
    }

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

#### **3.4 Основные правила работы с состоянием**
1. **Не изменяйте состояние напрямую.**  
   Используйте `setState` (в классовых компонентах) или `setCount` (в функциональных).
   
   **Неверно:**
   ```javascript
   this.state.count = 10;
   ```
   **Верно:**
   ```javascript
   this.setState({ count: 10 });
   ```

2. **Состояние асинхронно.**  
   Если новое состояние зависит от предыдущего, передавайте функцию в `setState`.

   **Пример:**
   ```javascript
   this.setState((prevState) => ({ count: prevState.count + 1 }));
   ```

---

### **4. Сравнение Props и State**

| **Свойство** | **Props**               | **State**               |
|--------------|-------------------------|-------------------------|
| **Тип**      | Передаются извне        | Управляются компонентом |
| **Изменяемость** | Неизменяемые (readonly) | Изменяемые (mutable)    |
| **Цель**     | Передача данных         | Управление состоянием   |

---

### **5. Совместное использование Props и State**

Часто компоненты используют и `props`, и `state` одновременно. Пропсы управляют внешними данными, а состояние — внутренними.

**Пример:**
```javascript
function Counter({ step }) {
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + step)}>Increment by {step}</button>
        </div>
    );
}

function App() {
    return <Counter step={5} />;
}
```

---

### **6. Жизненный цикл состояния**

#### **Функциональные компоненты**
Состояние в функциональных компонентах управляется хуками:
- **`useState`** — для инициализации и управления состоянием.
- **`useEffect`** — для управления побочными эффектами (например, асинхронные запросы).

**Пример:**
```javascript
import React, { useState, useEffect } from 'react';

function Timer() {
    const [seconds, setSeconds] = useState(0);

    useEffect(() => {
        const interval = setInterval(() => setSeconds((s) => s + 1), 1000);
        return () => clearInterval(interval);
    }, []);

    return <p>Seconds: {seconds}</p>;
}
```

#### **Классовые компоненты**
Для управления состоянием используются методы жизненного цикла:
- **`componentDidMount`** — вызывается после монтирования.
- **`componentDidUpdate`** — вызывается после обновления.
- **`componentWillUnmount`** — вызывается перед размонтированием.

**Пример:**
```javascript
class Timer extends React.Component {
    constructor(props) {
        super(props);
        this.state = { seconds: 0 };
    }

    componentDidMount() {
        this.interval = setInterval(() => {
            this.setState((prevState) => ({ seconds: prevState.seconds + 1 }));
        }, 1000);
    }

    componentWillUnmount() {
        clearInterval(this.interval);
    }

    render() {
        return <p>Seconds: {this.state.seconds}</p>;
    }
}
```

---

### **Рекомендуемые ресурсы**

1. **React Props и State:**  
   [React Docs: State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)

2. **Хуки в React:**  
   [React Docs: Hooks](https://reactjs.org/docs/hooks-intro.html)

3. **Примеры функциональных компонентов:**  
   [React Functional Components](https://react.dev/learn)

---

### **Заключение**

Компоненты, состояние, и пропсы формируют основу React-приложений:
- **Компоненты**: Создают переиспользуемые части интерфейса.
- **Пропсы**: Передают данные в компоненты.
- **Состояние**: Управляет внутренними данными компонентов.

Эти концепции помогают строить мощные и масштабируемые приложения с чёткой логикой управления данными.
