Métodos a agregar a la clase `MainController` para poder realizar ABM y Listado de datos de la clase `Alumno`.

```java
@GetMapping("/alumnos")
public ResponseEntity<List<Alumno>> getAlumnos() {
  try {

    return new ResponseEntity<>((List<Alumno>) this.bd.listar(Alumno.class), HttpStatus.FOUND);

  } catch (Exception e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
    return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
  }
}

@GetMapping("/alumnos/{documento}")
public ResponseEntity<Alumno> getAlumno(@PathVariable long documento) {
  try {
    val alumnos = (List<Alumno>) this.bd.listar(Alumno.class, a -> ((Alumno) a).getDocumento() == documento);
    if (Objects.nonNull(alumnos) && !alumnos.isEmpty())
      return new ResponseEntity<>(alumnos.get(0), HttpStatus.FOUND);

    return new ResponseEntity<>(null, HttpStatus.NOT_FOUND);

  } catch (Exception e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
    return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
  }
}

@PostMapping("/alumnos")
public ResponseEntity<Alumno> postAlumno(@RequestBody Alumno alumno) {
  try {
    alumno = (Alumno) this.bd.persistir(alumno);
    return new ResponseEntity<>(alumno, HttpStatus.FOUND);

  } catch (Exception e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
    return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
  }
}

@PutMapping("/alumnos")
public ResponseEntity<Alumno> putAlumno(@RequestBody final Alumno alumno) {
  try {

    val l = bd.listar(Alumno.class, a -> ((Alumno) a).getDocumento() == alumno.getDocumento());

    if (l.isEmpty())
      return new ResponseEntity<>(null, HttpStatus.NOT_FOUND);

    val editando = (Alumno) l.get(0);
    editando.setNombre(alumno.getNombre());
    editando.setActivo(alumno.isActivo());

    this.bd.persistir(editando);

    return new ResponseEntity<>(editando, HttpStatus.FOUND);

  } catch (Exception e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
    return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
  }
}

@DeleteMapping("/alumnos/{id}")
public ResponseEntity<Alumno> deleteAlumno(@PathVariable long documento) {
  try {

    if (bd.listar(Alumno.class, a -> ((Alumno) a).getDocumento() == documento).isEmpty())
      return new ResponseEntity<>(null, HttpStatus.NOT_FOUND);

    val alumno = Alumno.builder().documento(documento).build();

    this.bd.remover(alumno);

    val l = bd.listar(Alumno.class, a -> ((Alumno) a).getDocumento() == documento);

    if (l.isEmpty())
      return new ResponseEntity<>(null, HttpStatus.OK);

    return new ResponseEntity<>(null, HttpStatus.NOT_MODIFIED);

  } catch (Exception e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
    return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
  }
}
```

