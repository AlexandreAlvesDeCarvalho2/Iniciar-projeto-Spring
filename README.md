Creating the model (JPA Entity)

@AllArgsConstructor
@NoArgsConstructor
@Data
@Entity
public class Contact {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  private String name;
  private String email;
  private String phone;
}

AllArgsConstructor: automatically creates a class construtor with all arguments (properties).
NoArgsConstructor: automatically creates an empty class construtor with all arguments (properties).
Data: creates toString, equals, hashCode, getters and setters.


Creating the JPA Repository

@Repository
public interface ContactRepository 
    extends JpaRepository<Contact, Long> { }

Creating the Controller


@RestController
@RequestMapping({"/contacts"})
public class ContactController {

  private ContactRepository repository;

  ContactController(ContactRepository contactRepository) {
      this.repository = contactRepository;
  }

  // CRUD methods here
}



methods

@GetMapping
public List findAll(){
  return repository.findAll();
}


@GetMapping(path = {"/{id}"})
public ResponseEntity<Contact> findById(@PathVariable long id){
  return repository.findById(id)
          .map(record -> ResponseEntity.ok().body(record))
          .orElse(ResponseEntity.notFound().build());
}



@PostMapping
public Contact create(@RequestBody Contact contact){
    return repository.save(contact);
}


@PutMapping(value="/{id}")
  public ResponseEntity<Contact> update(@PathVariable("id") long id,
                                        @RequestBody Contact contact){
    return repository.findById(id)
        .map(record -> {
            record.setName(contact.getName());
            record.setEmail(contact.getEmail());
            record.setPhone(contact.getPhone());
            Contact updated = repository.save(record);
            return ResponseEntity.ok().body(updated);
        }).orElse(ResponseEntity.notFound().build());
  }


@DeleteMapping(path ={"/{id}"})
  public ResponseEntity<?> delete(@PathVariable("id") long id) {
    return repository.findById(id)
        .map(record -> {
            repository.deleteById(id);
            return ResponseEntity.ok().build();
        }).orElse(ResponseEntity.notFound().build());
  }
