# Introducción y novedades de Jakarta EE 8

El objetivo del presente tutorial es brindar una demostración de algunas de las novedades más importantes de Java EE 8, especificamente:

* Retrocompatibilidad
* JAX-RS 2.1 Reactive client
* JSON Binding
* JSON-P (Processing y Patching)
* CDI 2.0 (Async events)

El tutorial esta dividido en 5 secciónes:

1. Configuración e instalación de un entorno de desarrollo Java EE 8
2. Creación y despliegue de una aplicación con Java EE 7
3. Actualización de la aplicación de Java EE 7 a Java EE 8
4. Implementación de nuevas caracteristicas en Java EE 8
5. Extensiones populares en Java EE 8 -e.g. DeltaSpike, Eclipse MicroProfile-

## Configuración e instalación de un entorno de desarrollo Java EE 8

TODO

## Creación y depliegue de una aplicación con JavaEE 7

Para la siguiente demostración crearemos una aplicación web utilizando Netbeans IDE 8.2 de acuerdo al siguiente esquema:

El objetivo de la aplicación es la elaboración de un backend para aplicaciones SPA (para fines demostrativos el siguiente repositorio contiene una aplicación en AngularJS: ), con la cual podremos crear un listado de peliculas favoritas, para agregar, modificar y eliminar peliculas (CRUD).

### Creando el proyecto

Como primer paso debemos agregar un nuevo proyecto de tipo **Web Application** para *Maven* web Java EE, por defecto NetBeans 8.2 genera una aplicación compatible con Java EE 7 en el perfil web, se incluyen screenshots de referencia de una nueva aplicacion denominada *jmovies* mediantes los cuales se 1- selecciona el proyecto, 2- se establecen los datos del proyecto en Java y 3- se selecciona el entorno de ejecución:

#### Selección de tipo de proyecto

![proy1](img/proy1.png)

#### Creación de proyecto

![proy2](img/proy2.png)

#### Selección de entorno de ejecución

![proy3](img/proy3.png)

Por ultimo, configuraremos nuestro proyecto para dar soporte a Java 8. Para esto, debemos abrir el archivo pom.xml en el cual se encuentran las definiciones de dependencias y tareas para la construcción de nuestro proyecto. Al abrirlo, debemos reemplazar las lineas:

```xml
<source>1.7</source>
<target>1.7</target>
```

Por

```xml
<source>1.8</source>
<target>1.8</target>
```

### Creando la capa de persistencia de datos

