# Operaciones en múltiples archivos

Ser capaz de editar un texto en múltiples archivos a la vez es otra útil herramienta de edición. Anteriormente has aprendido cómo actualizar múltiples textos mediante `cfdo`. En este capítulo, aprenderás las diferentes formas con las que puedes editar múltiples archivos en Vim.

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

En la práctica, probablemente utilizarás solo una o dos de estas ocho formas la mayor parte del tiempo (personalmente utilizo `cdo` y `argdo` más que las demás), pero es bueno aprender sobre todas las opciones disponibles y utilizar aquellas que se adecuen mejor a tu estilo de editar. 

Aprender ocho comando puede sonar algo abrumador. Pero en realidad, estos comandos funcionan de manera similar. Después de aprender uno, aprender el resto será más sencillo. Todas las formas comparten la misma gran idea: hacer una lista de sus respectivas categorías y después pasársela al comando que quieres ejecutar.

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

La lista de argumentos es útil si necesitas agrupar un tipo específico de archivos o unos cuantos archivos. Quizás necesitas actualizar todos los "donut" a "tortitas" dentro de todos los archivos de tipo `yml`, puedes ejecutar:

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

Tu lista inicial de `archivo1`, `archivo2` y `archivo3` es reemplazada por `archivo4` y `archivo5`. Si tienes `archivo1`, `archivo2` y `archivo3` en tu lista de argumentos y quieres *añadir* `archivo4` y `archivo5` a la lista inicial de archivos, utilizael comando `:arga`. Ejecuta:

```
:arga archivo4 archivo5
```

Ahora tienes `archivo1`, `archivo2`, `archivo3`, `archivo4` y `archivo5` en tu lista de argumentos.

Si ejecutas `:arga` sin ningún argumento, mientras estás editando un archivo, Vim añadirá el *buffer* actual a la lista de argumentos. Si ya tenías en esa lista `archivo1`, `archivo2` y `archivo3` en tu lista de argumentos y tu *buffer* actual está en `archivo5`, al ejecutar `:arga` añadirá dicho archivo `archivo5` a la lista.

Una vez que ya tienes la lista, puedes pasarle cualquier comando que quieras. Antes hemos visto un ejemplo realizando una sustitución (`:argdo %s/donut/tortitas/g`). Veamos otros ejemplos:
- Para eliminar todas las líneas que contienen la palabra "postre" en los archivos de la lista, ejecuta `:argdo g/postre/d`.
- Para ejecutar la macro a (asumiendo que has grabado algo en la macro a) en la lista, ejecuta `:argdo norm @a`.
- Para escribir "hola" seguido del nombre del archivo en la primera línea, ejecuta `:argdo 0put='hola ' .. @:`.

Cuando hayas terminado, no olvides guardar todos los cambios con `:update`.

En ocasiones quizás necesites ejecutar el comando solo en los primeros n elementos de la lista de argumentos. Si es ese el caso, simplemente, pasa al comando `argdo` una dirección. Por ejemplo, para ejecutar el comando de sustitución solo en los 3 primeros elementos de la lista, ejecuta `:1,3argdo %s/donut/tortitas/g`.

## Lista de *buffer*

La lista de *buffer* será creada de manera casi automática cuando edites neuvos archivos porque cada vez que creas/abres un archivo, Vim lo almacena en un *buffer* (a menos que lo borres de manera explícita) Así que si ya has abierto 3 archivos: `archivo1.rb archivo2.rb archivo3.rb`, ya tienes 3 elementos en tu lista de *buffer*, ejecuta `:buffers` (también puedes utilizar: `:ls` o `:files`). Para navegar hacia adelante o hacia atrás, utiliza `:bnext` y `:bprev`. Para ir al primer o el último elemento de la lista, utiliza `:bfirst` o `:blast`.

