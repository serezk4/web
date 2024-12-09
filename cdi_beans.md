### **CDI Beans: Общее понятие**

**CDI Beans** — это управляемые компоненты (или бины), которые работают в рамках спецификации **CDI (Contexts and Dependency Injection)** в Jakarta EE (ранее Java EE). Они обеспечивают мощный механизм инъекции зависимостей, управление жизненным циклом объектов и контекстное взаимодействие между компонентами приложения.

---

### **Основные особенности CDI Beans**

1. **Управление жизненным циклом:**
   - Контейнер управляет созданием, жизненным циклом и уничтожением CDI Beans.
   - Время жизни зависит от области видимости (scope).

2. **Инъекция зависимостей:**
   - CDI автоматически внедряет зависимости между компонентами.
   - Используются аннотации `@Inject`, `@Produces` и квалификаторы (`@Qualifier`).

3. **Контексты:**
   - CDI поддерживает контекстно-зависимые объекты, области видимости которых управляются контейнером.

4. **События:**
   - CDI поддерживает публикацию и обработку событий через механизм событий (`@Observes`).

5. **Расширяемость:**
   - CDI предоставляет API для добавления пользовательских расширений через интерфейсы `Extension`.

---

### **Типы CDI Beans**

CDI поддерживает несколько типов бинов:

1. **Managed Beans (Управляемые бины):**
   - Простые Java-классы, управляемые CDI-контейнером.
   - Должны быть аннотированы, например, `@Named` или быть в области видимости (`@RequestScoped`, `@SessionScoped` и др.).

2. **Session Beans (EJB):**
   - Бины с более сложными возможностями, такими как транзакционность, таймеры и удалённый доступ.

3. **Producer Beans:**
   - Бины, создаваемые через фабричные методы с использованием аннотации `@Produces`.

4. **Interceptor Beans:**
   - Бины, реализующие функциональность перехватчиков для обработки вызовов методов (например, транзакций).

5. **Decorator Beans:**
   - Бины для динамического изменения поведения других бинов.

---

### **Основные аннотации CDI**

1. **@Inject:**
   - Используется для внедрения зависимостей.
   ```java
   @Inject
   private SomeService service;
   ```

2. **@Qualifier:**
   - Создаёт уникальные идентификаторы для внедряемых объектов.
   ```java
   @Qualifier
   @Retention(RUNTIME)
   @Target({FIELD, METHOD, PARAMETER, TYPE})
   public @interface MyQualifier {}
   ```

3. **@Produces:**
   - Позволяет создавать бины с помощью фабричных методов.
   ```java
   @Produces
   public MyBean createMyBean() {
       return new MyBean();
   }
   ```

4. **@Named:**
   - Делает бин доступным через EL (Expression Language).
   ```java
   @Named("userBean")
   @RequestScoped
   public class UserBean {
       // Код
   }
   ```

5. **Области видимости (Scopes):**
   - **@RequestScoped:** Живёт в течение одного HTTP-запроса.
   - **@SessionScoped:** Живёт в течение HTTP-сессии.
   - **@ApplicationScoped:** Живёт в течение времени работы приложения.
   - **@Dependent:** Область по умолчанию, бин живёт столько, сколько его клиент.

---

### **Пример использования CDI Beans**

#### **1. Определение бина**
```java
import jakarta.enterprise.context.RequestScoped;

@RequestScoped
public class GreetingService {
    public String greet(String name) {
        return "Hello, " + name;
    }
}
```

#### **2. Внедрение бина**
```java
import jakarta.inject.Inject;
import jakarta.enterprise.context.RequestScoped;

@RequestScoped
public class GreetingController {
    @Inject
    private GreetingService greetingService;

    public String getGreeting(String name) {
        return greetingService.greet(name);
    }
}
```

#### **3. Использование в JSF (XHTML):**
```xml
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html">
<h:head>
    <title>CDI Bean Example</title>
</h:head>
<h:body>
    <h:form>
        <h:inputText value="#{greetingController.name}" />
        <h:commandButton value="Greet" action="#{greetingController.getGreeting}" />
    </h:form>
</h:body>
</html>
```

---

### **Области видимости CDI Beans**

#### **1. @RequestScoped:**
- Живёт только в рамках одного HTTP-запроса.
- Подходит для операций, не требующих сохранения состояния.

#### **2. @SessionScoped:**
- Живёт в течение всей HTTP-сессии.
- Используется для сохранения пользовательских данных.

#### **3. @ApplicationScoped:**
- Единственный экземпляр для всего приложения.
- Подходит для кэширования или глобальных данных.

#### **4. @Dependent (по умолчанию):**
- Живёт столько, сколько его клиент.
- Не имеет собственного контекста.

#### **5. @ConversationScoped:**
- Используется для управления пользовательскими диалогами (например, мастер-формы).
- Требует явного управления (начало и завершение диалога).

---

### **Преимущества CDI Beans**

1. **Снижение сложности:**
   - Упрощается управление зависимостями и жизненным циклом объектов.

2. **Универсальность:**
   - Поддерживает множество областей видимости и гибкое управление бинами.

3. **Модульность:**
   - Лёгкая интеграция компонентов через событийную модель и квалификаторы.

4. **Расширяемость:**
   - Возможность создания собственных расширений и декораторов.

5. **Интеграция:**
   - Поддерживает взаимодействие с другими технологиями Jakarta EE, такими как JSF, EJB и JPA.

---

### **Полезные ссылки для изучения**

1. **Документация Jakarta CDI:**  
   [Jakarta CDI Specification](https://jakarta.ee/specifications/cdi/)

2. **Учебник Oracle по Java EE (CDI):**  
   [Oracle Java EE CDI Tutorial](https://docs.oracle.com/javaee/7/tutorial/cdi-basic.htm)

3. **Примеры CDI от Baeldung:**  
   [Baeldung CDI Guide](https://www.baeldung.com/cdi)

---

### **Заключение**

CDI Beans — это основа контекстно-зависимых приложений в Java EE/Jakarta EE. Они упрощают разработку, предоставляя инъекцию зависимостей, управление жизненным циклом и мощные механизмы взаимодействия. Понимание концепций CDI Beans важно для создания масштабируемых, модульных и надёжных приложений.
