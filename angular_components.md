### **Angular: Компоненты**

**Компоненты** — это основные строительные блоки Angular-приложений. Каждый компонент определяет часть пользовательского интерфейса (UI) и связан с соответствующим шаблоном, стилями и логикой.

---

### **1. Что такое компонент?**

**Компонент** — это класс в Angular, который управляет представлением, логикой и взаимодействием с пользователем. Он описывается с помощью декоратора `@Component`.

#### **Ключевые элементы компонента:**
1. **HTML-шаблон (Template):** Определяет структуру и представление интерфейса.
2. **Стили (Styles):** Описывают внешний вид компонента.
3. **Класс (Class):** Содержит логику и данные компонента.
4. **Метаданные:** Указываются через декоратор `@Component` для настройки компонента.

---

### **2. Создание компонента**

#### **2.1 С помощью Angular CLI**
Angular CLI автоматизирует создание компонентов.
```bash
ng generate component component-name
```

CLI создаёт:
- `component-name.component.ts` — код компонента.
- `component-name.component.html` — HTML-шаблон.
- `component-name.component.css/scss` — стили.
- `component-name.component.spec.ts` — файл для тестов.

---

#### **2.2 Вручную**

**Шаг 1:** Создайте файл компонента, например, `example.component.ts`.  
**Шаг 2:** Добавьте код компонента:
```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-example',
    templateUrl: './example.component.html',
    styleUrls: ['./example.component.css']
})
export class ExampleComponent {
    title = 'Hello, Angular!';
}
```

**Шаг 3:** Зарегистрируйте компонент в модуле:
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { ExampleComponent } from './example/example.component';

@NgModule({
    declarations: [ExampleComponent],
    imports: [BrowserModule],
    bootstrap: [ExampleComponent]
})
export class AppModule {}
```

---

### **3. Структура компонента**

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-root', // Селектор для подключения компонента в шаблонах
    templateUrl: './app.component.html', // Путь к HTML-шаблону
    styleUrls: ['./app.component.css'] // Путь к стилям
})
export class AppComponent {
    title = 'My Angular App'; // Данные, используемые в шаблоне
}
```

---

### **4. Свойства декоратора `@Component`**

1. **`selector`:**  
   Указывает, где компонент может быть использован в шаблонах.
   ```html
   <app-root></app-root>
   ```

2. **`template` и `templateUrl`:**  
   Определяют HTML-разметку компонента.
   ```typescript
   @Component({
       template: '<h1>Inline Template</h1>'
   })
   ```

3. **`styleUrls` и `styles`:**  
   Определяют стили для компонента.
   ```typescript
   @Component({
       styles: [`
           h1 {
               color: blue;
           }
       `]
   })
   ```

4. **`providers`:**  
   Регистрация сервисов на уровне компонента.

---

### **5. Взаимодействие с компонентами**

#### **5.1 Входные данные (`@Input`)**
Позволяет родительскому компоненту передавать данные дочернему.

**Пример:**
```typescript
import { Component, Input } from '@angular/core';

@Component({
    selector: 'app-child',
    template: `<p>Message: {{ message }}</p>`
})
export class ChildComponent {
    @Input() message!: string;
}
```

Использование в родительском компоненте:
```html
<app-child [message]="'Hello from parent!'"></app-child>
```

---

#### **5.2 Выходные данные (`@Output`)**
Используется для передачи событий из дочернего компонента в родительский.

**Пример:**
```typescript
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
    selector: 'app-child',
    template: `<button (click)="sendMessage()">Send Message</button>`
})
export class ChildComponent {
    @Output() messageEvent = new EventEmitter<string>();

    sendMessage() {
        this.messageEvent.emit('Hello from child!');
    }
}
```

Использование в родительском компоненте:
```html
<app-child (messageEvent)="receiveMessage($event)"></app-child>
```

---

### **6. Жизненный цикл компонентов**

Компоненты Angular имеют ряд хуков жизненного цикла, которые позволяют реагировать на изменения состояния и привязки данных.

| **Хук**             | **Описание**                                                              |
|---------------------|---------------------------------------------------------------------------|
| `ngOnInit`          | Вызывается при инициализации компонента. Используется для настройки.      |
| `ngOnChanges`       | Реагирует на изменения входных данных (`@Input`).                        |
| `ngDoCheck`         | Вызывается при каждой проверке изменений.                                |
| `ngAfterViewInit`   | Вызывается после инициализации представления компонента.                 |
| `ngOnDestroy`       | Вызывается перед уничтожением компонента. Используется для очистки.      |

**Пример использования хуков:**
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
    selector: 'app-example',
    template: `<p>Example Component</p>`
})
export class ExampleComponent implements OnInit, OnDestroy {
    ngOnInit() {
        console.log('Component initialized');
    }

    ngOnDestroy() {
        console.log('Component destroyed');
    }
}
```

---

### **7. Коммуникация между компонентами**

#### **7.1 Родитель — Дочерний**
- Используйте `@Input` и `@Output`.

#### **7.2 Сервисы**
- Используйте сервисы для обмена данными между компонентами, не связанными напрямую.

**Пример:**
```typescript
@Injectable({
    providedIn: 'root'
})
export class DataService {
    private data = new BehaviorSubject<string>('Default Message');
    currentData = this.data.asObservable();

    updateData(message: string) {
        this.data.next(message);
    }
}
```

Использование:
```typescript
// Component A
constructor(private dataService: DataService) {}
this.dataService.updateData('New Message');

// Component B
constructor(private dataService: DataService) {}
this.dataService.currentData.subscribe(message => console.log(message));
```

---

### **8. Работа с шаблонами**

#### **8.1 Привязка данных**
1. **Интерполяция:**
   ```html
   <p>{{ title }}</p>
   ```

2. **Привязка свойств:**
   ```html
   <img [src]="imageUrl" />
   ```

3. **Привязка событий:**
   ```html
   <button (click)="handleClick()">Click Me</button>
   ```

4. **Двусторонняя привязка:**
   ```html
   <input [(ngModel)]="username" />
   ```

---

### **9. Пример простого приложения**

**Компонент:**
```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-root',
    template: `
        <h1>{{ title }}</h1>
        <app-child [message]="parentMessage" (messageEvent)="receiveMessage($event)"></app-child>
        <p>Message from child: {{ childMessage }}</p>
    `
})
export class AppComponent {
    title = 'Angular Components';
    parentMessage = 'Hello Child!';
    childMessage = '';

    receiveMessage(event: string) {
        this.childMessage = event;
    }
}
```

**Дочерний компонент:**
```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
    selector: 'app-child',
    template: `
        <p>Message from parent: {{ message }}</p>
        <button (click)="sendMessage()">Send Message to Parent</button>
    `
})
export class ChildComponent {
    @Input() message!: string;
    @Output() messageEvent = new EventEmitter<string>();

    sendMessage() {
        this.messageEvent.emit('Hello Parent!');
    }
}
```

---

### **Рекомендуемые ресурсы**

1. **Официальная документация Angular:**  
   [Angular Components Guide](https://angular.io/guide/component-overview)

2. **Учебник по Angular:**  
   [Angular Tutorial](https://angular.io/start)

---

### **Заключение**

Компоненты в Angular — это основа архитектуры приложения. Они обеспечивают:
- Разделение интерфейса на независимые части.
- Удобное взаимодействие между элементами.
- Простоту масштабирования и тестирования.

Благодаря мощному инструментарию Angular, компоненты позволяют создавать гибкие и масштабируемые приложения.
