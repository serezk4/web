### **Angular: Директивы**

Директивы в Angular — это специальные инструкции, которые добавляют поведение к элементам DOM. Они позволяют изменять структуру, внешний вид или функциональность элементов в шаблонах.

---

### **1. Виды директив**

Angular предоставляет три типа директив:

#### **1.1 Атрибутивные директивы (Attribute Directives)**
- Изменяют внешний вид или поведение существующих элементов.
- Применяются к элементу как атрибут.

**Примеры встроенных директив:**
1. `ngClass` — динамически добавляет/удаляет классы.
2. `ngStyle` — динамически применяет стили.

**Пример:**
```html
<p [ngClass]="{ 'highlight': isHighlighted }">This is a paragraph.</p>
<p [ngStyle]="{ color: isHighlighted ? 'blue' : 'black' }">Styled dynamically</p>
```

---

#### **1.2 Структурные директивы (Structural Directives)**
- Изменяют структуру DOM, добавляя или удаляя элементы.
- Начинаются с `*` в шаблоне.

**Примеры встроенных директив:**
1. `*ngIf` — условно отображает элемент.
2. `*ngFor` — создаёт элементы на основе коллекции.
3. `*ngSwitch` — отображает элементы в зависимости от условия.

**Пример:**
```html
<p *ngIf="isVisible">This paragraph is visible.</p>

<ul>
    <li *ngFor="let item of items">{{ item }}</li>
</ul>
```

---

#### **1.3 Собственные директивы (Custom Directives)**
- Пользовательские директивы создаются для добавления специфического поведения.
- Могут быть как атрибутивными, так и структурными.

---

### **2. Работа с встроенными директивами**

#### **2.1 Атрибутивные директивы**

1. **`ngClass`**
   - Позволяет добавлять классы к элементу динамически.
   ```html
   <p [ngClass]="{ 'active': isActive, 'disabled': !isActive }">Dynamic Classes</p>
   ```

2. **`ngStyle`**
   - Позволяет динамически изменять стили элемента.
   ```html
   <div [ngStyle]="{ backgroundColor: isActive ? 'green' : 'red' }">Styled Div</div>
   ```

---

#### **2.2 Структурные директивы**

1. **`*ngIf`**
   - Отображает элемент только если условие истинно.
   ```html
   <p *ngIf="isLoggedIn">Welcome back!</p>
   ```

2. **`*ngFor`**
   - Итерация по массиву и отображение списка элементов.
   ```html
   <ul>
       <li *ngFor="let user of users">{{ user.name }}</li>
   </ul>
   ```

3. **`*ngSwitch`**
   - Отображает один из нескольких блоков в зависимости от значения.
   ```html
   <div [ngSwitch]="role">
       <p *ngSwitchCase="'admin'">Admin View</p>
       <p *ngSwitchCase="'user'">User View</p>
       <p *ngSwitchDefault>Guest View</p>
   </div>
   ```

---

### **3. Создание пользовательских директив**

#### **3.1 Атрибутивная директива**

Создаём директиву, которая меняет цвет фона при наведении.

**Шаги:**
1. Создайте директиву с помощью Angular CLI:
   ```bash
   ng generate directive highlight
   ```

2. Код директивы:
   ```typescript
   import { Directive, ElementRef, HostListener, Input } from '@angular/core';

   @Directive({
       selector: '[appHighlight]',
   })
   export class HighlightDirective {
       @Input() appHighlight = ''; // цвет, переданный в директиву

       constructor(private el: ElementRef) {}

       @HostListener('mouseenter') onMouseEnter() {
           this.highlight(this.appHighlight || 'yellow');
       }

       @HostListener('mouseleave') onMouseLeave() {
           this.highlight('');
       }

       private highlight(color: string) {
           this.el.nativeElement.style.backgroundColor = color;
       }
   }
   ```

3. Использование директивы:
   ```html
   <p appHighlight="lightblue">Hover to highlight!</p>
   ```

---

#### **3.2 Структурная директива**

Создаём директиву, которая добавляет элемент только если выполнено условие.

**Шаги:**
1. Создайте директиву с помощью Angular CLI:
   ```bash
   ng generate directive unless
   ```

2. Код директивы:
   ```typescript
   import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

   @Directive({
       selector: '[appUnless]',
   })
   export class UnlessDirective {
       @Input() set appUnless(condition: boolean) {
           if (!condition) {
               this.viewContainer.createEmbeddedView(this.templateRef);
           } else {
               this.viewContainer.clear();
           }
       }

       constructor(
           private templateRef: TemplateRef<any>,
           private viewContainer: ViewContainerRef
       ) {}
   }
   ```

3. Использование директивы:
   ```html
   <p *appUnless="isHidden">This is visible when 'isHidden' is false.</p>
   ```

---

### **4. Жизненный цикл директив**

Директивы, как и компоненты, имеют свой жизненный цикл, управляемый Angular. Основные хуки:
1. **`ngOnInit`** — вызывается при инициализации директивы.
2. **`ngOnChanges`** — вызывается при изменении входных данных.
3. **`ngOnDestroy`** — вызывается перед уничтожением директивы.

**Пример:**
```typescript
import { Directive, OnInit, OnDestroy } from '@angular/core';

@Directive({
    selector: '[appExample]',
})
export class ExampleDirective implements OnInit, OnDestroy {
    ngOnInit() {
        console.log('Directive initialized');
    }

    ngOnDestroy() {
        console.log('Directive destroyed');
    }
}
```

---

### **5. Рекомендуемые ресурсы**

1. **Официальная документация Angular по директивам:**  
   [Angular Directives Documentation](https://angular.io/guide/attribute-directives)

2. **Учебник по директивам в Angular:**  
   [Angular Directive Examples](https://angular.io/start)

3. **Руководство по структурным директивам:**  
   [Angular Structural Directives](https://angular.io/guide/structural-directives)

---

### **6. Заключение**

Директивы — мощный инструмент Angular, позволяющий управлять поведением элементов DOM. Благодаря встроенным и пользовательским директивам разработчики могут динамически изменять интерфейс приложения, делая его более гибким и интерактивным. Создание пользовательских директив упрощает повторное использование логики и улучшает читаемость кода.
