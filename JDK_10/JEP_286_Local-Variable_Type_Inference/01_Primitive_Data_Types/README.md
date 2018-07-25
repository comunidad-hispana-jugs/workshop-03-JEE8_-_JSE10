**Inferencia de Tipo de Variable local para ejemplos de tipos de datos primitivos**

El primer ejemplo es una clase llamada `PrimitiveTypes` cuyos métodos deberían usar `Type Inference` para declarar variables usandolo en expresiones, decaraciones y bloques como bucles.

Realice los siguientes comandos para ver los contenidos de las respectivas clases de Java que están en la carpeta src:

    $ cat src/com/typeinference/PrimitiveTypes.java

Compile el ejemplo con el siguiente comando:

    $ javac -d target src/com/typeinference/PrimitiveTypes.java 

Y ejecutamos el ejemplo con el siguiente comando:

    $ java -cp target com/typeinference/PrimitiveTypes

Ahora, abra el archivo `com/typeinference/PrimitiveTypes.java` y reemplace todos las declaraciones de datos primitivos con la nueva palabra llave `var`, este cambio le permite usar la Inferencia de Tipo de Variable.

    int id --> var id

Una vez que haya reemplazado toda la declaración de tipos de datos primitivos, ejecute el comando para compilar y ejecutar. Por favor compare las salidas, ¿son lo mismo?
