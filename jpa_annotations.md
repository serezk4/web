### **JPA: Основные Аннотации**

JPA (Java Persistence API) предоставляет широкий набор аннотаций для работы с объектно-реляционным отображением (ORM). Эти аннотации помогают описывать сущности, маппинг полей на колонки, связи между сущностями, стратегии генерации ключей и многое другое.

---

### **1. Аннотации для определения сущностей**

#### **@Entity**
- Указывает, что класс является сущностью и будет маппиться на таблицу в базе данных.
- Необходима как минимум одна аннотация `@Id` для идентификатора.

**Пример:**
```java
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class User {
    @Id
    private Long id;
    private String name;
}
```

---

#### **@Table**
- Используется для указания имени таблицы, на которую маппится сущность.
- Опциональна; если не указана, имя таблицы будет совпадать с именем класса.

**Пример:**
```java
import jakarta.persistence.Entity;
import jakarta.persistence.Table;

@Entity
@Table(name = "users")
public class User {
    @Id
    private Long id;
    private String name;
}
```

---

### **2. Аннотации для идентификаторов**

#### **@Id**
- Указывает поле как первичный ключ.

#### **@GeneratedValue**
- Определяет стратегию генерации значения для идентификатора.
- Параметр `strategy` может принимать следующие значения:
  - `GenerationType.AUTO` — выбирается автоматически.
  - `GenerationType.IDENTITY` — использует автоинкремент.
  - `GenerationType.SEQUENCE` — использует последовательность.
  - `GenerationType.TABLE` — использует специальную таблицу.

**Пример:**
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

---

#### **@SequenceGenerator**
- Используется для создания последовательностей (только с `GenerationType.SEQUENCE`).

**Пример:**
```java
@SequenceGenerator(name = "user_seq", sequenceName = "user_sequence", allocationSize = 1)
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
private Long id;
```

---

### **3. Аннотации для полей и колонок**

#### **@Column**
- Используется для настройки маппинга поля на колонку.
- Атрибуты:
  - `name` — имя колонки.
  - `nullable` — допускает ли колонка `NULL`.
  - `unique` — является ли колонка уникальной.
  - `length` — максимальная длина для строк.

**Пример:**
```java
@Column(name = "user_name", nullable = false, unique = true, length = 50)
private String name;
```

---

#### **@Transient**
- Указывает, что поле не должно быть сохранено в базе данных.

**Пример:**
```java
@Transient
private String temporaryData;
```

---

#### **@Lob**
- Указывает, что поле должно быть сохранено как большой объект (Large Object), например `CLOB` или `BLOB`.

**Пример:**
```java
@Lob
private String largeText;
```

---

#### **@Temporal**
- Используется для работы с датами (`java.util.Date` или `java.util.Calendar`).
- Типы:
  - `TemporalType.DATE` — только дата.
  - `TemporalType.TIME` — только время.
  - `TemporalType.TIMESTAMP` — дата и время.

**Пример:**
```java
@Temporal(TemporalType.TIMESTAMP)
private Date createdAt;
```

---

#### **@Enumerated**
- Используется для работы с `enum`.
- Типы:
  - `EnumType.ORDINAL` — сохраняет порядковый номер.
  - `EnumType.STRING` — сохраняет строковое представление.

**Пример:**
```java
@Enumerated(EnumType.STRING)
private Status status;
```

---

### **4. Аннотации для связей между сущностями**

#### **@OneToOne**
- Отображает связь "один к одному".
- Параметр `mappedBy` указывает поле, которое управляет связью.

**Пример:**
```java
@OneToOne
@JoinColumn(name = "profile_id")
private Profile profile;
```

---

#### **@OneToMany**
- Отображает связь "один ко многим".
- Используется с `mappedBy` для указания управляющего поля.

**Пример:**
```java
@OneToMany(mappedBy = "user")
private List<Order> orders;
```

---

#### **@ManyToOne**
- Отображает связь "многие к одному".

**Пример:**
```java
@ManyToOne
@JoinColumn(name = "user_id")
private User user;
```

---

#### **@ManyToMany**
- Отображает связь "многие ко многим".
- Использует промежуточную таблицу, которая задаётся аннотацией `@JoinTable`.

**Пример:**
```java
@ManyToMany
@JoinTable(
    name = "user_roles",
    joinColumns = @JoinColumn(name = "user_id"),
    inverseJoinColumns = @JoinColumn(name = "role_id")
)
private Set<Role> roles;
```

---

### **5. Аннотации для запросов**

#### **@NamedQuery**
- Используется для создания именованных JPQL-запросов.

**Пример:**
```java
@NamedQuery(name = "User.findByName", query = "SELECT u FROM User u WHERE u.name = :name")
```

---

#### **@NamedNativeQuery**
- Создаёт именованный SQL-запрос.

**Пример:**
```java
@NamedNativeQuery(name = "User.findAll", query = "SELECT * FROM users", resultClass = User.class)
```

---

#### **@Query**
- Используется для определения запросов в Spring Data JPA.

**Пример:**
```java
@Query("SELECT u FROM User u WHERE u.email = :email")
User findByEmail(@Param("email") String email);
```

---

### **6. Другие аннотации**

#### **@Embedded**
- Используется для вложения другого объекта как части сущности.

**Пример:**
```java
@Embedded
private Address address;
```

#### **@Embeddable**
- Указывает, что класс может быть встраиваемым.

**Пример:**
```java
@Embeddable
public class Address {
    private String city;
    private String street;
}
```

---

#### **@Version**
- Используется для оптимистичной блокировки записей.

**Пример:**
```java
@Version
private int version;
```

---

### **Полезные ресурсы**

1. **Официальная спецификация JPA:**
   [Jakarta Persistence Specification](https://jakarta.ee/specifications/persistence/)

2. **Hibernate Documentation:**
   [Hibernate Documentation](https://hibernate.org/documentation/)

3. **Baeldung JPA Guide:**
   [Baeldung JPA Annotations](https://www.baeldung.com/jpa)

---

### **Заключение**

JPA предоставляет мощный набор аннотаций для описания сущностей, маппинга, работы с отношениями и выполнения запросов. Правильное понимание и использование этих аннотаций позволяет эффективно разрабатывать приложения с использованием объектно-реляционного подхода.
