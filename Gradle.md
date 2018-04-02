# Tutorial sobre Gradle

**Crear una tarea**  

~~~
task nombre_tarea << {
  //Instrucciones
}
~~~

**Ejecutar tarea**

~~~
gradle -q nombre_tarea
~~~

La opción `-q` es para que no imprima información innecesaria.

**Mostrar todas las tareas disponibles**

~~~
gradle -q tasks --all
~~~

**Uso de variables**

~~~
task variables{
  ext{
    nombre_propiedad1="Valor"
    nombre_propiedad2="Valor2"
  }
}

task tarea << {
  println variables.nombre_propiedad1
}
~~~

**Ejecutar al final de una tarea**

~~~
task tarea{
  doLast{
    println 'Finalizada tarea'
  }
}
~~~

**Estructura de un proyecto en Gradle**

Por compatibildad usa la misma que Maven.

~~~
src
  |--main
  |   |--java
  |   |--kotlin
  |
  |--test
      |--java
      |--kotlin 
~~~

**Ejecutar desde Gradle**

Usar el plugin de Java (o Kotlin)
~~~
apply plugin: 'java'
~~~
EL plugin ya viene con una serie de tareas tales como javadoc, jar, clean entre otras.

**Compatibilidad con JVM**
Indicar la version compatible del jar creado (poner dentro del fichero build.gradle)
~~~
sourceCompatibility = '1.8'
~~~

**Indicar versión del código**
~~~
version = '1.0'
~~~

**Modificar el manifest**
Para modificar el archivo manifest que se crea al ejecutar el comando build, hay que usar el siguinte código:

~~~
jar {
  manifest {
    attributes 'Main-Class': 'ClaseConMetodoMain',
               'OtraPropiedad': 'OtroValor'
  }
}
~~~

Para ejecutar el jar desde comando basta con ejecutar:

~~~
java -jar MiJar.jar
~~~

**Ejecutar la aplicación**
Usar el plugin *application* y sobreescribir la propiedad *mainClassName*. El plugin agrega varias tareas, entre otras añade *run*, que ejecuta la clase principal (y la compila si fuera necesaria).

~~~
apply plugin: 'application'

mainClassName = 'ClaseConMetodoMain'
~~~

**Dependencia entre tareas**
Para la correcta ejecución de algunas tareas, a veces puede ser necesario que antes se ejecute otra.

~~~
task compilar << {
  println 'Estoy compilando'
}

task ejecutar(dependsOn: 'compilar') << {
  println 'Estoy ejecutando'
}
~~~

**Comprobar si una tarea ya se a ejecutado**

Todas las tareas tiene el método *didWork* que devuelve `true` si ya ha acabado la terea, devuelve false en caso contrario.

**Ejecución condicional de una tarea**

~~~
task miTarea {
  onlyIf{
    //Booleanos
    otraTarea.didWork
    //Comprobar si existe un archivo
    new File('path/file').exists()
  }

  //Resto de código de la tarea
}
~~~

**Ejecutar una tarea al finalizar otra**

Ejecuta `tarea2` al finalizar `tarea1`

~~~
tarea1.finalizedBy tarea2
~~~

**Tarea copiar**

~~~
task crearDist << {
  //Crea un directorio llamado 'dist'
  new File('dist).mkdirs()
}

task copiar(type: Copy, dependsOn: crearDist) {
  from 'tools' //Carpeta origen
  into 'dist/tools' //Carpeta destino

  //Expresion regular sobre los archivos a copiar
  include 'regexp'

  //Expresion regular sobre los archivos a NO copiar
  exclude 'regexp'
}
~~~

**Tarea ejecutar**

Permite ejecutar comandos. Si se sobreescribe la propiedad *standarOutput*, se puede redirigir la salida estandar. Tambien existe una propiedad *standarInput*, análoga para la entrada estandar.

~~~
task ejecutar(type: Exec){
  commandLine 'comando','opción1','opción2'

  //Redirige la salida a fichero
  standarOutput = new FileOutputStream('salida.txt')
}
~~~

**Tarea comprimir**

Permite comprimir directorios en un archivo zip.

~~~
task comprimir(type:Zip) {
  from 'carpetaOrigen'
  archiveName 'destino.zip'
}
~~~