# Capítulo 14: Comandos externos

Dentro del sistema Unix, encontrarás muchos comandos pequeños y muy específicos donde cada uno hace una tarea y la hace eficientemente. Puedes encadenar estos comandos para que funcionen de manera conjunta para dar solución a un problema complejo. ¿No sería genial si pudieras utilizar estos comandos dentro de Vim?

Por supuesto que sí. En este capítulo, aprenderás cómo extender las funcionalidades de Vim para que trabaje sin problemas con estos comandos externos.

## El comando _bang_

Vim tiene un comando llamado _bang_ \(`!`\) que puede realizar tres cosas:

1. Leer la salida estándar \(STDOUT\) de un comando externo dentro del _buffer_ actual.
2. Escribir el contenido de tu _buffer_ como la entrada estándar \(STDIN\) a un comando externo.
3. Ejecutar un comando externo desde dentro de Vim.

Profundicemos más en detalle cada una de estas tareas.

## Leer la salida estándar \(STDOUT\) de un comando dentro de Vim

La sintaxis para leer la salida estándar \(STDOUT\) de un comando externo dentro del _buffer_ actual es:

```text
:r !cmd
```

`:r` es el comando de Vim para leer. Si lo utilizas sin `!`, puedes utilizarlo para obtener el contenido de un archivo. Si tienes un archivo llamado `archivo1.txt` en el directorio actual y ejecutas:

```text
:r archivo1.txt
```

Vim pondrá el contenido de `archivo1.txt` dentro del _buffer_ actual.

Si ejecutas el comando `:r` seguido por un `!` y un comando externo, la salida de ese comando será insertada dentro del _buffer_ actual. Para obtener el resultado del comando `ls`, ejecuta:

```text
:r !ls
```

Esto devolverá algo similar a esto:

```text
archivo1.txt
archivo2.txt
archivo3.txt
```

Puedes leer los datos desde el comando `curl`:

```text
:r !curl -s 'https://jsonplaceholder.typicode.com/todos/1'
```

El comando `r` también acepta una dirección:

```text
:10r !cat archivo1.txt
```

Ahora la salida estándar \(STDOUT\) de ejecutar `cat archivo1.txt` será insertada después de la línea 10.

## Escribir el contenido de un _buffer_ en un comando externo

Además de para guardar un archivo, también puedes utilizar el comando de escribir \(`:w`\) para pasar el contenido del _buffer_ actual como la entrada estándar \(STDIN\) a un comando externo. La sintaxis es:

```text
:w !cmd
```

Si tienes las siguientes expresiones en el _buffer_:

```text
console.log("Hola Vim");
console.log("Vim es asombroso");
```

