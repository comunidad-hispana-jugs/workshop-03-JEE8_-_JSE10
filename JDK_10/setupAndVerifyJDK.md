## Descargar, instalar y verificar el JDK

#### Descargar, instalar y verificar el JDK 10

- Descargar JDK 10
  
  - JDK 10 General-Availability Release está disponible en [http://jdk.java.net/10/](http://jdk.java.net/10/)

- Instalar JDK 10 a partir de archivos tar.gz 
  
  - Desempaquete el archivo
    - Linux - MacOSX: `tar -xvf jdk-10*.tar`
    - Windows: utilice alguna herramienta como 7zip, Winzip or Winrar
         
  
  - Configure el JAVA_HOME, para efectos de los ejercicios recomendamos configurar el JAVA_HOME para ser usado de manera local en una consola o terminal, esto  con el fin de no afectar
  la configuración del equipo

    **Linux and MacOSX users only:** 

       -  `export JAVA_HOME=[jdk destination]`

           `[destination]` usualmente es `.../jdk-10.jdk/Contents/Home/` o un directorio similar
                  
    **Windows users:** 
    
       -  `set JAVA_HOME=[jdk destination]`

           `[destination]` usualmente es `.../jdk-10.jdk/Contents/Home/`  
       
       -  `set PATH=%JAVA_HOME%\bin`

           `Estás dos configuraciones se guardarán tempralmente en la consola o terminal. `
             
       - Si el paso anterior no le funciono, edite `JAVA_HOME` y `PATH` en las variables de entorno a través de `My Computer` > `Properties` 
         - `JAVA_HOME`: `JAVA_HOME=[jdk destination]`
         - `PATH`: `PATH=%JAVA_HOME%/bin;%PATH%`
         
           `[destination]` usualmente es `C:\Program Files\Java\...` o un directorio similar  

##### Verificar la instalación del JDK

###### java

Después de descargar e instalar el JDK 10, ejecute los siguientes comandos:

```
    $ java -version
```

Como salida debe tener una respuesta similar a la siguiente:

```
    openjdk version "10" 2018-03-20
    OpenJDK Runtime Environment 18.3 (build 10+46)
    OpenJDK 64-Bit Server VM 18.3 (build 10+46, mixed mode)
```

###### javac

```
    $ javac -version
```

Como salida debe tener una respuesta similar a la siguiente:

```
    javac 10
```

###### jlink

Verifique si `jlink` está disponible:

```
    $ jlink --version
```

Como salida debe tener una respuesta similar a la siguiente:

```
    10
```

###### jshell

Verifique si `jshell` está disponible:

```
    $JAVA_HOME/bin/jshell -version
```

Como salida debe tener una respuesta similar a la siguiente:

```
    jshell 10
```
