Definición de la clase que hará de base de datos en memoria para los ejercicios a desarrollar

```java
/**
 * Clase que alojará los datos de las entidades en memoria 
 * 
 * @author Cristian Garay : <a href='cdaniel.py@gmail.com'>cdaniel.py@gmail.com</a>
 *
 */
public final class BaseDatos {

	static BaseDatos instancia;

	final Set<Alumno> mapAlumnos = new HashSet<>();
	final Set<Materia> mapMaterias = new HashSet<>();
	final Set<Aula> mapAulas = new HashSet<>();

	final AtomicLong oGeneraIDAlumnos = new AtomicLong();
	final AtomicLong oGeneraIDMaterias = new AtomicLong();
	final AtomicLong oGeneraIDAulas = new AtomicLong();

	private BaseDatos() {
		super();
	}

	public static BaseDatos getInstancia() {
		if (Objects.isNull(instancia))
			instancia = new BaseDatos();
		return instancia;
	}

	public Entidad<?> persistir(Entidad<?> entidad) throws Exception {

		// evaluamos el tipo de la instancia
		if (Alumno.class.isInstance(entidad)) {
			if (Objects.isNull(entidad.getId()))
				((Alumno) entidad).setId(oGeneraIDAlumnos.incrementAndGet());

			this.mapAlumnos.add((Alumno) entidad);

			return entidad;
		}

		if (Materia.class.isInstance(entidad)) {
			if (Objects.isNull(entidad.getId()))
				((Materia) entidad).setId(oGeneraIDMaterias.incrementAndGet());

			this.mapMaterias.add((Materia) entidad);

			return entidad;
		}

		if (Aula.class.isInstance(entidad)) {
			if (Objects.isNull(entidad.getId()))
				((Aula) entidad).setId(oGeneraIDAulas.incrementAndGet());

			this.mapAulas.add((Aula) entidad);

			return entidad;
		}

		throw new Exception("Tipo de instancia no persistible!");

	}

	public boolean remover(Entidad<?> entidad) throws Exception {

		// evaluamos el tipo de la instancia
		if (Alumno.class.isInstance(entidad))
			return this.mapAlumnos.remove((Alumno) entidad);

		if (Materia.class.isInstance(entidad))
			return this.mapMaterias.remove((Materia) entidad);

		if (Aula.class.isInstance(entidad))
			return this.mapAulas.remove((Aula) entidad);

		throw new Exception("Tipo de instancia no persistible!");

	}

	public List<?> listar(Class clase) throws Exception {

		// evaluamos la clase solicitada
		if (clase == Alumno.class)
			return this.mapAlumnos.stream().collect(Collectors.toList());

		if (clase == Materia.class)
			return this.mapMaterias.stream().collect(Collectors.toList());

		if (clase == Aula.class)
			return this.mapAulas.stream().collect(Collectors.toList());

		throw new Exception("Tipo de instancia no persistible!");

	}

	@SuppressWarnings({ "unchecked", "rawtypes" })
	public List<?> listar(Class<?> clase, Predicate predicado) throws Exception {

		// evaluamos la clase solicitada
		if (clase == Alumno.class)
			return (List<?>) this.mapAlumnos.stream().filter(predicado).collect(Collectors.toList());

		if (clase == Materia.class)
			return (List<?>) this.mapMaterias.stream().filter(predicado).collect(Collectors.toList());

		if (clase == Aula.class)
			return (List<?>) this.mapAulas.stream().filter(predicado).collect(Collectors.toList());

		throw new Exception("Tipo de instancia no persistible!");

	}

}

```