Por cierto, aquí comparto un interesante truco con los *buffer* sin relación con este capítulo: si tienes un número de elementos en tu lista de *buffer*, puedes mostrarlos todos con `:ball` (*buffer* all). El comando `ball` muestra todos los *buffers* de manera horizontal. Para mostrarlos de manera vertical, ejecuta `:vertical ball`.

Volviendo al tema del capítulo, la mecánica para ejecutar operaciones en todos los *buffers* abiertos es similar a la que hemos visto anteriormente. Una vez que hayas creado tu lista de *buffer* con aquellos archivos que quieras modificar a la vez, solo necesitas anteponer el(los) comando(s) que quieres ejecutar junto con `:bufdo` en vez de `:argdo` que hemos utilizado anteriormente. Así que si quieres sustituir todos los "donut" con "tortitas" en todos los *buffers* y después guardar los cambios ejecuta `:bufdo %s/donut/tortitas/g | update`.

## Lista de ventanas y pestañas

La lista de ventanas y pestañas también son similares a las lista de argumentos y *buffer* que hemos visto hasta ahora. Las únicas diferencias son su contexto y sintaxis. 

Las operaciones de ventana son realizadas en cada ventana abierta y realizada con el comando `:windo`. Las operación de pestaña son realizadas en cada pestaña que tengas abierta y realizadas con `:tabdo`. Para más información, echa un vistazo a la ayuda de Vim `:h list-repeat`, `:h :windo` y `:h :tabdo`.

Por ejemplo, si tienes tres ventanas abiertas (puedes abrir nuevas ventanas con `Ctrl-W v` para ventanas en vertical y `Ctrl-W s` para ventanas en horizontal) y ejecutas `:windo 0put ='hola' . @%`, Vim añadirá la palabra "hola" + el nombre del archivo en todas las ventanas abiertas.

## Lista de quickfix

En capítulos previos (capítulos 3 y 19), pudiste leer sobre *quickfix*. Quickfix tiene muchos usos. Los complementos más populares utilizan ventanas de quickfix, así que es bueno pasar algo de tiempo en entenderlos bien.

Si eres recién llegado a Vim, quickfix puede ser un concepto nuevo. En tiempos más remotos cuando tenías que compilar de manera explícita tu código, durante la la fase de compilación te podías encontrar diferentes errores. Para mostrar esos errores, necesitabas una ventana especial. Ahí es donde la ventana quickfix entra en escena. Cuando compilas tu código, Vim muestra los mensajes de error en una ventana quickfix para poder solucionarlos más tarde. Muchos lenguajes de programación actuales no necesitan ya un compilado explícito, pero eso no hace que la ventana quickfix se haya quedado obsoleta. Ahora, las personas utilizan la ventana quickfix para diversas cosas, como mostrar un terminal virtual o almacenar los resultados de la búsqueda. Vamos a centrarnos más en este último uso, almacenar los resultados de la búsqueda.

Además de los comandos de compilar, ciertos comandos de Vim también utilizan la interfaz quickfix. Un tipo de comando que utiliza quickfix de manera intensiva son los comandos de búsqueda. Tanto `:vimgrep` como `:grep` utilizan quickfixes de manera predeterminada.

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

Para desplazarte por la lista dentro de la ventana quickfix hacia adelante y hacia atrás, ejecuta:

```
:cnext
:cprev
```

Para ir al primer elemento o al último, ejecuta:

```
:cfirst
:clast
```

Anteriormente he mencionado que hay dos comandos para quickfix: `cdo` y `cfdo`. ¿En qué se diferencian? `cdo` ejecuta el comando para cada elemento en la lista quickfix mientras que `cfdo` ejecuta el comando para cada *archivo* en la lista quickfix.

Voy a aclararlo con un ejemplo. Supongamos que después de ejecutar el comando `vimgrep` anterior, Vim ha encontrado:
- 1 resultado en `archivo1.js`
- 10 resultados en `archivo2.js`

