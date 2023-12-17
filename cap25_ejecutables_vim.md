# Capítulo 25: Los ejecutables \(runtime\) de Vim

En capítulos anteriores, he mencionado que Vim busca de manera automática rutas especiales como `pack/` \(capítulo 22\) o `compiler/` \(capítulo 19\) dentro del directorio `~/.vim/`. Estos son ejemplos de rutas de ejecutables de Vim.

Vim tiene más rutas de ejecutables que estas dos que he mencionado, repasaremos una descripción general de alto nivel de estas rutas de ejecutables. La meta de este capítulo es mostrar cuando son llamados. Sabiendo esto, te permitirá entender y personalizar Vim aún mas.

## La ruta de ejecutables

En equipos con Unix o derivadas, una de tus rutas de ejecutables es `$HOME/.vim/` \(si tienes un sistema operativo diferente como Windows, esto podría ser diferente\). Para ver qué rutas diferentes de ejecutables hay para distintos sistemas operativos, echa un vistazo a `:h 'runtimepath'`. En este capítulo, usaré `~/.vim/` como ruta predeterminada.

## _Scripts_ de complementos

Vim tiene una ruta de ejecutable para los complementos que ejecutan cualquier _script_ en este directorio una vez que arranca Vim. No hay que confundir el nombre "complemento" \(o _plugin_\) con los complementos externos de Vim \(como NERDTree, fzf.vim, etc\).

Ve al directorio `~/.vim/` y crea un directorio llamado `plugin/`. Crea dos archivos dentro: `donut.vim` y `chocolate.vim`.

Dentro del archivo `~/.vim/plugin/donut.vim` añade esta línea:

```text
echo "¡donut!"
```

Dentro del archivo `~/.vim/plugin/chocolate.vim` añade esta línea:

```text
echo "¡chocolate!"
```

Ahora cierra Vim. La próxima vez que lo inicies, verás que se muestran las palabras `"donut!"` y `"chocolate!"`. La ruta de ejecutable de complemento puede ser utilizada para _scripts_ de inicialización.

## Detección de tipos de archivos

Antes de comenzar, asegúrate que estas detecciones funcionan, asegurándote que tu archivo vimrc contiene al menos la siguiente línea:

```text
filetype plugin indent on
```

Echa un vistazo a `:h filetype-overview` para más información. En esencia, esto activa la detección de tipos de archivo en Vim.

Cuando abre un archivo nuevo, Vim normalmente sabe qué clase de archivo es. Si tienes un archivo `hola.rb`, al ejecutar `:set filetype?` debería mostrar la respuesta correcta `filetype=ruby`.

Vim sabe cómo detectar los tipos de archivos "más comunes" \(Ruby, Python, Javascript, etc\). Pero ¿qué pasa si tienes un tipo de archivos personalizados? Necesitas enseñar a Vim a detectarlo y asignarlo con el tipo de archivo correcto.

Hay dos métodos de detección: utilizando un nombre de archivo o utilizando un archivo de contenido.

### Detección por nombre de archivo

Mediante la detección por nombre de archivo se detecta el tipo de archivo utilizando el nombre del archivo. Cuando abres el archivo `hola.rb`, Vim sabe que es un tipo de archivo de lenguaje Ruby por la extensión `.rb`.

Hay dos maneras por las que puedes detectar un archivo por el nombre: utilizando el directorio `ftdetect/` o utilizando el archivo `filetype.vim`. Vamos a explorar ambas opciones.

#### `ftdetect/`

Vamos a crear un archivo vacío \(aunque sabroso\), `hola.chocodonut`. Cuando lo abres y ejecutas `:set filetype?`, como no tiene una extensión común que Vim pueda reconocer, este devuelve lo siguiente `filetype=`.

Necesitas enseñar a Vim que todos los archivos que acaban con `.chocodonut` es un archivo de tipo "chocodonut". Crea un directorio llamado `ftdetect/` en la ruta `~/.vim/`. Dentro crea un archivo y llámalo `chocodonut.vim` \(`~/.vim/ftdetect/chocodonut.vim`\). Dentro de ese archivo, añade:

