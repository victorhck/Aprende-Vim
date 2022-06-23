# Operaciones en múltiples archivos

Ser capaz de editar un texto en múltiples archivos a la vez es otra de las funcionalidades útiles de edición. Anteriormente has aprendido cómo actualizar múltiples textos mediante `cfdo`. En este capítulo, aprenderás las diferentes formas con las que puedes editar múltiples archivos en Vim.

## Diferentes formas de ejecutar un comando en múltiples archivos

Vim tiene ocho formas de ejecutar comandos en múltiples archivos:
- lista de argumentos (`argdo`)
- lista de buffer (`bufdo`)
- lista de ventanas (`windo`)
- lista de pestañas (`tabdo`)
- lista de quickfix (`cdo`)
- lista de quickfix inteligente de archivo (`cfdo`)
- lista de ubicación (`ldo`)
- lista de ubicación inteligente de archivo (`lfdo`)

En la práctica, la mayor parte del tiempo probablemente utilizarás solo una o dos de estas ocho formas (personalmente utilizo `cdo` y `argdo` más que las demás), pero es bueno aprender sobre todas las opciones disponibles y utilizar aquellas que se adecuen mejor a tu estilo de editar. 

Aprender ocho comandos puede sonar algo abrumador. Pero en realidad, estos comandos funcionan de manera similar. Después de aprender uno, aprender el resto será más sencillo. Todas las formas comparten la misma gran idea: hacer una lista de sus respectivas categorías y después pasársela al comando que quieres ejecutar.

## Lista de argumentos

La lista de argumentos es la lista más básica. Crea una lista de archivos. Para crear una lista de archivo1, archivo2 y archivo3, puedes ejecutar:

```
:args archivo1 archivo2 archivo3
```

También puedes pasar un comodín (`*`), así si quieres hacer una lista de todos los archivos de tipo `.js` del directorio actual, ejecuta:

```
:args *.js
```

Si quieres hacer una lista de todos los archivos Javascript que comienzan con "a" en el directorio actual, ejecuta:

```
:args a*.js
```

El símbolo del comodín reemplazará uno o más de cualquier caracter del nombre del archivo en el directorio actual, pero ¿qué sucede si necesitas buscar de manera recursiva en cualquier directorio? Puedes utilizar el comodín doble (`**`). Para obtener todos los archivos Javascript dentro de los directorios dentro de la ubicación actual, ejecuta:

```
:args **/*.js
```

Una vez que ejecutas el comando `args`, tu *buffer* actual cambiará al primer elemento de la lista. Para ver la lista de archivos que acabas de crear, ejecuta `:args`. Creada la lista, puedes recorrerla. `:first` te llevará al primer elemento de la lista. `:last` te llevará al último elemento de la lista. Para moverte en la lista un archivo hacia adelante a la vez, ejecuta `:next`. Para mover hacia atrás un archivo, ejecuta `:prev`. Para moverte hacia adelante / hacia atrás un archivo cada vez y guardar los cambios, ejecuta `:wnext` y `:wprev`. Hay muchos más comandos de navegación. Echa un vistazo a `:h arglist` para saber más.

La lista de argumentos es útil si necesitas agrupar un tipo específico de archivos o unos cuantos archivos. Quizás necesitas actualizar la palabra "donut" a "tortitas" dentro de todos los archivos de tipo `yml`, para eso puedes ejecutar:

```
:args **/*.yml
:argdo %s/donut/tortitas/g | update

```
Si ejecutas de nuevo el comando `args`, esto reemplazará la lista previa. Por ejemplo, si previamente ejecutaste:

```
:args archivo1 archivo2 archivo3
```

Asumiendo que estos archivos existen, ahora tienes una lista de `archivo1`, `archivo2` y `archivo3`. Después ejecutas esto:

```
:args archivo4 archivo5
```

Tu lista inicial de `archivo1`, `archivo2` y `archivo3` es reemplazada por `archivo4` y `archivo5`. Si tienes `archivo1`, `archivo2` y `archivo3` en tu lista de argumentos y quieres *añadir* `archivo4` y `archivo5` a la lista inicial de archivos, utiliza el comando `:arga`. Ejecuta:

```
:arga archivo4 archivo5
```

Ahora tienes `archivo1`, `archivo2`, `archivo3`, `archivo4` y `archivo5` en tu lista de argumentos.

Si ejecutas `:arga` sin ningún argumento, mientras estás editando un archivo, Vim añadirá el *buffer* actual a la lista de argumentos. Si ya tenías en esa lista `archivo1`, `archivo2` y `archivo3` en tu lista de argumentos y tu *buffer* actual está en `archivo5`, al ejecutar `:arga` añadirá dicho archivo llamado `archivo5` a la lista.

Una vez que ya tienes la lista, puedes pasarle cualquier comando que quieras. Antes hemos visto un ejemplo realizando una sustitución (sustituir la palabra "donut" por "tortitas" mediante `:argdo %s/donut/tortitas/g`). Veamos otros ejemplos:
- Para eliminar todas las líneas que contienen la palabra "postre" en los archivos de la lista, ejecuta `:argdo g/postre/d`.
- Para ejecutar la macro a (asumiendo que has grabado algo en la macro a) en la lista, ejecuta `:argdo norm @a`.
- Para escribir "hola" seguido del nombre del archivo en la primera línea, ejecuta `:argdo 0put='hola ' .. @:`.

Cuando hayas terminado, no olvides guardar todos los cambios con `:update`.

En ocasiones quizás necesites ejecutar el comando solo en los primeros n elementos de la lista de argumentos. Si es ese el caso, simplemente, pasa al comando `argdo` una dirección. Por ejemplo, para ejecutar el comando de sustitución solo en los 3 primeros elementos de la lista, ejecuta `:1,3argdo %s/donut/tortitas/g`.

## Lista de *buffer*

La lista de *buffer* será creada de manera casi automática cuando edites nuevos archivos porque cada vez que creas/abres un archivo, Vim lo almacena en un *buffer* (a menos que lo borres de manera explícita). Así que si ya has abierto 3 archivos: `archivo1.rb archivo2.rb archivo3.rb`, ya tienes 3 elementos en tu lista de *buffer*, ejecuta `:buffers` (también puedes utilizar: `:ls` o `:files`). Para navegar hacia adelante o hacia atrás por los elementos de la lista de *buffer*, utiliza `:bnext` y `:bprev`. Para ir al primer o el último elemento de la lista, utiliza `:bfirst` o `:blast` respectivamente.

Por cierto, aquí comparto un interesante truco con los *buffer* sin relación con este capítulo: si tienes un número de elementos en tu lista de *buffer*, puedes mostrarlos todos con `:ball` (*buffer* all). El comando `ball` muestra todos los *buffers* de manera horizontal. Para mostrarlos de manera vertical, ejecuta `:vertical ball`.

Volviendo al tema del capítulo, la mecánica para ejecutar operaciones en todos los *buffers* abiertos es similar a la que hemos visto anteriormente. Una vez que hayas creado tu lista de *buffer* con aquellos archivos que quieras modificar a la vez, solo necesitas anteponer el(los) comando(s) que quieres ejecutar junto con `:bufdo` en vez de `:argdo` que hemos utilizado anteriormente. Así que si quieres sustituir todos los "donut" por "tortitas" en todos los *buffers* y después guardar los cambios ejecuta `:bufdo %s/donut/tortitas/g | update`.

## Lista de ventanas y pestañas

Las listas de ventanas y pestañas también son similares a las lista de argumentos y *buffer* que hemos visto hasta ahora. Las únicas diferencias son su contexto y sintaxis. 

Las operaciones de ventana son realizadas en cada ventana abierta y realizada con el comando `:windo`. Las operación de pestaña son realizadas en cada pestaña que tengas abierta y realizadas con `:tabdo`. Para más información, echa un vistazo a la ayuda de Vim `:h list-repeat`, `:h :windo` y `:h :tabdo`.