Asegúrate de tener [node](https://nodejs.org/en/) instalado en tu equipo y después ejecuta:

```text
:w !node
```

Vim usará `node` para ejecutar las expresiones de Javascript para mostrar por pantalla "Hola Vim" y "Vim is asombroso".

Al utilizar el comando `:w`, Vim utiliza todo el texto del _buffer_ actual, de manera similar al comando global \(la mayoría de comandos de la línea de comandos, si no les pasas un rango, solo ejecutan el comando en la línea actual, no en todo el _buffer_\). Si le pasas al comando `:w` una dirección específica:

```text
:2w !node
```

Vim solo utilizará el texto de la segunda línea en el intérprete de `node`.

Existe una sutil pero significativa diferencia entre `:w !node` y `:w! node`. Con `:w !node`, estás "escribiendo" el texto del _buffer_ actual en un comando externo, en este caso `node`. Con `:w! node`, estás forzando a guardar un archivo y dándole el nombre de "node".

## Ejecutando un comando externo

Puedes ejecutar un comando externo desde dentro de Vim con el comando _bang_. La sintaxis es:

```text
:!cmd
```

Para ver el contenido del directorio actual en su formato extendido, ejecuta:

```text
:!ls -ls
```

Para matar un proceso que se está ejecutando con el indicador del proceso PID 3456, puedes ejecutar:

```text
:!kill -9 3456
```

Puedes ejecutar cualquier comando externo sin salir de Vim y así permanecer centrado en tu tarea.

## Filtrando textos

Si das un rango al comando `!`, esto puede ser utilizado para filtrar textos. Supongamos que tenemos estos textos:

```text
hello vim
hello vim
```

Con el cursor en la primera línea, vamos a convertir a mayúsculas la línea actual utilizando el comando `tr` \(translate\). Ejecuta:

```text
:.!tr '[:lower:]' '[:upper:]'
```

Este es el resultado:

```text
HELLO VIM
hello vim
```

Vamos a ver en detalle el comando:

* `.!` ejecuta el filtro del comando en la línea actual.
* `tr '[:lower:]' '[:upper:]'` llama al comando `tr` para reemplazar los caracteres de minúsculas a mayúsculas.

Es imperativo pasar un rango para ejecutar el comando externo como un filtro. Si tratas de ejecutar el comando anterior sin el `.` \(`:!tr '[:lower:]' '[:upper:]'`\), verás un error.

Asumamos ahora que necesitamos eliminar la segunda columna de ambas líneas utilizando el comando `awk`:

```text
:%!awk "{print $1}"
```

Este es el resultado:

```text
hello
hello
```

La explicación:

* `:%!` ejecuta el filtro del comando en todas las líneas \(`%`\).
* `awk "{print $1}"` imprime solo la primera columna.

Puedes encadenar múltiples comandos con el operador `|` de igual manera que lo haces en la terminal. Supongamos que tenemos un archivo con estos elementos de un delicioso desayuno:

```text
nombre precio
Tarta chocolate 10
Tarta merengue 9
Tarta frambuesa 12
```

Si necesitas ordenar la lista en base al precio y mostrar solo el menú incluso con los espacios, puedes ejecutar:

```text
:%!awk 'NR > 1' | sort -nk 3 | column -t
```

El resultado:

```text
Tarta merengue 9
Tarta chocolate 10
Tarta frambuesa 12
```

La explicación:

* `:%!` aplica el filtro a todas las líneas \(`%`\).
* `awk 'NR > 1'` muestra los texto solo desde la columna 2 en adelante.
* `|` encadena esto con el siguiente comando.
* `sort -nk 3` ordena numéricamente \(`n`\) utilizando los valores de la columna 3 \(`k 3`\).
* `column -t` organiza el texto con los espaciados.

## Comando del modo normal

Vim tiene un filtro de operador \(`!`\) en el modo normal. Si tienes los siguientes saludos:

```text
hello vim
hola vim
bonjour vim
salve vim
```

Para convertir en mayúsculas la línea actual y la línea inferior, puedes ejecutar:

```text
!jtr '[a-z]' '[A-Z]'
```

La explicación:

* `!j` ejecuta el filtro operador del comando normal \(`!`\) en la línea actual y la línea inferior. Recuerda que es un operador del modo normal, la regla gramática de `verbo + sustantivo` se aplica en este caso. 
* `tr '[a-z]' '[A-Z]'` reemplaza las letras minúsculas con letras mayúsculas.

El filtro del comando normal solo funciona en movimientos u objetos de texto que tengan al menos una línea o más. Si intentas ejecutar `!iwtr '[a-z]' '[A-Z]'` \(ejecutar `tr` dentro de la palabra donde está el cursor\), verás que se aplica el comando `tr` a la línea completa, no a la palabra sobre la que está el cursor.

## Aprender los comandos externos de la manera más inteligente

Vim no es un entorno de desarrollo integrado \(o IDE por sus siglas en inglés\). Es un editor modal ligero que por diseño es altamente extensible. Debido a esa propiedad de ser extensible, tienes un acceso sencillo a los comandos externos de tu sistema. Con esto, Vim está un paso más cerca de convertirse en un IDE. Alguien dijo alguna vez que el sistema Unix es el primer IDE que ha existido.

El comando _bang_ es tan útil como el número de comandos que conozcas. No te preocupes si tienes un conocimiento limitado de los comando externos. Yo también tengo mucho que aprender. Cada vez que necesites modificar un texto, echa un vistazo si existe un comando externo que pueda solucionar tu problema. No te preocupes en dominar todo sobre un comando en particular. Simplemente aprende los que necesites para realizar tu tarea actual.

