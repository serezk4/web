### **JSF: Конверторы и валидаторы**

**Конверторы (Converters)** и **валидаторы (Validators)** в JSF — это инструменты для работы с пользовательским вводом. Они позволяют преобразовывать значения между строками (String) и объектами, а также проверять корректность введённых данных.

---

### **Конверторы (Converters)**

#### **Назначение**
- **Конверторы** преобразуют значения между строковым представлением (например, текст в HTML-форме) и объектами Java.
- Они работают на уровне UI-компонентов, таких как текстовые поля и списки.

---

#### **Стандартные конверторы в JSF**
JSF предоставляет ряд встроенных конверторов:
1. **NumberConverter** (`javax.faces.convert.NumberConverter`) — преобразует числа.
   ```xml
   <h:inputText value="#{bean.value}">
       <f:convertNumber minFractionDigits="2" maxFractionDigits="2" />
   </h:inputText>
   ```
2. **DateTimeConverter** (`javax.faces.convert.DateTimeConverter`) — преобразует даты и время.
   ```xml
   <h:inputText value="#{bean.date}">
       <f:convertDateTime pattern="yyyy-MM-dd" />
   </h:inputText>
   ```

---

#### **Создание пользовательского конвертора**

1. **Класс конвертора:**
   ```java
   import jakarta.faces.component.UIComponent;
   import jakarta.faces.context.FacesContext;
   import jakarta.faces.convert.Converter;
   import jakarta.faces.convert.FacesConverter;

   @FacesConverter("customConverter")
   public class CustomConverter implements Converter {
       @Override
       public Object getAsObject(FacesContext context, UIComponent component, String value) {
           return value != null ? value.toUpperCase() : null;
       }

       @Override
       public String getAsString(FacesContext context, UIComponent component, Object value) {
           return value != null ? value.toString().toLowerCase() : "";
       }
   }
   ```

2. **Использование в XHTML:**
   ```xml
   <h:inputText value="#{bean.text}">
       <f:converter converterId="customConverter" />
   </h:inputText>
   ```

---

#### **Пример использования конвертора**
**Пример: Преобразование идентификатора объекта.**

1. **XHTML:**
   ```xml
   <h:selectOneMenu value="#{bean.selectedUser}">
       <f:converter converterId="userConverter" />
       <f:selectItems value="#{bean.users}" var="user" itemValue="#{user}" itemLabel="#{user.name}" />
   </h:selectOneMenu>
   ```

2. **Конвертор:**
   ```java
   @FacesConverter("userConverter")
   public class UserConverter implements Converter {
       @Override
       public Object getAsObject(FacesContext context, UIComponent component, String value) {
           return Database.findUserById(Integer.parseInt(value));
       }

       @Override
       public String getAsString(FacesContext context, UIComponent component, Object value) {
           return value != null ? String.valueOf(((User) value).getId()) : "";
       }
   }
   ```

---

### **Валидаторы (Validators)**

#### **Назначение**
- **Валидаторы** проверяют корректность пользовательского ввода.
- Работают на уровне UI-компонентов и вызываются на этапе **Process Validations**.

---

#### **Стандартные валидаторы в JSF**
JSF предоставляет встроенные валидаторы:
1. **LengthValidator:** Проверяет длину текста.
   ```xml
   <h:inputText value="#{bean.name}">
       <f:validateLength minimum="3" maximum="10" />
   </h:inputText>
   ```
2. **RegexValidator:** Проверяет значение с использованием регулярных выражений.
   ```xml
   <h:inputText value="#{bean.email}">
       <f:validateRegex pattern="^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$" />
   </h:inputText>
   ```

---

#### **Создание пользовательского валидатора**

1. **Класс валидатора:**
   ```java
   import jakarta.faces.application.FacesMessage;
   import jakarta.faces.component.UIComponent;
   import jakarta.faces.context.FacesContext;
   import jakarta.faces.validator.FacesValidator;
   import jakarta.faces.validator.Validator;
   import jakarta.faces.validator.ValidatorException;

   @FacesValidator("customValidator")
   public class CustomValidator implements Validator {
       @Override
       public void validate(FacesContext context, UIComponent component, Object value) throws ValidatorException {
           String input = (String) value;
           if (input == null || !input.matches("[a-zA-Z]+")) {
               throw new ValidatorException(new FacesMessage(FacesMessage.SEVERITY_ERROR, 
                       "Validation Error", "Only letters are allowed!"));
           }
       }
   }
   ```

2. **Использование в XHTML:**
   ```xml
   <h:inputText value="#{bean.text}">
       <f:validator validatorId="customValidator" />
   </h:inputText>
   ```

---

#### **Пример использования валидатора**
**Пример: Проверка возраста пользователя.**

1. **XHTML:**
   ```xml
   <h:inputText value="#{bean.age}">
       <f:validator validatorId="ageValidator" />
   </h:inputText>
   ```

2. **Валидатор:**
   ```java
   @FacesValidator("ageValidator")
   public class AgeValidator implements Validator {
       @Override
       public void validate(FacesContext context, UIComponent component, Object value) throws ValidatorException {
           Integer age = (Integer) value;
           if (age < 18 || age > 100) {
               throw new ValidatorException(new FacesMessage("Age must be between 18 and 100."));
           }
       }
   }
   ```

---

### **Комбинированное использование конверторов и валидаторов**

Конверторы и валидаторы могут использоваться одновременно для обработки одного поля.

1. **XHTML:**
   ```xml
   <h:inputText value="#{bean.userInput}">
       <f:converter converterId="customConverter" />
       <f:validator validatorId="customValidator" />
   </h:inputText>
   ```

2. **Процесс:**
   - Значение сначала преобразуется с помощью конвертора.
   - Затем преобразованное значение проходит проверку валидатором.

---

### **Сообщения об ошибках**

Если валидатор обнаруживает ошибку, JSF может отображать сообщение об ошибке:

```xml
<h:inputText id="name" value="#{bean.name}" required="true">
    <f:validateLength minimum="3" maximum="10" />
</h:inputText>
<h:message for="name" />
```

---

### **Полезные ссылки**
1. **Официальная документация JSF:**
   - [Java EE Converters and Validators](https://docs.oracle.com/javaee/7/tutorial/jsf-converters.htm)
2. **Примеры от Baeldung:**
   - [Baeldung JSF Validation](https://www.baeldung.com/jsf-validation)
3. **PrimeFaces Input Validation:**
   - [PrimeFaces Documentation](https://www.primefaces.org/documentation)

---

### **Заключение**

Конверторы и валидаторы — важная часть JSF-приложений, обеспечивающая корректность пользовательских данных и их преобразование для работы с бизнес-логикой. Использование встроенных инструментов или создание собственных позволяет гибко настроить поведение интерфейса и повысить удобство для пользователей.