Por ejemplo, si tienes tres ventanas abiertas (puedes abrir nuevas ventanas con `Ctrl-W v` para ventanas en vertical y `Ctrl-W s` para ventanas en horizontal) y ejecutas `:windo 0put ='hola' . @%`, Vim añadirá la palabra "hola" + el nombre del archivo, en todas las ventanas abiertas.

## Lista de *quickfix*

En capítulos previos (capítulos 3 y 19), pudiste leer sobre *quickfix*. Quickfix tiene muchos usos. Los complementos más populares utilizan ventanas de quickfix, así que es bueno pasar algo de tiempo en entenderlos bien.

Si eres recién llegado a Vim, *quickfix* puede ser un concepto nuevo. En tiempos más remotos cuando tenías que compilar de manera explícita tu código, durante la la fase de compilación te podías encontrar diferentes errores. Para mostrar esos errores, necesitabas una ventana especial. Ahí es donde la ventana *quickfix* entra en escena.

Cuando compilas tu código, Vim muestra los mensajes de error en una ventana *quickfix* para poder solucionarlos más tarde. Muchos lenguajes de programación actuales no necesitan ya un compilado explícito, pero eso no hace que la ventana *quickfix* se haya quedado obsoleta. Ahora, las personas utilizan la ventana *quickfix* para diversas cosas, como mostrar un terminal virtual o almacenar los resultados de la búsqueda. Vamos a centrarnos más en este último uso, almacenar los resultados de la búsqueda.

Además de los comandos de compilar, ciertos comandos de Vim también utilizan la interfaz *quickfix*. Un tipo de comando que la utiliza de manera intensiva son los comandos de búsqueda. Tanto `:vimgrep` como `:grep` utilizan la de manera predeterminada.

Por ejemplo, si necesitas buscar la palabra "donut" en todos los archivos Javascript de manera recursiva, puedes ejecutar:

```
:vimgrep /donut/ **/*.js
```

El resultado de la búsqueda de "donut" es almacenado en una ventana quickfix. Para ver esta ventana quickfix de los resultados encontrados, ejecuta:

```
:copen
```

Para cerrarla, ejecuta:

```
:cclose
```

Para desplazarte por la lista dentro de la ventana *quickfix* hacia adelante y hacia atrás, ejecuta:

```
:cnext
:cprev
```

Para ir al primer elemento o al último, ejecuta:

```
:cfirst
:clast
```

Anteriormente he mencionado que hay dos comandos para *quickfix*: `cdo` y `cfdo`. ¿En qué se diferencian? `cdo` ejecuta el comando para cada elemento en la lista *quickfix* mientras que `cfdo` ejecuta el comando para cada *archivo* en la lista *quickfix*.

Voy a aclararlo con un ejemplo. Supongamos que después de ejecutar el comando `vimgrep` anterior, Vim ha encontrado:
- 1 resultado en `archivo1.js`
- 10 resultados en `archivo2.js`

Si ejecutas `:cfdo %s/donut/tortitas/g`, esto ejecutará `%s/donut/tortitas/g` una vez en `archivo1.js` y otra vez en `archivo2.js`. Lo ejecuta *tantas veces como archivos hay en las coincidencias encontradas.* Como hay dos archivos en los resultados, Vim ejecuta el comando de sustitución dos veces como ya hemos visto, no te preocupes por el hecho de que hay 10 coincidencias en el segundo archivo. `cfdo` solo se preocupa de cuantos archivos en total hay en la lista *quickfix*.

Si ejecutas `:cdo %s/donut/tortitas/g`, esto ejecutará `%s/donut/tortitas/g` una vez en en `archivo1.js` y *diez veces* en `archivo2.js`. Esto lo ejecuta tantas veces como elementos existen en la lista *quickfix*. Como solo hay una coincidencia encontrada en `archivo1.js` y diez coincidencias encontradas en `archivo2.js`, esto ejecutará el comando un total de 11 veces.

Como ejecutaste el comando `%s/donut/tortitas/g`, tendría sentido utilizar `cfdo`. No tendría sentido utilizar `cdo` debido a que ejecutaría `%s/donut/tortitas/g` diez veces en `archivo2.js` (`%s` es una sustitución en el archivo al completo). Ejecutar `%s` una vez por archivo es suficiente. Si usaras `cdo`, tendría más sentido utilizar este otro comando `s/donut/tortitas/g`.

