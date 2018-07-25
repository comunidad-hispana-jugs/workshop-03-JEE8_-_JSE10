**Inferencia de tipo de variable local para ejemplos de tipos de datos de objetos**

Este ejemplo es una clase llamada `ObjectTypes` cuyos métodos deberían usar
`Type Inference` para declarar variables que los usan en expresiones, declaraciones y bloques como bucles.


Realice los siguientes comandos para ver los contenidos de las respectivas clases de llama que están en la carpeta src:

    $ cat src/com/typeinference/ObjectTypes.java

Compile los ejemplos con los siguientes comandos:

    $ javac -d target src/com/typeinference/*

Y ejecutamos el ejemplo con el siguiente comando:

    $ java -cp target com/typeinference/ObjectTypes
    
Ahora, abra el archivo `com/typeinference/ObjectTypes.java` y reemplace todos las declaraciones de datos de objetos con la nueva palabra llave `var`, este cambio le permite usar la Inferencia de Tipo de Variable.

    String id --> var id
    Book theLordOfTheRings --> var theLordOfTheRings
 
**Nota:** La clase `ObjectTypes.java` usa objetos definidos por el usuario `(Book, Store)` y por el JDK `(String, Integer, List )` 

Una vez que haya reemplazado toda la declaración de tipos de datos de objetos, ejecute el comando para compilar y ejecutar. Por favor compare las salidas, ¿son lo mismo?
