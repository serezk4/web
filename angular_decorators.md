### **Angular: Декораторы**

**Декораторы** в Angular — это функции, которые добавляют метаданные к классам, методам, свойствам или параметрам. Они являются основной частью механизма Angular и используются для настройки компонентов, директив, сервисов, модулей и других элементов приложения.

---

### **1. Что такое декораторы?**

- **Декоратор** — это функция, которая принимает объект метаданных и модифицирует поведение целевого элемента (например, класса или метода).
- Angular использует декораторы для определения типов сущностей (компоненты, сервисы, модули) и управления их поведением.

---

### **2. Типы декораторов в Angular**

#### **2.1 Классовые декораторы**
Используются для предоставления метаданных классам. Например:
- `@Component` для компонентов.
- `@Directive` для директив.
- `@NgModule` для модулей.
- `@Injectable` для сервисов.

#### **2.2 Декораторы свойств**
Используются для связывания данных между компонентами или DOM. Например:
- `@Input`
- `@Output`

#### **2.3 Декораторы методов**
Используются для настройки методов, например, обработки событий Angular через жизненные циклы.

#### **2.4 Декораторы параметров**
Используются для инъекции зависимостей. Например:
- `@Inject`

---

### **3. Основные декораторы в Angular**

#### **3.1 `@Component`**
- Определяет компонент Angular.
- Используется для привязки шаблона, стилей и логики к определённому классу.

**Пример:**
```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.css']
})
export class AppComponent {
    title = 'My Angular App';
}
```

**Ключевые свойства:**
- `selector` — CSS-селектор, который идентифицирует компонент в шаблоне.
- `templateUrl`/`template` — путь к HTML-шаблону или сам шаблон.
- `styleUrls`/`styles` — пути к стилям или сами стили.

---

#### **3.2 `@NgModule`**
- Определяет модуль приложения.
- Используется для группировки компонентов, директив и сервисов.

**Пример:**
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';

@NgModule({
    declarations: [AppComponent], // Компоненты и директивы
    imports: [BrowserModule],     // Другие модули
    providers: [],                // Сервисы
    bootstrap: [AppComponent]     // Главный компонент
})
export class AppModule {}
```

---

#### **3.3 `@Injectable`**
- Указывает, что класс может быть инжектирован как зависимость.
- Используется для сервисов.

**Пример:**
```typescript
import { Injectable } from '@angular/core';

@Injectable({
    providedIn: 'root' // Автоматическая регистрация в корневом модуле
})
export class DataService {
    getData() {
        return ['Item 1', 'Item 2', 'Item 3'];
    }
}
```

---

#### **3.4 `@Directive`**
- Определяет директиву, которая изменяет поведение элементов DOM.

**Пример:**
```typescript
import { Directive, ElementRef, Renderer2 } from '@angular/core';

@Directive({
    selector: '[appHighlight]'
})
export class HighlightDirective {
    constructor(el: ElementRef, renderer: Renderer2) {
        renderer.setStyle(el.nativeElement, 'backgroundColor', 'yellow');
    }
}
```

---

#### **3.5 `@Input`**
- Позволяет передавать данные от родительского компонента к дочернему.

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

Использование:
```html
<app-child [message]="'Hello from parent!'"></app-child>
```

---

#### **3.6 `@Output`**
- Используется для передачи событий из дочернего компонента в родительский.

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

Использование:
```html
<app-child (messageEvent)="receiveMessage($event)"></app-child>
```

---

#### **3.7 `@HostListener`**
- Позволяет слушать события на хост-элементе компонента или директивы.

**Пример:**
```typescript
import { Directive, HostListener } from '@angular/core';

@Directive({
    selector: '[appClickTracker]'
})
export class ClickTrackerDirective {
    @HostListener('click', ['$event'])
    onClick(event: MouseEvent) {
        console.log('Element clicked:', event.target);
    }
}
```

---

#### **3.8 `@HostBinding`**
- Позволяет привязывать свойства хост-элемента к свойствам класса.

**Пример:**
```typescript
import { Directive, HostBinding } from '@angular/core';

@Directive({
    selector: '[appBackground]'
})
export class BackgroundDirective {
    @HostBinding('style.backgroundColor') bgColor = 'lightblue';
}
```

---

#### **3.9 `@Inject`**
- Позволяет вручную указать токен зависимости, которую нужно инжектировать.

**Пример:**
```typescript
import { Injectable, Inject } from '@angular/core';

@Injectable()
export class MyService {
    constructor(@Inject('API_URL') private apiUrl: string) {
        console.log('API URL:', apiUrl);
    }
}
```

Регистрация:
```typescript
providers: [{ provide: 'API_URL', useValue: 'https://api.example.com' }]
```

---

### **4. Роль декораторов в Angular**

1. **Определение сущностей:**  
   Декораторы сообщают Angular, как обрабатывать классы, методы и свойства.

2. **Привязка данных:**  
   Декораторы, такие как `@Input` и `@Output`, упрощают связь между компонентами.

3. **Инъекция зависимостей:**  
   Декораторы, такие как `@Injectable` и `@Inject`, позволяют внедрять зависимости.

4. **Управление событиями и поведением:**  
   Декораторы, такие как `@HostListener` и `@HostBinding`, позволяют контролировать поведение DOM.

---

### **Рекомендуемые ресурсы**

1. **Официальная документация Angular по декораторам:**  
   [Angular Decorators Documentation](https://angular.io/guide/architecture-services#decorators)

2. **Руководство по работе с компонентами:**  
   [Angular Components Guide](https://angular.io/guide/component-overview)

3. **TypeScript Decorators:**  
   [TypeScript Decorators](https://www.typescriptlang.org/docs/handbook/decorators.html)

---

### **Заключение**

Декораторы — это мощный инструмент в Angular, который делает разработку интуитивной и гибкой. Они позволяют:
- Определять ключевые элементы приложения (компоненты, директивы, модули).
- Настраивать связи между частями приложения (через инъекцию зависимостей).
- Управлять поведением и данными.

Использование декораторов является одной из ключевых особенностей Angular, упрощающей разработку масштабируемых приложений.