```text
autocmd BufNewFile,BufRead *.chocodonut set filetype=chocodonut
```

* `BufNewFile` y `BufRead` son ejecutados cada vez que creas o abres un nuevo _buffer_.
* `*.chocodonut` significa que este evento solo será ejecutado si el _buffer_ abierto tiene `.chocodonut` como extensión en el nombre del archivo.
* El comando, `set filetype=chocodonut` establece el tipo de archivo para que sea de tipo chocodonut.

Reinicia Vim. Ahora abre el archivo `hola.chocodonut` y ejecuta `:set filetype?`. Y ahora contestará `filetype=chocodonut`.

¡Delicioso! Puedes poner tantos archivos como quieras dentro de `ftdetect/`. En el futuro, quizás puedas añadir `ftdetect/fresadonut.vim`, `ftdetect/glasedodonut.vim`, etc., si decides expandir tus tipos de archivos de donuts.

En realidad hay dos manera de establecer el tipo de archivo en Vim. Una es la que acabas de utilizar `set filetype=chocodonut`. La otra manera es ejecutar `setfiletype chocodonut`. El primer comando `set filetype=chocodonut` _siempre_ establecerá el tipo de archivo al tipo chocodonut, mientras que el segundo comando `setfiletype chocodonut` solo establecerá el tipo si todavía no se ha establecido el tipo de archivo.

#### Archivo Filetype

El segundo método de detección de archivo requiere que crees un archivo llamado `filetype.vim` en el directorio raíz de tu instalación de Vim \(`~/.vim/filetype.vim`\). Añade esto dentro del archivo:

```text
autocmd BufNewFile,BufRead *.plaindonut set filetype=plaindonut
```

Crea un archivo `hola.plaindonut`. Cuando lo abres y ejecutas `:set filetype?`, Vim muestra el tipo de archivo personalizado correcto `filetype=plaindonut`.

¡Madre mía, funciona! Por cierto, si experimentas un poco con `filetype.vim`, puedes notar que este archivo está siendo ejecutado múltiples veces cuando abres `hola.plaindonut`. Para prevenir esto, puedes añadir un sistema para que el _script_ principal solo se ejecute una vez. Añade lo siguiente en el archivo `filetype.vim`:

```text
if exists("did_load_filetypes")
  finish
endif

augroup donutfiletypedetection
  autocmd! BufRead,BufNewFile *.plaindonut setfiletype plaindonut
augroup END
```

`finish` es un comando de Vim para detener la ejecución del resto del _script_. La expresión `"did_load_filetypes"` _no_ es una función propia de Vim. Es realmente una variable global de dentro de `$VIMRUNTIME/filetype.vim`. Si te puede la curiosidad, ejecuta `:e $VIMRUNTIME/filetype.vim`. Encontrará estas líneas dentro:

```text
if exists("did_load_filetypes")
  finish
endif

let did_load_filetypes = 1
```

Cuando Vim llama a este archivo, este define una variable `did_load_filetypes` y la establece a 1. 1 es equiparable a verdadero en Vim. Deberías también leer el resto de `filetype.vim`. Comprueba si puedes entender qué hace cuando Vim la llama.

### _Script_ de tipo de archivo

Vamos a aprender a detectar y asignar un tipo de archivo basándonos en el contenido del archivo.

Supongamos que tienes unos cuantos archivos sin una extensión que les identifique. La única cosa que tienen en común esos archivos es que todos ellos comienzan con la palabra "donutify" en su primera línea. Quieres asignar estos archivos a un tipo de archivo llamado `donut`. Crea unos nuevos archivos llamados `sugardonut`, `glazeddonut` y `frieddonut` \(sin extensión\). Dentro de cada archivo, añade esta línea:

```text
donutify
```

Cuando ejecutas `:set filetype?` dentro de `sugardonut`, Vim no sabe qué tipo de archivo asignar a este archivo. Así que devuelve `filetype=`.

En la ruta raíz de los ejecutables de Vim, añade un archivo llamado `scripts.vim` \(`~/.vim/scripts.vim`\). Dentro del archivo, añade estas líneas:

```text
if did_filetype()
  finish
endif

if getline(1) =~ '^\\<donutify\\>'
  setfiletype donut
endif
```

La función `getline(1)` devuelve el texto de la primera línea. Esta comprueba si la primera línea comienza con la palabra "donutify". La función `did_filetype()` es una función propia de Vim. Esta devolverá "true" cuando un evento relacionado con un tipo de archivo se ejecute la primera vez. Es utilizado como medida de seguridad para impedir volver a ejecutar un evento de tipo de archivo.

Abre el archivo `sugardonut` y ejecuta `:set filetype?`, Vim ahora muestra `filetype=donut`. Si abres otro archivo de tipo "donut" \(`glazeddonut` o `frieddonut`\), Vim también identifica esos archivos como de tipo `donut`.

Ten en cuenta que `scripts.vim` solo es ejecutado cuando Vim abre un archivo con un tipo de archivo desconocido. Si Vim abre un archivo con un tipo de archivo conocido `scripts.vim` no se ejecutará.

## Tipo de archivo complemento

¿Qué ocurre si quieres que Vim ejecute unos _scripts_ específicos de archivos _chocodonut_ cuando abres un archivo chocodonut y no quieres que ejecute esos _scripts_ cuando abres archivos de tipo plaindonut?

Puedes hacer esto con tipos de archivos de complementos en la ruta de los ejecutables de Vim \(`~/.vim/ftplugin/`\). Vim busca dentro de este directorio un archivo con el mismo nombre que el tipo de archivo que acabas de abrir. 

Crea un archivo llamado `chocodonut.vim` \(`~/.vim/ftplugin/chocodonut.vim`\):

```text
echo "Mensaje desde chocodonut ftplugin"
```

Crea otro archivo ftplugin, `plaindonut.vim` \(`~/.vim/ftplugin/plaindonut.vim`\):

```text
echo "Mensaje desde plaindonut ftplugin"
```

Ahora cada vez que abras un tipo de archivo chocodonut, Vim ejecuta los _scripts_ desde la ruta `~/.vim/ftplugin/chocodonut.vim`. Cada vez que abras un archivo de tipo plaindonut, Vim ejecuta los _scripts_ desde la ruta `~/.vim/ftplugin/plaindonut.vim`.

Una advertencia: estos archivos son ejecutados cada vez que un _buffer_ de este tipo de archivo se establece \(`set filetype=chocodonut` por ejemplo\). Si abres 3 archivos diferentes de tipo chocodonut, los _scripts_ serán ejecutados un _total_ de tres veces.

## Archivos de sangrado de márgenes \(_indentado_\)

Vim tiene una ruta para ejecutables para el sangrado que funciona de manera similar a ftplugin, donde Vim busca un archivo llamado de igual manera que el tipo de archivo abierto. El propósito de esta ruta de ejecutables de sangrado es almacenar los códigos relacionados con el sangrado. Si tienes el archivo `~/.vim/indent/chocodonut.vim`, este será ejecutado solo cuando abres un archivo de tipo chocodonut. Puedes almacenar códigos relacionados con el sangrado en esta ubicación.

## Colores

Vim tiene una ruta para los ejecutables de los colores \(`~/.vim/colors/`\) para almacenar los esquemas de colores. Cualquier archivo que este dentro de este directorio será mostrado al ejecutar el comando `:color`.

Si tienes un archivo `~/.vim/colors/beautifulprettycolors.vim`, cuando ejecutas `:color` y presionas la tecla tabulador, verás `beautifulprettycolors` como una de las opciones que puedes escoger con el color. Si prefieres añadir tu propio esquema de color, este es el lugar para hacerlo.

