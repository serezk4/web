### **Angular: Dependency Injection (DI)**

**Dependency Injection (DI)** или инъекция зависимостей — это ключевая концепция Angular, которая позволяет управлять зависимостями между компонентами, сервисами и другими сущностями приложения. Angular предоставляет встроенный механизм DI для создания, обмена и управления объектами.

---

### **1. Что такое DI в Angular?**

**Инъекция зависимостей** — это способ предоставления объектов (зависимостей) классу, вместо их явного создания внутри класса. DI улучшает:
- **Модульность:** Код становится более гибким и разделяемым.
- **Тестируемость:** Упрощается создание тестов через внедрение mock-объектов.

#### **Ключевые элементы DI в Angular:**
1. **Provider (Поставщик):**  
   Указывает, как создавать зависимость.
2. **Injector (Инжектор):**  
   Хранилище зависимостей, которое отвечает за их создание и управление.
3. **Dependency Token (Токен зависимости):**  
   Уникальный идентификатор для предоставляемой зависимости.

---

### **2. Как работает DI в Angular?**

1. **Зависимости регистрируются в `providers` модулей, компонентов или сервисов.**
2. **Angular создаёт инжектор, который хранит зарегистрированные зависимости.**
3. **Когда компоненту или сервису требуется зависимость, Angular предоставляет её через конструктор.**

---

### **3. Регистрация зависимостей**

#### **3.1 Регистрация на уровне модуля**
- Сервис доступен для всех компонентов модуля.

**Пример:**
```typescript
@NgModule({
    providers: [MyService]
})
export class AppModule {}
```

---

#### **3.2 Регистрация на уровне компонента**
- Сервис доступен только для данного компонента и его дочерних компонентов.

**Пример:**
```typescript
@Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    providers: [MyService]
})
export class AppComponent {}
```

---

#### **3.3 Регистрация через `@Injectable`**
- Если указать `providedIn: 'root'`, Angular автоматически регистрирует сервис на уровне всего приложения.

**Пример:**
```typescript
@Injectable({
    providedIn: 'root'
})
export class MyService {
    getData() {
        return 'Hello from service!';
    }
}
```

---

### **4. Инъекция зависимостей в компоненты**

1. **Добавьте зависимость через конструктор.**
2. **Angular автоматически предоставит объект через инжектор.**

**Пример:**
```typescript
import { Component } from '@angular/core';
import { MyService } from './my-service.service';

@Component({
    selector: 'app-root',
    template: `<p>{{ message }}</p>`
})
export class AppComponent {
    message: string;

    constructor(private myService: MyService) {
        this.message = this.myService.getData();
    }
}
```

---

### **5. Типы провайдеров**

#### **5.1 Class Provider**
- Используется по умолчанию. Регистрация класса как провайдера.

**Пример:**
```typescript
providers: [MyService]
```

---

#### **5.2 Value Provider**
- Предоставляет фиксированное значение.

**Пример:**
```typescript
providers: [
    { provide: 'API_URL', useValue: 'https://api.example.com' }
]
```

Использование:
```typescript
constructor(@Inject('API_URL') private apiUrl: string) {
    console.log(this.apiUrl);
}
```

---

#### **5.3 Factory Provider**
- Использует фабричную функцию для создания объекта.

**Пример:**
```typescript
providers: [
    {
        provide: MyService,
        useFactory: () => new MyService('Custom data')
    }
]
```

---

#### **5.4 Existing Provider**
- Использует другой провайдер для предоставления зависимости.

**Пример:**
```typescript
providers: [
    { provide: MyServiceAlias, useExisting: MyService }
]
```

---

### **6. Виды инжекторов**

#### **6.1 Корневой инжектор**
- Хранит зависимости, зарегистрированные с помощью `providedIn: 'root'`.
- Доступен для всех модулей приложения.

#### **6.2 Локальный инжектор**
- Создаётся на уровне модуля или компонента.
- Хранит зависимости, зарегистрированные в `providers` компонента или модуля.

---

### **7. Токены зависимостей**

Токены используются для идентификации зависимостей. В Angular могут быть:
1. **Классовые токены:**  
   Используются для обычных сервисов.
   ```typescript
   providers: [MyService]
   ```

2. **Строковые токены:**  
   Используются для значения (например, строки).
   ```typescript
   providers: [{ provide: 'API_URL', useValue: 'https://api.example.com' }]
   ```

3. **OpaqueToken (Токены типа):**  
   Создаются с помощью `InjectionToken` для сложных зависимостей.
   ```typescript
   import { InjectionToken } from '@angular/core';

   export const CONFIG = new InjectionToken<string>('App Config');

   providers: [
       { provide: CONFIG, useValue: { apiUrl: 'https://api.example.com' } }
   ];
   ```

---

### **8. Жизненный цикл сервисов**

1. **Создание:**  
   Сервис создаётся Angular при первом обращении.
   
2. **Кэширование:**  
   Зависимость кэшируется и повторно используется.
   
3. **Уничтожение:**  
   Сервис уничтожается при уничтожении инжектора (например, при размонтировании компонента).

---

### **9. Примеры DI в Angular**

#### **9.1 Использование нескольких провайдеров**
```typescript
providers: [
    { provide: 'API_URL', useValue: 'https://api.example.com' },
    { provide: MyService, useClass: CustomService }
]
```

#### **9.2 Инъекция зависимости в конструктор**
```typescript
constructor(@Inject('API_URL') private apiUrl: string) {
    console.log(this.apiUrl);
}
```

---

### **10. Рекомендуемые ресурсы**

1. **Официальная документация Angular по DI:**  
   [Dependency Injection Guide](https://angular.io/guide/dependency-injection)

2. **Видео-уроки по DI в Angular:**  
   [Angular Dependency Injection](https://www.youtube.com/results?search_query=angular+dependency+injection)

---

### **Заключение**

Dependency Injection в Angular предоставляет мощный и гибкий механизм для управления зависимостями. Этот подход улучшает:
- Модульность.
- Повторное использование кода.
- Удобство тестирования.

Благодаря DI, Angular позволяет строить масштабируемые и легко поддерживаемые приложения.