Cuando decidas entre utilizar `cfdo` o `cdo`, piensa en el alcance del comando que estás ejecutando. ¿Es un comando que se ejecuta en el archivo completo (como `:%s` o `:g`) o es un comando que se ejecuta en una única línea (como `:s` o `:!`)?

## Lista de ubicación (location)

La lista de ubicación es similar a la lista *quickfix* en el sentido de que Vim también utiliza una ventana especial para mostrar los mensajes. La diferencia entre la lista *quickfix* y la lista de ubicación es que en cualquier momento, puedes tener solo una lista *quickfix*, mientra que puedes tener tantas listas de ubicación como ventanas.

Supongamos que tenemos dos ventanas abiertas, una ventana mostrando el archivo `comida.txt` y otra ventana mostrando `bebidas.txt`. Desde dentro de `comida.txt`, puedes ejecutar un comando de búsqueda de lista de ubicación `:lvimgrep` (la variante de ubicación para el comando `:vimgrep`):

```
:lvim /rosquilla/ **/*.md
```

Vim creará una lista de ubicación de todas las coincidencias encontradas de rosquilla para esa *ventana* `comida.txt`. Puedes ver la lista de ubicación con `:lopen`. Ahora ve a la otra ventana de `bebidas.txt` y ejecuta:

```
:lvimgrep /leche/ **/*.md
```

Vim creará una ubicación *separada* con todos los resultados de la búsqueda leche para esa *ventana* de `bebidas.txt`.

Para cada comando de ubicación que ejecutas en cada ventana, Vim crea una lista de ubicación distinta. Si tienes 10 ventanas diferentes, puedes llegar a tener 10 listas de ubicación distintas. Contrasta con la lista *quickfix* donde solo podías tener una cada vez. Si tienes 10 ventanas diferentes, solo tendrías una lista *quickfix*.

La mayoría de los comandos de las listas de ubicación son similares a los comandos *quickfix* excepto que están precedidos con `l-`. Por ejemplo: `:lvimgrep`, `:lgrep`, y `:lmake` vs `:vimgrep`, `:grep`, and `:make`. Para manipular la ventana de la lista de ubicación, de nuevo, los comandos son similares a los comandos *quickfix* `:lopen`, `:lclose`, `:lfirst`, `:llast`, `:lnext` y `:lprev` vs `:copen`, `:cclose`, `:cfirst`, `:clast`, `:cnext` y `:cprev`.

Los comandos de archivos múltiples de las listas de ubicación también son similares a los comandos de archivos múltiples de *quickfix*: `:ldo` y `:lfdo`. `:ldo` ejecuta el comando de ubicación en cada lista de ubicación, mientras `:lfdo` ejecuta el comando de la lista de ubicación para cada archivo en la lista de ubicación. Para más información, echa un vistazo a `:h location-list`.

## Ejecutar operaciones en múltiples archivos en Vim de la manera más inteligente

Conocer cómo realizar operaciones en múltiples archivos es una técnica muy útil a la hora de editar archivos de una manera rápida y eficiente. Ya sea porque necesitas cambiar el nombre de una variable en muchos archivos y quieras hacerlo de una pasada. Vim tiene ocho maneras diferentes para realizar esto.

En la práctica, probablemente no necesitará usar los ocho de igual manera. Quizás te decantes por uno o dos. Cuando estás empezando, escoge uno (personalmente te aconsejaría la lista de argumentos `:argdo`) y lo domines. Una vez que te encuentres cómodo usándolo, entonces aprende el siguiente. Verás que aprender el segundo, el tercero y el cuarto modo será cada vez más fácil. Se creativo. Utilízalo con diferentes combinaciones. Sigue practicando hasta que puedas realizarlo sin esfuerzo y casi sin pensarlo. Haz que forme parte de tu memoria.

Dicho esto, ya dominas la edición con Vim ¡Enhorabuena!
