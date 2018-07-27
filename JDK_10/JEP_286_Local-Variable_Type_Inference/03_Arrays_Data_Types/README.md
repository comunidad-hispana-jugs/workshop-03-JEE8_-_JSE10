**Local Variable Type Inference for Arrays Data Types example**
**Inferencia de Tipo de Variable local para ejemplos de tipo de datos arrays**

Este ejemplo es una clase llamada `ArraysTypes` cuyos métodos deberían usar `Type Inference` para declarar variables usandolo en expresiones, decaraciones y bloques como bucles.

Realice los siguientes comandos para ver los contenidos de las respectivas clases de Java que están en la carpeta src:

    $ cat src/com/typeinference/ArraysTypes.java

Compile el ejemplo con el siguiente comando:

    $ javac -d target src/com/typeinference/*

Y ejecutamos el ejemplo con el siguiente comando:

    $ java -cp target com/typeinference/ArraysTypes
    
Ahora, abra el archivo c`com/typeinference/ArraysTypes.java` y reemplace todos las declaraciones de datos primitivos con la nueva palabra llave `var`, este cambio le permite usar la Inferencia de Tipo de Variable.

    int[] myArray --> var myArray

Una vez que haya reemplazado toda la declaración de tipos de datos array, ejecute el comando para compilar y ejecutar. 
