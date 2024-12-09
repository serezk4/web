### **CDI Beans: Именование, Фабрики и Перехватчики**

CDI (Contexts and Dependency Injection) предоставляет гибкие механизмы для управления компонентами (beans). Три ключевые концепции в CDI, связанные с именованием, созданием объектов (фабрики) и перехватом вызовов, обеспечивают высокую модульность и расширяемость приложений.

---

### **1. Именование CDI Beans**

#### **@Named**
Аннотация `@Named` используется для привязки CDI Bean к имени, чтобы оно было доступно в EL (Expression Language). Это удобно в JSF и других шаблонизаторах, где требуется интеграция с компонентами пользовательского интерфейса.

**Пример:**
1. **Объявление Bean:**
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

2. **Использование в JSF:**
   ```xml
   <h:outputText value="#{userBean.name}" />
   ```

---

### **2. Фабрики в CDI Beans**

#### **@Produces**
Фабрики в CDI позволяют создавать объекты, которые не могут быть напрямую распознаны контейнером. Это реализуется с помощью аннотации `@Produces`. Фабричный метод возвращает объект, который можно внедрить в другие компоненты.

#### **Пример использования фабрики:**

1. **Создание фабрики:**
   ```java
   import jakarta.enterprise.context.ApplicationScoped;
   import jakarta.enterprise.inject.Produces;

   @ApplicationScoped
   public class ServiceFactory {
       @Produces
       public SomeService createService() {
           return new SomeServiceImpl(); // Создаём экземпляр вручную
       }
   }
   ```

2. **Внедрение объекта:**
   ```java
   import jakarta.inject.Inject;
   import jakarta.enterprise.context.RequestScoped;

   @RequestScoped
   public class ServiceConsumer {
       @Inject
       private SomeService service;

       public void performAction() {
           service.doSomething();
       }
   }
   ```

#### **Создание объектов с параметрами:**
Фабрики позволяют создавать объекты с разными конфигурациями, используя квалификаторы.

**Пример:**
1. **Фабричный метод:**
   ```java
   import jakarta.enterprise.inject.Produces;
   import jakarta.enterprise.context.ApplicationScoped;

   @ApplicationScoped
   public class ConfigFactory {
       @Produces
       @Development
       public Config createDevConfig() {
           return new Config("dev");
       }

       @Produces
       @Production
       public Config createProdConfig() {
           return new Config("prod");
       }
   }
   ```

2. **Квалификаторы:**
   ```java
   import jakarta.inject.Qualifier;
   import java.lang.annotation.Retention;
   import java.lang.annotation.Target;
   import static java.lang.annotation.ElementType.*;
   import static java.lang.annotation.RetentionPolicy.RUNTIME;

   @Qualifier
   @Retention(RUNTIME)
   @Target({METHOD, FIELD, PARAMETER, TYPE})
   public @interface Development {}

   @Qualifier
   @Retention(RUNTIME)
   @Target({METHOD, FIELD, PARAMETER, TYPE})
   public @interface Production {}
   ```

3. **Использование:**
   ```java
   import jakarta.inject.Inject;

   public class ConfigConsumer {
       @Inject @Development
       private Config devConfig;

       @Inject @Production
       private Config prodConfig;
   }
   ```

---

### **3. Перехватчики в CDI Beans**

#### **@Interceptor**
Перехватчики (Interceptors) в CDI позволяют обрабатывать вызовы методов или событий перед их выполнением и после. Это полезно для таких задач, как:
- Логирование.
- Проверка безопасности.
- Управление транзакциями.

#### **Основные аннотации для перехватчиков:**
1. **@Interceptor:** Объявляет класс как перехватчик.
2. **@AroundInvoke:** Указывает метод, который будет перехватывать вызовы других методов.
3. **@InterceptorBinding:** Создаёт привязку для определения, где должен применяться перехватчик.

#### **Пример создания перехватчика:**

1. **Создание привязки (binding):**
   ```java
   import jakarta.interceptor.InterceptorBinding;
   import java.lang.annotation.Retention;
   import java.lang.annotation.Target;
   import static java.lang.annotation.ElementType.*;
   import static java.lang.annotation.RetentionPolicy.RUNTIME;

   @InterceptorBinding
   @Retention(RUNTIME)
   @Target({METHOD, TYPE})
   public @interface Logged {}
   ```

2. **Реализация перехватчика:**
   ```java
   import jakarta.interceptor.AroundInvoke;
   import jakarta.interceptor.Interceptor;
   import jakarta.interceptor.InvocationContext;

   @Logged
   @Interceptor
   public class LoggingInterceptor {
       @AroundInvoke
       public Object logMethod(InvocationContext ctx) throws Exception {
           System.out.println("Method called: " + ctx.getMethod().getName());
           Object result = ctx.proceed();
           System.out.println("Method finished: " + ctx.getMethod().getName());
           return result;
       }
   }
   ```

3. **Применение перехватчика:**
   ```java
   import jakarta.enterprise.context.RequestScoped;

   @RequestScoped
   @Logged
   public class SomeService {
       public void doSomething() {
           System.out.println("Business logic executed.");
       }
   }
   ```

#### **Результат:**
- При вызове метода `doSomething()` сначала будет выполнен код в перехватчике (`LoggingInterceptor`), а затем основная бизнес-логика.

---

### **Преимущества фабрик и перехватчиков**

1. **Фабрики (`@Produces`):**
   - Гибкость в создании сложных объектов.
   - Возможность изменять поведение или конфигурацию объектов без изменения их кода.
   - Поддержка пользовательских квалификаторов для настройки.

2. **Перехватчики (`@Interceptor`):**
   - Централизованное управление аспектами, такими как логирование, безопасность, транзакции.
   - Уменьшение дублирования кода.
   - Простота добавления новых аспектов.

---

### **Рекомендуемые ресурсы**

1. **Jakarta CDI Specification:**  
   [Jakarta CDI Documentation](https://jakarta.ee/specifications/cdi/)

2. **Примеры CDI от Baeldung:**  
   [Baeldung CDI Guide](https://www.baeldung.com/cdi)

3. **Официальный учебник Jakarta EE:**  
   [Jakarta EE Tutorials](https://jakarta.ee)

---

### **Заключение**

CDI предоставляет мощные механизмы для управления жизненным циклом и поведением объектов:
- **Именование (`@Named`)** делает бины доступными для UI.
- **Фабрики (`@Produces`)** упрощают создание объектов с особой конфигурацией.
- **Перехватчики (`@Interceptor`)** позволяют обрабатывать вызовы методов, что делает код более модульным и удобным для поддержки.

Эти инструменты повышают гибкость и расширяемость приложений, соответствуя требованиям современных корпоративных систем.
