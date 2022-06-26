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

Buscar

    @GetMapping
    public List findAll(){
      return repository.findAll();
    }

Buscar por id 

    @GetMapping(path = {"/{id}"})
    public ResponseEntity<Contact> findById(@PathVariable long id){
      return repository.findById(id)
              .map(record -> ResponseEntity.ok().body(record))
              .orElse(ResponseEntity.notFound().build());
    }

adicionar

    @PostMapping
    public Contact create(@RequestBody Contact contact){
        return repository.save(contact);
    }

editar

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

deletar

    @DeleteMapping(path ={"/{id}"})
      public ResponseEntity<?> delete(@PathVariable("id") long id) {
        return repository.findById(id)
            .map(record -> {
                repository.deleteById(id);
                return ResponseEntity.ok().build();
            }).orElse(ResponseEntity.notFound().build());
      }
      
      
-=-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-==-=--=-=
Até aqui já funciona o código, caso queira implementar o DTO e Service siga os proximos passos.

Criar Classe DTO, colocar somente os atributos que irá passar ao usuário.

    @AllArgsConstructor
    @NoArgsConstructor
    @Data
    public class UsuarioDTO {

        private Long id;
        private String name;
        private String email;


        public UsuarioDTO(Usuario usuario){
            id = usuario.getId();
            name = usuario.getName();
            email = usuario.getEmail();        
        }

    }

Criar Classe Service, para converter a entidade em DTO. 

    @Service
    public class UsuarioService {

        @Autowired
        private UsuarioRepository repository;

        public List<UsuarioDTO> buscarTodosDto(){
            return repository.findAll()
            .stream()
            .map(this::convertEntityToDto)
            .collect(Collectors.toList());

        }
           private UsuarioDTO convertEntityToDto(Usuario usuario){
                UsuarioDTO usuarioDTO = new UsuarioDTO();
                usuarioDTO.setId(usuario.getId());
                usuarioDTO.setName(usuario.getName());
                usuarioDTO.setEmail(usuario.getEmail());

           return usuarioDTO;
       }

modificar o metodo do controlador 

    @GetMapping
    public List<UsuarioDTO> buscarTodosDTO(){
        return serviceRepository.buscarTodosDto();
    }







Alexandre Alves de Carvalho
