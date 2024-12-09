### **JSF: Managed Beans**

#### **Что такое Managed Bean**
**Managed Bean** — это Java-класс, который управляется контейнером JSF. Он является центральной частью технологии JSF, служащей связующим звеном между пользовательским интерфейсом (View) и бизнес-логикой (Model). 

Managed Bean выполняет следующие роли:
1. Обработка пользовательских действий, таких как ввод данных и нажатие кнопок.
2. Хранение данных, необходимых для отображения и обработки.
3. Управление навигацией между страницами JSF.

---

#### **Аннотации для Managed Beans**
Существует несколько аннотаций для создания Managed Bean:

1. **@ManagedBean** *(устаревший способ, используется в JSF 2.0):*
   ```java
   import jakarta.faces.bean.ManagedBean;

   @ManagedBean
   public class MyBean {
       private String message = "Hello, JSF!";

       public String getMessage() {
           return message;
       }

       public void setMessage(String message) {
           this.message = message;
       }
   }
   ```

2. **@Named и @RequestScoped** *(рекомендуемый способ, используется в JSF с CDI):*
   Managed Beans можно создавать через CDI (Contexts and Dependency Injection):
   ```java
   import jakarta.inject.Named;
   import jakarta.enterprise.context.RequestScoped;

   @Named
   @RequestScoped
   public class MyBean {
       private String message = "Hello, CDI!";

       public String getMessage() {
           return message;
       }

       public void setMessage(String message) {
           this.message = message;
       }
   }
   ```

---

#### **Жизненные циклы Managed Beans**
В зависимости от аннотации область видимости Managed Bean определяет, как долго объект будет существовать:

1. **@RequestScoped:** Живёт в течение одного HTTP-запроса. Подходит для простых операций.
   - Пример: форма, отправляемая на сервер.

2. **@SessionScoped:** Живёт в течение пользовательской сессии. Данные сохраняются между запросами.
   - Пример: информация о пользователе, вошедшем в систему.

3. **@ApplicationScoped:** Делится между всеми пользователями приложения и живёт в течение всего времени работы приложения.
   - Пример: общие настройки приложения.

4. **@ViewScoped:** Живёт в течение времени отображения одной страницы. Сохраняет данные между запросами, пока пользователь взаимодействует с конкретной страницей.
   - Пример: данные формы, сохраняющиеся при обновлении страницы.

5. **@CustomScoped:** Позволяет задавать собственную область видимости.

---

#### **Пример использования Managed Beans**
**1. Определение Managed Bean:**
   ```java
   import jakarta.inject.Named;
   import jakarta.enterprise.context.RequestScoped;

   @Named
   @RequestScoped
   public class UserBean {
       private String username;

       public String getUsername() {
           return username;
       }

       public void setUsername(String username) {
           this.username = username;
       }

       public String greetUser() {
           return "Hello, " + username + "!";
       }
   }
   ```

**2. Использование в JSF-странице (XHTML):**
   ```xml
   <!DOCTYPE html>
   <html xmlns="http://www.w3.org/1999/xhtml"
         xmlns:h="http://xmlns.jcp.org/jsf/html">
   <h:head>
       <title>Managed Bean Example</title>
   </h:head>
   <h:body>
       <h:form>
           <h:outputLabel for="username" value="Enter your name:" />
           <h:inputText id="username" value="#{userBean.username}" />
           <h:commandButton value="Greet" action="#{userBean.greetUser}" />
       </h:form>
   </h:body>
   </html>
   ```

---

#### **Ссылки на Managed Beans в JSF**
В JSF используется **EL (Expression Language)** для доступа к свойствам и методам Managed Bean:
- `#{bean.property}` — доступ к свойству.
- `#{bean.method}` — вызов метода.

---

#### **Способы внедрения зависимостей**
Managed Beans поддерживают внедрение зависимостей, что позволяет инкапсулировать логику:

1. **Внедрение через CDI:**
   ```java
   import jakarta.inject.Named;
   import jakarta.enterprise.context.RequestScoped;

   @Named
   @RequestScoped
   public class ExampleBean {
       @Inject
       private Service service;

       public String performAction() {
           return service.doSomething();
       }
   }
   ```

2. **Внедрение через аннотацию @ManagedProperty:**
   ```java
   import jakarta.faces.bean.ManagedBean;
   import jakarta.faces.bean.ManagedProperty;

   @ManagedBean
   public class ExampleBean {
       @ManagedProperty(value = "#{myService}")
       private MyService myService;

       public void setMyService(MyService myService) {
           this.myService = myService;
       }
   }
   ```

---

#### **Преимущества Managed Beans**
1. **Интеграция с JSF и CDI:** Упрощает взаимодействие между слоями приложения.
2. **Модульность:** Чёткое разделение логики и интерфейса.
3. **Поддержка инъекций:** Упрощает работу с зависимостями.

---

#### **Недостатки Managed Beans**
1. **Устаревшие подходы:** Аннотация `@ManagedBean` не рекомендуется для новых проектов.
2. **Ограниченная гибкость:** Для сложных сценариев требуется использование CDI.

---

#### **Заключение**
Managed Beans являются ключевым элементом JSF, обеспечивая связь между представлением и бизнес-логикой. Хотя подход с использованием `@ManagedBean` постепенно заменяется CDI, знание этой концепции остаётся важным для работы с JSF.
