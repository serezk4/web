### **JAX-RS: Общие принципы**

**JAX-RS (Jakarta RESTful Web Services)** — это спецификация для разработки RESTful веб-служб в экосистеме Jakarta EE (ранее Java EE). Она упрощает создание веб-сервисов, поддерживающих REST (Representational State Transfer) архитектуру, и позволяет взаимодействовать с клиентами через HTTP.

---

### **1. Основные принципы JAX-RS**

#### **1.1 RESTful архитектура**
RESTful веб-службы используют принципы REST для обмена данными через HTTP. Основные принципы REST:
- **Ресурсы:** Идентифицируются URI (Uniform Resource Identifier).
- **Методы HTTP:** Используются для выполнения операций (GET, POST, PUT, DELETE и др.).
- **Статусы HTTP:** Отражают результат обработки запросов.
- **Без состояния:** Сервер не сохраняет состояние клиента между запросами.
- **Поддержка форматов данных:** JSON и XML используются для передачи данных.

#### **1.2 Стандартизированные аннотации**
JAX-RS предоставляет аннотации для определения RESTful сервисов, таких как:
- `@Path` — Указывает путь ресурса.
- `@GET`, `@POST`, `@PUT`, `@DELETE` — Указывает HTTP-метод.
- `@Produces` и `@Consumes` — Указывает формат данных (JSON, XML и т.д.).

---

### **2. Структура JAX-RS приложения**

1. **Ресурсные классы:** Определяют ресурсы и их обработчики (методы).
2. **HTTP методы:** Связывают методы ресурса с операциями (например, `GET` для чтения данных).
3. **Форматы данных:** JSON или XML для передачи данных между сервером и клиентом.
4. **Конфигурация:** Включает регистрацию RESTful сервиса в сервере приложений.

---

### **3. Основные аннотации JAX-RS**

#### **3.1 Ресурсы**

**@Path**
- Указывает URI-адрес для ресурса.
- Может быть применена к классу или методу.

**Пример:**
```java
@Path("/users")
public class UserResource {
    @GET
    @Path("/{id}")
    public String getUser(@PathParam("id") int id) {
        return "User ID: " + id;
    }
}
```

---

#### **3.2 HTTP методы**

1. **@GET:** Обрабатывает HTTP GET запросы.
2. **@POST:** Обрабатывает HTTP POST запросы.
3. **@PUT:** Обрабатывает HTTP PUT запросы.
4. **@DELETE:** Обрабатывает HTTP DELETE запросы.

**Пример:**
```java
@Path("/users")
public class UserResource {
    @GET
    public String getAllUsers() {
        return "List of users";
    }

    @POST
    public String createUser(String user) {
        return "User created: " + user;
    }
}
```

---

#### **3.3 Обработка данных**

**@Produces**
- Указывает формат данных, который возвращает сервер.
- Поддерживает `application/json`, `application/xml` и другие MIME-типы.

**@Consumes**
- Указывает формат данных, который может принимать сервер.

**Пример:**
```java
@Path("/users")
@Produces("application/json")
@Consumes("application/json")
public class UserResource {
    @POST
    public User createUser(User user) {
        // Логика создания пользователя
        return user;
    }
}
```

---

#### **3.4 Передача параметров**

1. **@PathParam**
   - Связывает переменную в URI с аргументом метода.

   **Пример:**
   ```java
   @GET
   @Path("/{id}")
   public String getUser(@PathParam("id") int id) {
       return "User ID: " + id;
   }
   ```

2. **@QueryParam**
   - Извлекает параметры из строки запроса.

   **Пример:**
   ```java
   @GET
   public String getUsers(@QueryParam("start") int start, @QueryParam("size") int size) {
       return "Start: " + start + ", Size: " + size;
   }
   ```

3. **@HeaderParam**
   - Извлекает значения из заголовков HTTP.

   **Пример:**
   ```java
   @GET
   public String getUserAgent(@HeaderParam("User-Agent") String userAgent) {
       return "User-Agent: " + userAgent;
   }
   ```

4. **@FormParam**
   - Извлекает значения из тела запроса (для данных форм).

   **Пример:**
   ```java
   @POST
   public String createUser(@FormParam("name") String name, @FormParam("email") String email) {
       return "User created: " + name + " (" + email + ")";
   }
   ```

---

#### **3.5 Обработка исключений**

1. **@ExceptionMapper**
   - Позволяет обрабатывать исключения и возвращать пользовательский ответ.

   **Пример:**
   ```java
   @Provider
   public class GenericExceptionMapper implements ExceptionMapper<Exception> {
       @Override
       public Response toResponse(Exception e) {
           return Response.status(Response.Status.INTERNAL_SERVER_ERROR)
                          .entity("Error: " + e.getMessage())
                          .build();
       }
   }
   ```

---

### **4. Конфигурация JAX-RS**

#### **4.1 Регистрация приложения**

Класс `Application` регистрирует ресурсы.

**Пример:**
```java
import jakarta.ws.rs.ApplicationPath;
import jakarta.ws.rs.core.Application;

@ApplicationPath("/api")
public class RestApplication extends Application {
    // Дополнительная конфигурация при необходимости
}
```

---

### **5. Пример JAX-RS приложения**

**Сущность User:**
```java
public class User {
    private int id;
    private String name;

    // Конструкторы, геттеры и сеттеры
}
```

**Ресурс UserResource:**
```java
import jakarta.ws.rs.*;
import jakarta.ws.rs.core.MediaType;
import java.util.HashMap;
import java.util.Map;

@Path("/users")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class UserResource {
    private static Map<Integer, User> users = new HashMap<>();

    @GET
    @Path("/{id}")
    public User getUser(@PathParam("id") int id) {
        return users.get(id);
    }

    @POST
    public User createUser(User user) {
        users.put(user.getId(), user);
        return user;
    }
}
```

**Конфигурация приложения:**
```java
@ApplicationPath("/api")
public class RestApplication extends Application {
}
```

---

### **6. Преимущества JAX-RS**

1. **Упрощённость:**
   - Лёгкий API с аннотациями.
2. **Модульность:**
   - Ресурсы легко делить на отдельные классы.
3. **Поддержка стандартов:**
   - Совместимость с JSON, XML, и другими стандартами.
4. **Интеграция:**
   - Легко интегрируется с Jakarta EE, CDI и другими технологиями.

---

### **7. Рекомендуемые ресурсы**

1. **Документация JAX-RS:**  
   [Jakarta RESTful Web Services Documentation](https://jakarta.ee/specifications/restful-ws/)

2. **Примеры от Baeldung:**  
   [Baeldung JAX-RS Guide](https://www.baeldung.com/jax-rs)

3. **Учебник Oracle по JAX-RS:**  
   [Oracle JAX-RS Tutorial](https://docs.oracle.com/javaee/7/tutorial/jaxrs.htm)

---

### **Заключение**

JAX-RS — мощный инструмент для создания RESTful веб-служб. Он упрощает разработку, обеспечивая разработчикам удобный API для работы с HTTP-запросами и ресурсами. Использование аннотаций делает код понятным, модульным и легко поддерживаемым.
