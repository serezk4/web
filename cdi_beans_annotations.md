### **Аннотации CDI Beans**

CDI (Contexts and Dependency Injection) предоставляет набор аннотаций для определения, конфигурации и управления жизненным циклом бинов. Эти аннотации используются для инъекции зависимостей, настройки областей видимости, управления событиями и других задач.

---

### **Основные аннотации CDI Beans**

#### **1. Аннотации для инъекции зависимостей**

1. **@Inject**
   - Используется для внедрения зависимостей в поля, конструкторы и методы.
   - Контейнер автоматически разрешает зависимость и предоставляет экземпляр.

   **Пример:**
   ```java
   import jakarta.inject.Inject;

   public class ServiceConsumer {
       @Inject
       private SomeService service;

       public void performAction() {
           service.doSomething();
       }
   }
   ```

2. **@Qualifier**
   - Используется для создания пользовательских квалификаторов, которые помогают различать бины одного типа.

   **Пример:**
   ```java
   @Qualifier
   @Retention(RUNTIME)
   @Target({FIELD, METHOD, PARAMETER, TYPE})
   public @interface Special {}

   @Special
   public class SpecialService implements SomeService {
       @Override
       public void doSomething() {
           System.out.println("Special Service");
       }
   }

   public class ServiceConsumer {
       @Inject
       @Special
       private SomeService service;
   }
   ```

3. **@Produces**
   - Используется для создания объектов, которые не могут быть автоматически распознаны CDI-контейнером.

   **Пример:**
   ```java
   import jakarta.enterprise.inject.Produces;

   public class Producer {
       @Produces
       public SomeService createService() {
           return new SomeServiceImpl();
       }
   }
   ```

4. **@Disposes**
   - Используется для очистки ресурсов, созданных с помощью `@Produces`.

   **Пример:**
   ```java
   public class Producer {
       @Produces
       public Resource createResource() {
           return new Resource();
       }

       public void closeResource(@Disposes Resource resource) {
           resource.close();
       }
   }
   ```

---

#### **2. Аннотации для управления областями видимости**

1. **@RequestScoped**
   - Указывает, что бин живёт в течение одного HTTP-запроса.
   - Подходит для кратковременных операций.

   **Пример:**
   ```java
   import jakarta.enterprise.context.RequestScoped;

   @RequestScoped
   public class RequestScopedBean {
       // Код
   }
   ```

2. **@SessionScoped**
   - Указывает, что бин живёт в течение HTTP-сессии.
   - Используется для хранения пользовательских данных между запросами.

   **Пример:**
   ```java
   import jakarta.enterprise.context.SessionScoped;
   import java.io.Serializable;

   @SessionScoped
   public class SessionScopedBean implements Serializable {
       // Код
   }
   ```

3. **@ApplicationScoped**
   - Указывает, что бин создаётся один раз на всё время работы приложения.
   - Подходит для хранения глобальных данных или кэша.

   **Пример:**
   ```java
   import jakarta.enterprise.context.ApplicationScoped;

   @ApplicationScoped
   public class ApplicationScopedBean {
       // Код
   }
   ```

4. **@Dependent**
   - Область видимости по умолчанию.
   - Бин живёт столько, сколько живёт его клиент.

   **Пример:**
   ```java
   import jakarta.enterprise.context.Dependent;

   @Dependent
   public class DependentBean {
       // Код
   }
   ```

5. **@ConversationScoped**
   - Указывает, что бин живёт в течение пользовательского диалога.
   - Требует явного управления началом и завершением диалога.

   **Пример:**
   ```java
   import jakarta.enterprise.context.ConversationScoped;
   import jakarta.enterprise.context.Conversation;
   import jakarta.inject.Inject;
   import java.io.Serializable;

   @ConversationScoped
   public class ConversationBean implements Serializable {
       @Inject
       private Conversation conversation;

       public void begin() {
           conversation.begin();
       }

       public void end() {
           conversation.end();
       }
   }
   ```

---

#### **3. Аннотации для событий**

1. **@Observes**
   - Используется для обработки событий.

   **Пример:**
   ```java
   public class EventConsumer {
       public void handleEvent(@Observes String event) {
           System.out.println("Event received: " + event);
       }
   }
   ```

2. **@Event**
   - Используется для публикации событий.

   **Пример:**
   ```java
   import jakarta.enterprise.event.Event;
   import jakarta.inject.Inject;

   public class EventProducer {
       @Inject
       private Event<String> event;

       public void publishEvent(String message) {
           event.fire(message);
       }
   }
   ```

---

#### **4. Аннотации для именования и использования в EL**

1. **@Named**
   - Делает бин доступным в EL (Expression Language).
   - Обычно используется в JSF.

   **Пример:**
   ```java
   import jakarta.inject.Named;
   import jakarta.enterprise.context.RequestScoped;

   @Named("userBean")
   @RequestScoped
   public class UserBean {
       private String name;

       public String getName() {
           return name;
       }

       public void setName(String name) {
           this.name = name;
       }
   }
   ```

   **Использование в JSF:**
   ```xml
   <h:outputText value="#{userBean.name}" />
   ```

---

#### **5. Аннотации для декораторов и интерсепторов**

1. **@Decorator**
   - Используется для изменения поведения других бинов.

   **Пример:**
   ```java
   import jakarta.decorator.Decorator;
   import jakarta.decorator.Delegate;
   import jakarta.inject.Inject;

   @Decorator
   public class LoggingDecorator implements Service {
       @Inject
       @Delegate
       private Service delegate;

       @Override
       public void perform() {
           System.out.println("Before service");
           delegate.perform();
           System.out.println("After service");
       }
   }
   ```

2. **@Interceptor**
   - Используется для перехвата вызовов методов.

   **Пример:**
   ```java
   import jakarta.interceptor.Interceptor;
   import jakarta.interceptor.AroundInvoke;
   import jakarta.interceptor.InvocationContext;

   @Interceptor
   public class LoggingInterceptor {
       @AroundInvoke
       public Object log(InvocationContext ctx) throws Exception {
           System.out.println("Before method: " + ctx.getMethod().getName());
           Object result = ctx.proceed();
           System.out.println("After method: " + ctx.getMethod().getName());
           return result;
       }
   }
   ```

---

### **Рекомендуемые ресурсы**

1. **Jakarta CDI Specification:**  
   [Официальная спецификация Jakarta CDI](https://jakarta.ee/specifications/cdi/)

2. **Baeldung CDI Guide:**  
   [Baeldung Guide on CDI](https://www.baeldung.com/cdi)

3. **Oracle Java EE CDI Tutorial:**  
   [Oracle CDI Tutorial](https://docs.oracle.com/javaee/7/tutorial/cdi-basic.htm)

---

### **Заключение**

Аннотации CDI предоставляют мощный инструмент для упрощения разработки приложений в Jakarta EE. Они позволяют управлять зависимостями, событиями, контекстами и поведением бинов, делая код более модульным и читаемым. Хорошее понимание этих аннотаций — основа эффективного использования CDI.
