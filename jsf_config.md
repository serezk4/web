### **JSF: Конфигурация**

Конфигурация JSF определяет, как веб-приложение будет использовать технологию JSF, включая настройки жизненного цикла, навигации, ресурсов и состояния. В JSF конфигурация выполняется через XML-файлы, аннотации и стандартные соглашения. 

---

#### **Основные элементы конфигурации**

1. **web.xml**  
   Файл `web.xml` — это стандартный дескриптор развёртывания Java-сервлетов. Он используется для настройки сервлетов JSF и URL-шаблонов.

2. **faces-config.xml**  
   Файл `faces-config.xml` — основной файл конфигурации JSF, где настраиваются бины, навигация и другие параметры.

3. **Аннотации**  
   Современные JSF-приложения используют аннотации, такие как `@ManagedBean`, `@FacesConfig`, и настройки CDI, чтобы уменьшить зависимость от XML.

---

#### **Конфигурация через web.xml**

В файле `web.xml` определяются настройки сервлетов JSF и их отображения. Пример минимальной конфигурации:

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- JSF сервлет -->
    <servlet>
        <servlet-name>FacesServlet</servlet-name>
        <servlet-class>jakarta.faces.webapp.FacesServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- Маппинг JSF -->
    <servlet-mapping>
        <servlet-name>FacesServlet</servlet-name>
        <url-pattern>*.xhtml</url-pattern>
    </servlet-mapping>

    <!-- Кодировка -->
    <context-param>
        <param-name>javax.servlet.jsp.jstl.fmt.localizationContext</param-name>
        <param-value>resources.application</param-value>
    </context-param>

</web-app>
```

##### **Объяснение параметров:**
- `<servlet>` — объявление FacesServlet, который обрабатывает все запросы JSF.
- `<url-pattern>` — шаблон URL-адресов, обрабатываемых JSF (чаще используется `*.xhtml`).
- `<load-on-startup>` — указывает, что FacesServlet загружается при запуске приложения.

---

#### **Конфигурация через faces-config.xml**

Файл `faces-config.xml` позволяет настраивать навигацию, компоненты, конвертеры и бины.

Пример минимального `faces-config.xml`:

```xml
<faces-config xmlns="http://xmlns.jcp.org/xml/ns/javaee"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
                                  http://xmlns.jcp.org/xml/ns/javaee/web-facesconfig_2_3.xsd"
              version="2.3">

    <!-- Навигация -->
    <navigation-rule>
        <from-view-id>/login.xhtml</from-view-id>
        <navigation-case>
            <from-outcome>success</from-outcome>
            <to-view-id>/home.xhtml</to-view-id>
        </navigation-case>
    </navigation-rule>

    <!-- Объявление бина -->
    <managed-bean>
        <managed-bean-name>userBean</managed-bean-name>
        <managed-bean-class>com.example.UserBean</managed-bean-class>
        <managed-bean-scope>session</managed-bean-scope>
    </managed-bean>

</faces-config>
```

##### **Объяснение параметров:**
- `<navigation-rule>` — определяет переходы между страницами.
- `<managed-bean>` — объявляет Managed Bean, включая его имя, класс и область видимости.

---

#### **Конфигурация через аннотации**

Современные версии JSF позволяют избегать XML, используя аннотации:

1. **@FacesConfig**  
   Указывает, что приложение использует JSF 2.3 и выше:
   ```java
   import jakarta.faces.annotation.FacesConfig;

   @FacesConfig(version = FacesConfig.Version.JSF_2_3)
   public class ApplicationConfig {
   }
   ```

2. **@ManagedBean**  
   Объявляет Managed Bean:
   ```java
   import jakarta.faces.bean.ManagedBean;
   import jakarta.faces.bean.SessionScoped;

   @ManagedBean
   @SessionScoped
   public class UserBean {
       private String username;

       // Геттеры и сеттеры
   }
   ```

3. **@Named и CDI-аннотации:**  
   Рекомендуемый способ с использованием CDI:
   ```java
   import jakarta.enterprise.context.SessionScoped;
   import jakarta.inject.Named;

   @Named
   @SessionScoped
   public class UserBean {
       private String username;

       // Геттеры и сеттеры
   }
   ```

---

#### **Настройка ресурсов (CSS, JS, изображения)**

JSF позволяет организовать статические ресурсы через папку `resources`.

**Пример структуры папки `resources`:**
```
/webapp
  /resources
    /css
      style.css
    /js
      script.js
    /images
      logo.png
```

**Подключение ресурсов в JSF:**
```xml
<h:outputStylesheet name="css/style.css" />
<h:outputScript name="js/script.js" />
<h:graphicImage name="images/logo.png" />
```

---

#### **Настройка сохранения состояния**

JSF по умолчанию сохраняет состояние компонентов (stateful модель). Это поведение можно изменить:

1. **Включение client-side сохранения:**
   В `web.xml`:
   ```xml
   <context-param>
       <param-name>javax.faces.STATE_SAVING_METHOD</param-name>
       <param-value>client</param-value>
   </context-param>
   ```

2. **Server-side сохранение (по умолчанию):**
   ```xml
   <context-param>
       <param-name>javax.faces.STATE_SAVING_METHOD</param-name>
       <param-value>server</param-value>
   </context-param>
   ```

---

#### **Настройка кодировки**

Для правильной работы с кодировкой (например, UTF-8) необходимо добавить параметр в `web.xml`:

```xml
<context-param>
    <param-name>javax.faces.DEFAULT_CHARSET</param-name>
    <param-value>UTF-8</param-value>
</context-param>
```

---

#### **Ссылки на полезные ресурсы**
1. **Документация JSF:**  
   [JSF Specification](https://jakarta.ee/specifications/faces/3.0/)  
2. **Руководство по PrimeFaces:**  
   [PrimeFaces Guide](https://www.primefaces.org/getting-started)  
3. **Примеры конфигурации:**  
   [Baeldung JSF Configuration](https://www.baeldung.com/jsf)  
4. **Официальный учебник JSF:**  
   [Oracle JSF Tutorial](https://docs.oracle.com/javaee/7/tutorial/jsf.htm)  

---

#### **Заключение**

Конфигурация JSF обеспечивает гибкость и модульность при разработке веб-приложений. Переход на аннотации и CDI значительно упрощает настройку. При грамотной настройке JSF предоставляет мощный инструментарий для создания масштабируемых UI.