Si ejecutas `:cfdo %s/donut/tortitas/g`, esto ejecutará `%s/donut/tortitas/g` una vez en `archivo1.js` y otra vez en `archivo2.js`. Lo ejecuta *tantas veces como archivos hay en las coincidencias encontradas.* Como se hay dos archivos en los resultados, Vim ejecuta el comando de sustitución dos veces como ya hemos visto, no te preocupes por el hecho de que hay 10 coincidencias en el segundo archivo. `cfdo` solo se preocupa de cuantos archivos en total hay en la lista quickfix.

Si ejecutas `:cdo %s/donut/tortitas/g`, esto ejecutará `%s/donut/tortitas/g` una vez en en `archivo1.js` y *diez veces* en `archivo2.js`. Esto lo ejecuta tantas veces como elementos existen en la lista quickfix. Como solo hay una coincidencia encontrada en `archivo1.js` y diez coincidencias encontradas en `archivo2.js`, esto ejecutará el comando un total de 11 veces.

Como ejecutaste el comando `%s/donut/tortitas/g`, tendría sentido utilizar `cfdo`. No tendría sentido utilizar `cdo` debido a que ejecutaría `%s/donut/tortitas/g` diez veces en `archivo2.js` (`%s` es una sustitución en el archivo al completo). Ejecutar `%s` una vez por archivo es suficiente. Si usaras `cdo`, tendría más sentido utilizar este otro comando `s/donut/tortitas/g`.

Cuando decidas entre utilizar `cfdo` o `cdo`, piensa en el alcance del comando que estás ejecutando. ¿Es un comando que se ejecuta en el archivo completo (como `:%s` o `:g`) o es un comando que se ejecuta en una única línea (como `:s` o `:!`)?

## Location List

Location list is similar to quickfix list in a sense that Vim also uses a special window to display messages. The difference between a quickfix list and a location list is that at any time, you may only have one quickfix list, whereas you can have as many location list as windows.

Suppose that you have two windows opened, one window displaying `food.txt` and another displaying `drinks.txt`. From inside `food.txt`, you run a location-list search command `:lvimgrep` (the location variant for the `:vimgrep` command):

```
:lvim /bagel/ **/*.md
```

Vim will create a location list of all the bagel search matches for that `food.txt` *window*. You can see the location list with `:lopen`. Now go to the other window `drinks.txt` and run:

```
:lvimgrep /milk/ **/*.md
```

Vim will create a *separate* location list with all the milk search results for that `drinks.txt` *window*.

For each location-command you run in each window, Vim creates a distinct location list. If you have 10 different windows, you can have up to 10 different location lists. Contrast this with the quickfix list where you can only have one at any time. If you have 10 different windows, you still get only one quickfix list.

Most of the location list commands are similar to quickfix commands except that they are prefixed with `l-` instead. For example: `:lvimgrep`, `:lgrep`, and `:lmake` vs `:vimgrep`, `:grep`, and `:make`. To manipulate the location list window, again, the commands look similar to the quickfix commands `:lopen`, `:lclose`, `:lfirst`, `:llast`, `:lnext`, and `:lprev` vs `:copen`, `:cclose`, `:cfirst`, `:clast`, `:cnext`, and `:cprev`.

The two location list multi-file commands are also similar to quickfix multi-file commands: `:ldo` and `:lfdo`. `:ldo` executes the location command in each location list while `:lfdo` executes the location list command for each file in the location list. For more, check out `:h location-list`.

## Running Multiple-File Operations in Vim

Knowing how to do a multiple file operation is a useful skill to have in editing. Whenever you need to change a variable name across multiple files, you want to execute them in one swoop. Vim has eight different ways you can do this.

Practically speaking, you probably won't use all eight equally. You will gravitate towards one or two. When you are starting out, pick one (I personally suggest starting with the arg list `:argdo`) and master it. Once you are comfortable with one, then learn the next one. You will find that learning the second, third, fourth gets easier. Be creative. Use it with different combinations. Keep practicing until you can do this effortlessly and without much thinking. Make it part of your muscle memory.

With that being said, you've mastered Vim editing. Congratulations!
