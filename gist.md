# Spring Repositories in Spring Data

---

## What is Spring Data?

- Built **on top** of Hibernate and the JPA
- Supplies **repositories** as a means to save us from writing JPQL

---

## What is a Spring Repository?

- An **interface** that uses convention over configuration to implement queries
- Repository implementations are created at runtime
- Thanks to Dependency Injection and the IoC Container, we don't have to write the class or any JPQL out of the gates

---

## Example Repository

```java
@Repository
public interface ContactRepository extends CrudRepository<Contact, Long> {
  public Contact findByEmailAddress(String email);
}
```

- The interface extends CrudRepository with generic.
- The first defines what entity we're manipulating.
- The second defines the data type we use to uniquely identify each entity.

---

### Seeding Data

```java
@Component
public class ContactSeeder implements CommandLineRunner {
  @Autowired
  private ContactRepository repo;

  @Override
  public void run(String... args) throws Exception {
    //seeding code here
  }
}
```

- We use @Component to tell Spring Boot that we want this to be automatically added to the IoC container. 
- Implements CommandLineRunner so that it will be run once at startup.

---

### Seeding Routine

```java
List<Contact> contacts = new ArrayList<Contact>();

Contact jonSnow = new Contact();
jonSnow.setFirstName("Jon");
jonSnow.setLastName("Snow");
jonSnow.setEmailAddress("jon@example.com");
jonSnow.setPurchaseTotal(50);
contacts.add(jonSnow);

for(Contact contact : contacts) {
  if(repo.findByEmailAddress(contact.getEmailAddress()) == null) {
    repo.save(contact);
  }
}
```

We use our repository object to loop through a list of email addresses. If the emails are not found, the list will be added to the database table as it iterates through.

---

### Usage in a Controller

```java
@Controller
@RequestMapping("/contacts")
public class ContactsController {
  @Autowired
  private ContactRepository contactRepo;

  @GetMapping
  public String getList(Model model) {
    List<Contact> contacts = (List<Contact>)contactRepo.findAll();
    model.addAttribute("contacts", contacts);
    return "contacts/index";
  }
}
```

---

### Dynamic Queries

```java
@Repository
public interface ContactRepository extends CrudRepository<Contact, Long> {
  public Contact findByEmailAddress(String email);
  public List<Contact> findByPurchaseTotalGreaterThan(Integer purchaseAmount);
}
```

---

### Dynamic Query Reference

https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation
