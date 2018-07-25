**Inferencia de Tipo de Variable local para ejemplos de todos los tipos**

Este ejemplo es una clase llamada `Java10var` cuyos métodos deberían usar `Type Inference` para declarar variables que los usan en expresiones, declaraciones y bloques como bucles.

Realice los siguientes comandos para ver los contenidos de las respectivas clases de llama que están en la carpeta src:

    $ cat src/com/typeinference/Java10var.java

Compile los ejemplos con los siguientes comandos:

    $ javac -d target src/com/typeinference/Java10var.java 

Y ejecutamos el ejemplo con el siguiente comando:

    $ java -cp target com/typeinference/Java10var
    
Ahora, abra el archivo `com/typeinference/Java10var.java` y reemplace todos las declaraciones de datos de objetos con la nueva palabra llave `var`, este cambio le permite usar la Inferencia de Tipo de Variable.

    int id --> var id
    Integer myNumber --> var myNumber

Once you have replaced all primitive data types declaration, execute next command for compile the example:
Una vez que haya reemplazado toda la declaración de tipos de datos, ejecute el comando para compilar el ejemplo:

    $ javac -d target src/com/typeinference/Java10var.java 

**Nota:** La clase `Java10var` tiene algunas declaraciones con Inferencia de tipo de variable que no puede ser usado.
Abra el archivo `com/typeinference/Java10var.java` para identificar el uso de inferencia de tipo de variable que no están permitidos y cambielos para la declaración original. 

Una vez que el ejemplo compila, ejecútelo con el siguiente comando:

    $ java -cp target com/typeinference/Java10var
