### **JAX-RS: Основные Аннотации**

JAX-RS (Jakarta RESTful Web Services) предоставляет набор аннотаций для создания RESTful веб-служб. Эти аннотации определяют ресурсы, обрабатывают HTTP-запросы, настраивают форматы данных, и позволяют передавать параметры между клиентом и сервером.

---

### **1. Аннотации для определения REST-ресурсов**

#### **@Path**
- Указывает URI-адрес для ресурса или метода.
- Может быть применена на уровне класса или метода.
- Поддерживает параметризованные пути (например, `/{id}`).

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

### **2. Аннотации для HTTP-методов**

1. **@GET**
   - Обрабатывает HTTP GET запросы.
   - Обычно используется для получения данных.

   **Пример:**
   ```java
   @GET
   public String getUsers() {
       return "List of users";
   }
   ```

2. **@POST**
   - Обрабатывает HTTP POST запросы.
   - Обычно используется для создания новых ресурсов.

   **Пример:**
   ```java
   @POST
   public String createUser(String user) {
       return "User created: " + user;
   }
   ```

3. **@PUT**
   - Обрабатывает HTTP PUT запросы.
   - Обычно используется для обновления существующих ресурсов.

   **Пример:**
   ```java
   @PUT
   @Path("/{id}")
   public String updateUser(@PathParam("id") int id, String user) {
       return "User updated: " + user;
   }
   ```

4. **@DELETE**
   - Обрабатывает HTTP DELETE запросы.
   - Используется для удаления ресурсов.

   **Пример:**
   ```java
   @DELETE
   @Path("/{id}")
   public String deleteUser(@PathParam("id") int id) {
       return "User deleted: " + id;
   }
   ```

5. **@HEAD**
   - Обрабатывает HTTP HEAD запросы.
   - Возвращает заголовки ответа без тела.

6. **@OPTIONS**
   - Обрабатывает HTTP OPTIONS запросы.
   - Используется для получения поддерживаемых методов ресурса.

---

### **3. Аннотации для работы с форматами данных**

#### **@Produces**
- Указывает формат данных, возвращаемых сервером.
- Поддерживает MIME-типы, такие как `application/json`, `application/xml`, `text/plain`.

**Пример:**
```java
@GET
@Produces("application/json")
public User getUser() {
    return new User(1, "John Doe");
}
```

---

#### **@Consumes**
- Указывает формат данных, который может принимать сервер.
- Поддерживает MIME-типы, такие как `application/json`, `application/xml`, `text/plain`.

**Пример:**
```java
@POST
@Consumes("application/json")
public String createUser(User user) {
    return "User created: " + user.getName();
}
```

---

### **4. Аннотации для передачи параметров**

#### **@PathParam**
- Извлекает параметры из URI.

**Пример:**
```java
@GET
@Path("/{id}")
public String getUser(@PathParam("id") int id) {
    return "User ID: " + id;
}
```

---

#### **@QueryParam**
- Извлекает параметры из строки запроса (после `?` в URL).

**Пример:**
```java
@GET
public String getUsers(@QueryParam("start") int start, @QueryParam("size") int size) {
    return "Start: " + start + ", Size: " + size;
}
```

---

#### **@HeaderParam**
- Извлекает параметры из заголовков HTTP.

**Пример:**
```java
@GET
public String getUserAgent(@HeaderParam("User-Agent") String userAgent) {
    return "User-Agent: " + userAgent;
}
```

---

#### **@FormParam**
- Извлекает параметры из данных формы, отправленных через `application/x-www-form-urlencoded`.

**Пример:**
```java
@POST
public String createUser(@FormParam("name") String name, @FormParam("email") String email) {
    return "User created: " + name + " (" + email + ")";
}
```

---

#### **@MatrixParam**
- Извлекает параметры из матричной части URI (например, `/users;role=admin`).

**Пример:**
```java
@GET
@Path("/users")
public String getUsers(@MatrixParam("role") String role) {
    return "Role: " + role;
}
```

---

### **5. Аннотации для обработки исключений**

#### **@Provider**
- Указывает, что класс является провайдером (например, `ExceptionMapper` или фильтр).

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

#### **@ExceptionMapper**
- Используется для обработки исключений и возвращения пользовательского ответа.

**Пример:**
```java
@Provider
public class NotFoundExceptionMapper implements ExceptionMapper<NotFoundException> {
    @Override
    public Response toResponse(NotFoundException e) {
        return Response.status(Response.Status.NOT_FOUND)
                      .entity("Resource not found")
                      .build();
    }
}
```

---

### **6. Аннотации для фильтров и интерсепторов**

#### **@PreMatching**
- Указывает, что фильтр должен быть выполнен до сопоставления ресурса.

---

#### **@NameBinding**
- Используется для создания привязок фильтров или интерсепторов к определённым методам.

---

### **7. Аннотации для асинхронной обработки**

#### **@Suspended**
- Используется для асинхронной обработки запросов.

**Пример:**
```java
@GET
public void asyncGet(@Suspended AsyncResponse asyncResponse) {
    new Thread(() -> {
        asyncResponse.resume("Async Response");
    }).start();
}
```

---

### **Рекомендуемые ресурсы**

1. **Официальная спецификация JAX-RS:**  
   [Jakarta RESTful Web Services Documentation](https://jakarta.ee/specifications/restful-ws/)

2. **Учебник Oracle по JAX-RS:**  
   [Oracle JAX-RS Tutorial](https://docs.oracle.com/javaee/7/tutorial/jaxrs.htm)

3. **Baeldung JAX-RS Guide:**  
   [Baeldung JAX-RS Annotations](https://www.baeldung.com/jax-rs-annotations)

---

### **Заключение**

JAX-RS предоставляет широкий набор аннотаций для разработки RESTful веб-служб. Эти аннотации упрощают обработку HTTP-запросов, передачу данных и управление ресурсами, делая разработку RESTful API интуитивно понятной и удобной.