Si quieres echar un vistazo a los esquemas de colores que han hecho otras personas, un buen lugar para visitar es [vimcolors](https://vimcolors.com/).

## Resaltado de sintaxis

Vim tiene una ruta para los ejecutables de sintaxis \(`~/.vim/syntax/`\) para definir el resaltado de sintaxis.

Supongamos que tenemos el archivo `hello.chocodonut`, dentro del archivo tienes las siguientes expresiones:

```text
(donut "sabroso")
(donut "salado")
```

Aunque ahora Vim conoce el tipo de archivo correcto, todos los textos tienen el mismo color. Vamos a añadir una regla de resaltado de sintaxis para resaltar la palabra clave "donut". Crea un nuevo archivo de sintaxis llamado chocodonut, `~/.vim/syntax/chocodonut.vim`. Dentro añade lo siguiente:

```text
syntax keyword donutKeyword donut

highlight link donutKeyword Keyword
```

Ahora vuelve a abrir el archivo `hola.chocodonut`. Las palabras claves `donut` ahora están resaltadas.

En este capítulo no entraremos en profundidad sobre el tema de resaltado de sintaxis. Es un tema muy amplio. Si tienes curiosidad, echa un vistazo a `:h syntax.txt`.

El complemento [vim-polyglot](https://github.com/sheerun/vim-polyglot) es un gran complemento que ofrece resaltado para los lenguajes de programación más populares.

## Documentación

Si creas un complemento, tendrás que crear tu propia documentación de cómo funciona. Para eso deberás utilizar la ruta de la documentación.

Vamos a crear una documentación básica para las palabras clave chocodonut y plaindonut. Crea un archivo `donut.txt` \(`~/.vim/doc/donut.txt`\). Y dentro añade los siguientes textos:

```text
*chocodonut* Delicioso donut con chocolate

*plaindonut* Sin chocolate, pero aún así sigue siendo delicioso
```

Si tratas de buscar ayuda para `chocodonut` o `plaindonut` \(`:h chocodonut` o `:h plaindonut`\), no encontrarás nada.

Primero, necesitas ejecutar `:helptags` para generar nuevas entradas de la ayuda. Ejecuta `:helptags ~/.vim/doc/`

Ahora si ejecutas `:h chocodonut` o `:h plaindonut`, encontrarás estas nuevas entradas de la ayuda de Vim. Ten en cuenta que ahora el archivo es de solo lectura y tiene asignado un tipo de archivo de ayuda \("help"\).

## _Scripts_ de carga diferida

Todas estas rutas de ejecutables que has aprendido en este capítulo son ejecutadas de manera automática. Si quieres cargar un _script_ de manera manual, utiliza la ruta de _autoload_.

Crea un directorio autoload. \(`~/.vim/autoload/`\). Dentro del directorio, crea un nuevo archivo y ponle este nombre `tasty.vim` \(`~/.vim/autoload/tasty.vim`\). Dentro del archivo escribe:

```text
echo "tasty.vim global"

function tasty#donut()
  echo "tasty#donut"
endfunction
```

Ten en cuenta que el nombre de la función es `tasty#donut`, no `donut()`. El símbolo de _almohadilla_ \(`#`\) es necesario cuando se utiliza la funcionalidad de autoload. La convención para nombrar las funciones para autoload es la siguiente:

```text
function Nombredearchivo#Nombredefunción()
  ...
endfunction
```

En este caso, el nombre del archivo es `tasty.vim` y el nombre de la función es \(técnicamente\) `donut`.

Para invocar una función, necesitas el comando `call`. Vamos a llamar la función con `:call tasty#donut()`.

La primera vez que llamas a la función, deberías ver _ambos_ mensajes del comando echo \("tasty.vim global" y "tasty\#donut"\). Las siguiente llamadas a la función `tasty#donut` solo mostrarán el mensaje de "tasty\#donut".

Cuando abres un archivo en Vim, a diferencia de las rutas que hemos visto anteriormente, los _scripts_ de autoload no son cargados de manera automática. Solo cuando de manera explícita llamas `tasty#donut()`, es cuando Vim busca el archivo `tasty.vim` y carga todo lo que hay dentro de él, incluyendo la función `tasty#donut()`. Autoload es el mecanismo perfecto para funciones que utilizan muchos recursos de tu equipo pero que no las utilizas a menudo.

Puedes añadir cuantos directorios anidados con autoload como quieras. Si tienes la siguiente ruta `~/.vim/autoload/uno/dos/tres/tasty.vim`, puedes llamar a la función mediante `:call uno#dos#tres#tasty#donut()`.

## Después de los _scripts_

Vim tiene una ruta para los ejecutables posteriores \(`~/.vim/after/`\) este copia la estructura de `~/.vim/`. Cualquier cosa en esta ruta es ejecutada al final, así los desarrolladores utilizan estas rutas para _scripts_ de anulaciones.

Por ejemplo, si quieres sobrescribir los _scripts_ de `plugin/chocolate.vim`, puedes crear `~/.vim/after/plugin/chocolate.vim` en los que ubicar los _scripts_ de anulación. Vim ejecutará `~/.vim/after/plugin/chocolate.vim` _después_ de `~/.vim/plugin/chocolate.vim`.

## $VIMRUNTIME

Vim tiene una variable de entorno `$VIMRUNTIME` para _scripts_ predeterminados y archivos admitidos. Puedes comprobarla ejecutando `:e $VIMRUNTIME`.

La estructura debería tener una aspecto familiar. Este contiene muchas de las rutas de ejecutables que has aprendido en este capítulo.

Recuerda que en el capítulo 21 aprendiste que cuando abres Vim, este busca unos archivos vimrc en diferentes ubicaciones. Dije que la última ubicación que comprueba Vim es `$VIMRUNTIME/defaults.vim`. Si Vim falla al buscar cualquier archivo vimrc de usuario, Vim utiliza un archivo `defaults.vim` como vimrc.

¿Has intentado ejecutar Vim sin un complemento de sintaxis como vim-polyglot y aún así tu archivo está resaltado? Esto se produce cuando Vim falla al encontrar un archivo de sintaxis desde la ruta de ejecutables, Vim busca un archivo de sintaxis desde el directorio de sintaxis de `$VIMRUNTIME`.

Para aprender más, echa un vistazo a `:h $VIMRUNTIME`.

## Opciones del _Runtimepath_

Para comprobar la ruta de tus ejecutables, ejecuta `:set runtimepath?`

Si utilizas Vim-Plug u otros gestores de complementos externos populares, deberías mostrar una lista de directorios. Por ejemplo, la mía muestra:

```text
runtimepath=~/.vim,~/.vim/plugged/vim-signify,~/.vim/plugged/base16-vim,~/.vim/plugged/fzf.vim,~/.vim/plugged/fzf,~/.vim/plugged/vim-gutentags,~/.vim/plugged/tcomment_vim,~/.vim/plugged/emmet-vim,~/.vim/plugged/vim-fugitive,~/.vim/plugged/vim-sensible,~/.vim/plugged/lightline.vim, ...
```

Una de las cosas que hacen los gestores de complementos es añadir cada complemento en la ruta de ejecutables. Cada ruta de ejecutable puede tener su propio estructura de directorio similar a `~/.vim/`.

Si tienes un directorio `~/caja/de/donuts/` y quieres añadir ese directorio a tu ruta de ejecutables, puedes añadir esto en tu vimrc:

```text
set rtp+=$HOME/caja/de/donuts/
```

Si dentro de `~/caja/de/donuts/`, tienes un directorio de complementos \(`~/caja/de/donuts/plugin/hello.vim`\) y un _ftplugin_ \(`~/caja/de/donuts/ftplugin/chocodonut.vim`\), Vim ejecutará todos los _scripts_ de `plugin/hello.vim` cuando abras Vim. Vim también ejecutará `ftplugin/chocodonut.vim` cuando abras un archivo de tipo chocodonut.

Prueba esto tu mismo: crea una ruta aleatoria y añádela en tu _runtimepath_. Añade algunas rutas de ejecutables que has aprendido en este capítulo. Asegúrate de que funcionen como se espera que lo hagan.

## Aprende sobre los _runtime_ de la manera más inteligente

Tómate tu tiempo en leerlo y practicar con estas rutas. Para ver cómo se utilizan, ve al repositorio de uno de tus complementos preferidos de Vim y estudia las estructuras de sus directorios. Deberías ser capaz de entender la mayoría de ellos a partir de ahora. Trata de continuar y ver el panorama en general. Ahora que entiendes la estructura de directorios de Vim, ya estás preparado y preparada para aprender Vimscript.