En esta ocasión utilizaremos una entidad en Java que representa una tabla relacional de base de datos, para esta demostracion utilizaremos [Apache H2](http://www.h2database.com/html/main.html), el cual se encuentra disponible en Payara 5.  

Como parte del estandard de Java EE, los servidores de aplicaciones deben implementar una base de datos relacional en memoria que permita elaborar pruebas, demostraciones y servir de soporte para procesos de integración continua. Sobre esta capa utilizaremos tres APIs fundamentales en las aplicaciones de JavaEE:

* JPA: ORM y mecanismo de mapeo desde bases de datos relacionales hacia el paradigma orientado a objetos
* EJB: Para la creación de componentes con una arquitectura orientada a servicios, incluyendo transaccionalidad, manejo de ciclo de vida e inyección de dependencias
* Bean Validation: Validaciones declarativas para la integridad de nuestra aplicación

#### Creando la entidad de persistencia

La primera vez que ejecutemos nuestra aplicación notaremos que no existe una estructura de base de datos y la misma sera creada en base a una entidad Java, abordaje denominado "code first".

Como primer paso debemos activar JPA en nuestro proyecto mediante la adición de un archivo de configuración estandard, especificamente *persistence.xml* mediante el cual crearemos una **unidad de persistencia** denominada `jmovies_PU`. Las unidades de persistencia son las encargadas de vincular la conexión hacia una base de datos relacional para poder utilizar el recurso mediante inyección de dependencias en código Java.

Para esto, debemos crear el archivo en `src/main/resources/META-INF/persistence.xml` con el siguiente contenido:

```xml
<?xml version="1.0" encoding="UTF-8"?><persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.1" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
  <persistence-unit name="jmovies_PU" transaction-type="JTA">
    <exclude-unlisted-classes>false</exclude-unlisted-classes>
    <properties>
      <property name="javax.persistence.schema-generation.database.action" value="drop-and-create"/>
      <property name="javax.persistence.schema-generation.scripts.action" value="drop-and-create"/>
      <property name="javax.persistence.schema-generation.scripts.create-target" value="jmoviesCreate.ddl"/>
      <property name="javax.persistence.schema-generation.scripts.drop-target" value="jmoviesDrop.ddl"/>
    </properties>
  </persistence-unit>
</persistence>
```

Notese que al utilizar la unidad de persistencia por defecto, no es necesario seleccionar un origen JTA.

Una vez lista la unidad de persistencia, agregaremos una nueva entidad en Java denominada `Movie` cuyas propiedades representan las columnas de una tabla en base de datos relacional, las anotaciones en Java representan metadatos que indican las restricciones que aplican para cada una de las columnas -e.g. sin nulos, tamaño maximo-.

* imdb(codigo): String
* nombre: String
* descripcion: String

![entity1](img/entity1.png)
![entity2](img/entity2.png)

```java
@Entity
@XmlRootElement
public class Movie implements Serializable {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id", updatable = false, nullable = false)
    private Long id;
    private static final long serialVersionUID = 1L;
    @Version
    @Column(name = "version")
    private int version;

    @Column
    @NotNull
    private String nombre;

    @Column
    @Size(max = 2000)
    private String descripcion;

    @Column
    @NotNull
    private String imdb;

    //Getters y setters . . .
```

Como referencia puede visualizar el estado del proyecto en el commit `c0caddc`.

#### Creando un Data Access Object/Repositorio de datos

Los Data Access Objects (DAO), son un patron comun en frameworks en Java, especialmente en Spring y Java EE, su objetivo es proporcionar un objeto en forma de repositorio de datos que permita realizar operaciones de altas, bajas y cambios de información, para utilizarlo como un servicio desde cualquier otro punto de la aplicación.

Para generar un DAO utilizaremos nuevamente NetBeans y crearemos un Session Bean de tipo Stateless como base. El bean nos garantiza la reutilización en memoria del componente y un manejo del ciclo de vida automatico del repositorio de datos. Posteriormente y a través de un EntityManager que representa nuestra unidad de persistencia, crearemos metodos para la creación, lectura y eliminación de datos, siendo `MovieDao` el responsable directo de comunicación con la base de datos relacional:

![dao1](img/dao1.png)
![dao2](img/dao2.png)

```java
@Stateless
public class MovieDao {
    @PersistenceContext(unitName = "jmovies_PU")
    private EntityManager em;

    public void create(Movie entity) {
        em.persist(entity);
    }

    public void deleteById(Long id) {
        Movie entity = em.find(Movie.class, id);
        if (entity != null) {
            em.remove(entity);
        }
    }

    public Movie findById(Long id) {
        return em.find(Movie.class, id);
    }

    public Movie update(Movie entity) {
        return em.merge(entity);
    }

    public List<Movie> listAll(Integer startPosition, Integer maxResult) {
        TypedQuery<Movie> findAllQuery = em.createQuery(
            "SELECT DISTINCT m FROM Movie m ORDER BY m.id", Movie.class);
        if (startPosition != null) {
            findAllQuery.setFirstResult(startPosition);
        }
        if (maxResult != null) {
            findAllQuery.setMaxResults(maxResult);
        }
    return findAllQuery.getResultList();
    }
}
```

Como referencia pueden visualizar el estado del proyecto en el commit `d1a2428`.

#### Creando un API REST

Para exponer la funcinalidad de nuesto backend crearemos un Endpoint en REST utilizando los verbos de HTTP para cada una de las operaciones de creación (POST), actualización (PUT), eliminación (DELETE) y consulta(GET). Para esto necesitaremos:

* Crear una clase activadora, donde definiremos la base de nuestra API en REST

```java
@ApplicationPath("/rest")
public class RestApplication extends Application {

}
```

* Crear una clase en Java denominada `MovieEndpoint`, donde definiremos los metodos de nuestro backend, notese que para la persistencia inyectamos como recurso nuestro EJB el cual administra toda la logica de persistencia de datos.

```java
@RequestScoped
@Path("/movies")
@Produces("application/json")
@Consumes("application/json")
public class MovieEndpoint {

    @EJB
    MovieDao movieDao;

    @POST
    public Response create(Movie movie) {
        movieDao.create(movie);
        return Response
                .created(UriBuilder.fromResource(MovieEndpoint.class).path(String.valueOf(movie.getId())).build())
                .build();
    }

    @GET
    @Path("/{id:[0-9][0-9]*}")
    public Response findById(@PathParam("id") final Long id) {

        Movie movie = movieDao.findById(id);
        if (movie == null) {
            return Response.status(Status.NOT_FOUND).build();
        }
        return Response.ok(movie).build();
    }

    @GET
    public List<Movie> listAll(@QueryParam("start") final Integer startPosition,
            @QueryParam("max") final Integer maxResult) {
        final List<Movie> movies = movieDao.listAll(startPosition, maxResult);
        return movies;
    }

    @PUT
    @Path("/{id:[0-9][0-9]*}")
    public Response update(@PathParam("id") Long id, Movie movie) {
        movie = movieDao.update(movie);
        return Response.ok(movie).build();
    }

    @DELETE
    @Path("/{id:[0-9][0-9]*}")
    public Response deleteById(@PathParam("id") final Long id) {
        movieDao.deleteById(id);
        return Response.noContent().build();
    }
}
```

Como referencia pueden visualizar el estado del proyecto en el commit `e61cba9`.

### Probando nuestra aplicación con Java EE 7

Al finalizar nuestra aplicación, debemos compilarla y desplegarla sobre el servidor de aplicaciones, para probar los metodos del API tenemos dos caminos

1. Utilizar un cliente REST ligero en un navegador web, por ejemplo uno de los clientes más populares en firefox es [RESTClient](https://addons.mozilla.org/en-US/firefox/addon/restclient/)
2. Utilizar un cliente independiente como [Postman](https://www.getpostman.com/)

Utilizaremos RESTClient para ejecutar las pruebas sobre el backend recien publicado, la URL padron para un servidor de aplicaciones nuevo es http://localhost:8080/jmovies. Asi mismo utilizaremos JSON como el formato de comunicación con el backend, utilizando la siguiente muestra de una pelicula con su código IMDB:

```json
{
"nombre":"The Matrix",
"imdb":"tt0133093",
"descripcion":"Ghost in the shell para gringos"
}
```

Primero debemos configurar el cliente para utilizar JSON como formato de comunicación:

![test71](img/test71.png)

Al ejecutar una primera consulta, notamos que la base de datos ha sido inicializada sin datos:

![test72](img/test72.png)

Prodecemos a insertar un primer dato utilizando POST:

![test73](img/test73.png)

Si lanzamos nuevamente una consulta GET, notaremos que los datos han sido insertados satisfactoriamente en la base de datos:

![test74](img/test74.png)

Luego podemos actualizar la entidad mediante PUT, notese que tanto el id como la versión deben coincidir para poder actualizar satisfactoriamente:

![test75](img/test75.png)

Observamos nuevamente que la actualizacion es satisfactoria:

![test76](img/test76.png)

Si todas las pruebas son satisfactorias hasta este punto, hemos creado con exito un backend compatible con cualquier framework SPA vue/angular/react/knockout/jet/etc. Para probar nuestro backend se ha preparado una aplicación con AngularJS en el [siguiente respositorio](), basta con que copiemos el contenido del directorio dentro de la carpeta *src/main/webapp*.

![spa](img/spa.png)

## Actualización de la aplicación desde Java EE 7 hacia Java EE 8

Nuevamente abrimos nuestro archivo pom.xml y buscamos la dependencia denominada javaee-api:

```xml
<dependency>
    <groupId>javax</groupId>
    <artifactId>javaee-api</artifactId>
    <version>7.0</version>
    <scope>provided</scope>
</dependency>
```

Reemplazamos la version

```xml
<dependency>
    <groupId>javax</groupId>
    <artifactId>javaee-api</artifactId>
    <version>8.0</version>
    <scope>provided</scope>
</dependency>
```

Listo, ya estamos en JavaEE 8, ¿Esperaban más? :). Commit de referencia `c01baf2`.

[Más información sobre la retrocompatibilidad garantizada de JavaEE](https://dzone.com/articles/testing-javaee-backward-and-forward-compatibility)

## Implementación de nuevas caracteristicas en Java EE 8

### JSON-B



```json
{
"nombre-pelicula":"The Matrix",
"imdb":"tt0133093",
"descripcion":"Ghost in the shell para gringos",
"precio-publico":"1050"
}
```

### JAX-RS Reactivo
### JSON-P Patch
### Servlet push
### Security

