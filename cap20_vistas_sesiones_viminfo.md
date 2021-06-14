# Capítulo 20: Vistas, sesiones y Viminfo

Después de haber trabajado en un proyecto durante un tiempo, puedes encontrar que el proyecto de manera gradual va tomando forma con sus propios ajustes, carpetas, _buffers_, diseños, etc. Es como decorar tu apartamento después de vivir en el durante un tiempo. El problema es, que cuando cierras Vim, pierdes esos cambios. ¿No sería genial si pudieras mantener esos cambios para que la próxima vez que abras Vim, pareciera que nunca lo hubieras cerrado?

En este capítulo, aprenderás a utilizar las vistas, sesiones y Viminfo para preservar una *instantánea* de tus proyectos.

## Vista \(View\)

Una vista es el subconjunto más pequeño de los tres temas de este capítulo \(vista, sesiones, Viminfo\). Es una colección de ajustes para una ventana. Si pasas mucho tiempo trabajando en una ventana y quieres preservar los mapas y plegados de texto (o _fold_), puedes utilizar una vista.

Vamos a crear un archivo llamado `foo.txt`:

```text
foo1
foo2
foo3
foo4
foo5
foo6
foo7
foo8
foo9
foo10
```

En este archivo, creamos tres cambios: 

1. En la línea 1, crea un pliegue de texto \(fold\) manual con `zf4j` \(pliega el contenido de las cuatro líneas siguientes\).
2. Cambia el ajuste de la numeración de las líneas `number`: `setlocal nonumber norelativenumber`. Esto eliminará los indicadores de número de la parte izquierda de la ventana.
3. Crea un mapeado local para bajar dos líneas cada vez que presiones `j` en vez de solo una línea, mediante `:nnoremap <buffer> j jj`.

Tu archivo ahora deberá tener un aspecto similar a este:

```text
+-- 5 lines: foo1 -----
foo6
foo7
foo8
foo9
foo10
```

### Configurar los atributos de las vistas

Ejecuta:

```text
:set viewoptions?
```

De manera predeterminada debería aparecer \(aunque tu mensaje podría ser diferente dependiendo de tu archivo vimrc\):

```text
viewoptions=folds,cursor,curdir
```

Vamos a configurar `viewoptions`. Los tres atributos que quieres mantener son los pliegues de texto \(folds\), los mapeados y las opciones configuradas de manera local. Si tus ajustes se parecen a los míos, ya tienes la opción `folds`. Necesitas configurar las vistas para que recuerde las `localoptions`. Ejecuta:

```text
:set viewoptions+=localoptions
```

Para aprender qué otras opciones están disponibles para `viewoptions`, echa un vistazo a `:h viewoptions`. Ahora si ejecutas `:set viewoptions?`, deberías ver algo así:

```text
viewoptions=folds,cursor,curdir,localoptions
```

### Guardando las vistas

Con la ventana en la que aparece el archivo `foo.txt` correctamente plegada y teniendo las opciones de `nonumber norelativenumber`, vamos a guardas las vistas. Ejecuta:

```text
:mkview
```

Vim crea un archivo de vistas.

### Archivos de vistas

Quizás te preguntes ¿dónde guardó Vim el archivo de las vistas? Para ver donde lo guardó Vim, ejecuta:

```text
:set viewdir?
```

De manera predeterminada lo hace en esta ruta `~/.vim/view` \(si tienes un sistema operativo diferente, podría mostrar una ruta diferente. Echa un vistazo a `:h viewdir` para más información\). Si quieres cambiar esa ruta a otro sitio de tu sistema, añade esto en tu archivo de configuración vimrc:

```text
set viewdir=$HOME/tu_ruta/personalizada
```

### Cargando el archivo de vistas

Cierra el archivo `foo.txt` si todavía no lo has hecho y después vuelve a abrirlo de nuevo. **Deberías ver el texto original sin los cambios.** Eso es lo esperado.

Para restaurar el estado, necesitas cargar el archivo de vistas. Ejecuta:

```text
:loadview
```

Ahora en tu archivo deberías ver:

```text
+-- 5 lines: foo1 -----
foo6
foo7
foo8
foo9
foo10
```

Los pliegues de texto \(folds\), los ajustes locales y los mapeados locales se han recuperado. Si te das cuenta, también el cursor debería estar en la línea en la que estaba cuando ejecutaste `:mkview`. Siempre que tengas la opción `cursor`, las vistas también recuerdan la posición del cursor.

### Múltiples vistas

Vim te permite guardar hasta un número de 9 vistas \(1-9\).

Supongamos que quieres hacer un pliegue \(fold\) adicional \(por ejemplo quieres plegar el texto de las dos últimas líneas\) con `:9,10 fold`. Vamos a guardar esto como vista 1. Ejecuta:

```text
:mkview 1
```

Si quieres hacer un pliegue de texto más con `:6,7 fold` y guardarlo como una vista diferente, ejecuta:

```text
:mkview 2
```

Cierra el archivo. Cuando vuelvas a abrir `foo.txt` y quieras cargar la vista 1, ejecuta:

```text
:loadview 1
```

Para cargar la vista 2, ejecuta:

```text
:loadview 2
```

Para cargar la vista original, ejecuta:

```text
:loadview
```

### Automatizar la creación de vistas

Una de las peores cosas que te pueden pasar trabajando con Vim es que después de pasar incontables horas organizando un archivo enorme con pliegues de texto, de manera accidental cierres la ventana y pierdas toda la información de los pliegues \(folds\). Para prevenir esto, quizás quieras crear automáticamente una vista cada vez que cierres un _buffer_. Para ello añade esto en tu archivo vimrc:

```text
autocmd BufWinLeave *.txt mkview
```

De manera adicional, podría estar bien el cargar la vista cada vez que abres un _buffer_:

```text
autocmd BufWinEnter *.txt silent loadview
```

Ahora no te tienes que preocupar de crear o cargar una vista nunca más cuando estés trabajando con archivos `txt`. Ten en cuenta que con el paso del tiempo, tu `~/.vim/view` podría empezar a acumular muchos archivos de vistas. Es buena idea limpiarlo cada pocos meses.

## Sesiones

Si una vista guarda los ajustes de una ventana, una sesión guarda la información de todas las ventanas \(incluyendo el diseño\).

### Creando una nueva sesión

Supongamos que estás trabajando con estos 3 archivos en un proyecto llamado `foobarbaz`:

Dentro de `foo.txt`:

```text
foo1
foo2
foo3
foo4
foo5
foo6
foo7
foo8
foo9
foo10
```

Dentro de `bar.txt`:

```text
bar1
bar2
bar3
bar4
bar5
bar6
bar7
bar8
bar9
bar10
```

Dentro de `baz.txt`:

```text
baz1
baz2
baz3
baz4
baz5
baz6
baz7
baz8
baz9
baz10
```

Vamos a suponer que divides tus ventanas con `split` y `vsplit`. Para preservar este aspecto, necesitas guardar la sesión. Ejecuta:

```text
:mksession
```

A diferencia de `mkview` donde de manera predeterminada guarda la configuración en `~/.vim/view`, `mksession` guarda el archivo de sesión \(`Session.vim`\) en el directorio actual. Echa un vistazo al archivo si tienes curiosidad de qué guarda dentro Vim.

Si quieres guardar el archivo de sesión en algún otro lugar, puedes pasar un argumento al comando `mksession`:

```text
:mksession ~/otra/ruta/distinta.vim
```

Si quieres sobreescribir el archivo de sesión existente, invoca el comando con el símbolo `!` \(`:mksession! ~/otra/ruta/distinta.vim`\).

### Cargando una sesión

Para cargar una sesión existente, ejecuta:

```text
:source Session.vim
```

¡Ahora Vim vuelve a tener el mismo aspecto que cuando lo cerraste! Incluyendo las divisiones de ventanas. De manera alternativa también puedes cargar el archivo de sesión desde la terminal:

```text
vim -S Session.vim
```

### Configurando atributos de la sesión

Puedes configurar los atributos que se guardan de las sesiones. Para ver qué es lo que actualmente se está guardando, ejecuta:

```text
:set sessionoptions?
```

En mi caso aparece lo siguiente:

```text
blank,buffers,curdir,folds,help,tabpages,winsize,terminal
```

Si no quieres guardar la `terminal` cuando guardes una sesión, elimina esta opción de la sesión, para ello, ejecuta:

```text
:set sessionoptions-=terminal
```

Si quiere añadir las `options` cuando guardes una sesión, ejecuta:

```text
:set sessionoptions+=options
```

Estos son algunos de los atributos que `sessionoptions` pueden gestionar:

* `blank` almacena las ventanas vacía
* `buffers` almacena los _buffers_
* `folds` almacena los plegados de textos
* `globals` almacena variables globales \(debe comenzar con una letra mayúscula y contener al menos una letra minúscula\)
* `options` almacena las opciones y mapeados
* `resize` almacena las líneas y columnas de las ventanas
* `winpos` almacena la posición de las ventanas
* `winsize` almacena los tamaños de las ventanas
* `tabpages` almacena las pestañas
* `unix` almacena archivos en formato Unix

Para consultar la lista completa, echa un vistazo a `:h 'sessionoptions'`.

Las sesiones son una herramienta útil para preservar los atributos externos de tu proyecto. Sin embargo, algunos atributos internos no se guardan en las sesiones, como las marcas locales, registros, historiales, etc. Para guardarlos, ¡necesitas utilizar Viminfo!

## Viminfo

Quizás has notado, que después de copiar una palabra al registro a y salir de Vim, la próxima vez que abras Vim verás que el texto permanece almacenado en el registro. Esto es gracias al trabajo de Viminfo. Sin este, Vim no recordaría el registro después de haber cerrado Vim.

Si utilizas la versión 8 o superior de Vim, este habilita Viminfo de manera predeterminada, ¡así que has estado utilizando Viminfo todo este tiempo sin saberlo!

Quizás te preguntes: "¿Qué guarda Viminfo? ¿Qué tiene de diferente con las sesiones?"

Para utilizar Viminfo, primero necesita tener la funcionalidad `+viminfo` disponible \(`:version`\). Viminfo almacena:

* El historial de la línea de comando
* El historial de búsqueda de cadenas de texto
* El historial de línea de entrada
* El contenido de los registros no vacíos
* Las marcas de diversos archivos
* El archivo de marcas, apuntando a la ubicación en los archivos
* El último patrón de búsqueda/sustitución \(para 'n' y '&'\).
* La lista de \*buffer
* Las variables globales

En general, las sesiones almacenan los atributos "externos" y Viminfo los atributos "internos".

A diferencia de las sesiones en donde tenías un archivo de sesión por proyecto, normalmente usarás el archivo Viminfo por equipo. Viminfo es independiente del proyecto.

La ubicación predeterminada para Viminfo en sistemas basados en Unix es `$HOME/.viminfo` \(`~/.viminfo`\). Si utilizas un sistema operativo diferente, la ubicación de Viminfo podría ser diferente. Echa un vistazo a `:h viminfo-file-name`. Cada vez que haces cambios "internos", como copiar un texto a un registro, Vim automáticamente actualiza el archivo Viminfo.

_Asegúrate que tienes activada la opción `nocompatible` \(`set nocompatible`\), de otro modo tu Viminfo no funcionará._

### Escribir y leer Viminfo

Aunque usarás solo un archivo Viminfo, puedes crear múltiples archivos Viminfo. Para escribir un archivo Viminfo, utiliza el comando `:wviminfo` \(`:wv` en método abreviado\).

```text
:wv ~/.viminfo_extra
```

Para sobre escribir un archivo Viminfo ya existente, añade un signo de cierre de exclamación al comando `wv`:

```text
:wv! ~/.viminfo_extra
```

De manera predeterminada Vim leerá el archivo `~/.viminfo`. Para leer un archivo Viminfo diferente, ejecuta `:rviminfo`, o `:rv` en modo abreviado:

```text
:rv ~/.viminfo_extra
```

Para arrancar Vim con un archivo Viminfo diferente desde la terminal, utiliza la opción `i`:

```text
vim -i viminfo_extra
```

Si utilizas Vim para diferentes tareas, como crear código o escribir, puedes crear un Viminfo optimizado para crear código y otro para escribir.

```text
vim -i viminfo_writing

vim -i viminfo_coding
```

### Arrancar Vim sin archivo Viminfo

Para arrancar Vim sin el archivo Viminfo, puedes ejecutar lo siguiente desde la terminal:

```text
vim -i NONE
```

Para hacer esta opción permanente, puedes añadir lo siguiente en tu archivo vimrc:

```text
set viminfo="NONE"
```

### Configurando los atributos de Viminfo

De manera similar a `viewoptions` y `sessionoptions`, puedes determinar qué atributos guardar con la opción `viminfo`. Ejecuta:

```text
:set viminfo?
```

Obtendrás:

```text
!,'100,<50,s10,h
```

Esto parece muy críptico. Vamos a verlo en detalle:

* `!` guarda las variables globales que comienzan con una letra mayúscula y no contienen letras minúsculas. Recuerda que `g:` indica una variable global. Por ejemplo, si en algún punto escribiste la asignación `let g:FOO = "foo"`, Viminfo guardará la variable global `FOO`. Sin embargo si hiciste `let g:Foo = "foo"`, Viminfo no guardará esta variable global ya que contiene letras minúsculas. Sin `!`, Vim no guardará esas variables globales.
* `'100` representa las marcas. En este caso, Viminfo guardará las marcas globales \(a-z\) de los últimos 100 archivos. Ten cuidado, ya que si le dices a Vim que guarde muchos archivos, Vim puede empezar a ralentizarse. 1000 es un buen número para establecer.
* `<50` le dice a Viminfo cuantas líneas como máximo son guardadas para cada registro \(50 en este caso\). Si copio 100 líneas de texto en el registro a \(`"ay99j`\) y cierro Vim, la próxima vez que abra Vim y pegue desde el registro a \(`"ap`\), Vim solo pegará 50 líneas como máximo. Si no estableces un número máximo de líneas, _todas_ las líneas se guardarán. Si estableces un 0, no se guardará nada.
* `s10` establece un límite de tamaño \(en kb\) para un registro. En este caso, cualquier registro de tamaño mayor de 10kb será excluido.
* `h` inhabilita el resaltado \(de `hlsearch`\) cuando arranca Vim.

Hay otras opciones que puedes pasarle al comando. Para aprender más, echa un vistazo a `:h 'viminfo'`.

## Utilizando las vistas, sesiones y Viminfo de la manera más inteligente

Vim tiene vistas, sesiones y Viminfo para tener un nivel diferente de tu instantánea del entorno de Vim. Para proyectos pequeños, utiliza las vistas. Para proyectos más grandes, utiliza las sesiones. Deberías tomarte tu tiempo para echar un vistazo a todas las opciones que ofrecen estas tres herramientas de Vim.

Crea tu propia vista, sesión y Viminfo para tu propio estilo de edición. Si alguna vez necesitas utilizar Vim en otro equipo, puedes cargar tus ajustes !e inmediatamente te sentirás como en casa!

