# Capítulo 23: Los ejecutables (*runtime*) de Vim

En capítulos anteriores, he mencionado que Vim busca de manera automática rutas especiales como `pack/` (capítulo 22) o `compiler/` (capítulo 19) dentro del directorio `~/.vim/`. Estos son ejemplos de rutas de ejecutables de Vim.

Vim tiene más rutas de ejecutables que estas dos que he mencionado, repasaremos una descripción general de alto nivel de estas rutas de ejecutables. La meta de este capítulo es mostrar cuando son llamados. Sabiendo esto, te permitirá entender y personalizar ún mas.

## La ruta de ejecutables

En equipos con Unix o derivadas, una de tus rutas de ejecutables es `$HOME/.vim/` (si tienes un sistema operativo diferente como Windows, esto podría ser diferente). Para ver qué rutas diferentes de ejecutables hay para distintos sistemas operativos, echa un vistazo a `:h 'runtimepath'`. En este capítulo, usaré `~/.vim/` como ruta predeterminada.

## *Scripts* de complementos

Vim tiene una ruta de ejecutable para los complementos que ejecutan cualquier *script* en este directorio una vez que arranca Vim. No hay que confundir el nombre "complemento" (o *plugin*) con los complementos externos de Vim (como NERDTree, fzf.vim, etc).

Ve al directorio `~/.vim/` y crea un directorio llamado `plugin/`. Crea dos archivos dentro: `donut.vim` y `chocolate.vim`.

Dentro del archivo `~/.vim/plugin/donut.vim` añade esta línea:

```
echo "¡donut!"
```

Dentro del archivo `~/.vim/plugin/chocolate.vim` añade esta línea:

```
echo "¡chocolate!"
```

Ahora cierra Vim. La próxima vez que lo inicies, verás que se muestran las palabras `"donut!"` y `"chocolate!"`. La ruta de ejecutable de complemento puede ser utilizada para *scripts* de inicialización.

## Detección de tipos de archivos

Antes de comenzar, asegúrate que estas detecciones funcionan, asegurándote que tu archivo vimrc contiene al menos la siguiente línea:

```
filetype plugin indent on
```

Echa un vistazo a `:h filetype-overview` para más información. En esencia, esto activa la detección de tipos de archivo en Vim.

Cuando abre un archivo nuevo, Vim normalmente sabe qué clase de archivo es. Si tienes un archivo `hola.rb`, al ejecutar `:set filetype?` debería mostrar la respuesta correcta `filetype=ruby`.

Vim sabe cómo detectar los tipos de archivos "más comunes" (Ruby, Python, Javascript, etc). Pero ¿qué pasa si tienes un tipo de archivos personalizados? Necesitas enseñar a Vim a detectarlo y asignarlo con el tipo de archivo correcto.

Hay dos métodos de detección: utilizando un nombre de archivo o utilizando un archivo de contenido.

### Detección por nombre de archivo

Mediante la detección por nombre de archivo se detecta el tipo de archivo utilizando el nombre del archivo. Cuando abres el archivo `hola.rb`, Vim sabe que es un tipo de archivo de lenguaje Ruby por la extensión `.rb`.

Hay dos maneras por las que puedes detectar un archivo por el nombre: utilizando el directorio `ftdetect/` o utilizando el archivo `filetype.vim`. Vamos a explorar ambas opciones.

#### `ftdetect/`

Vamos a crear un archivo vacío (aunque sabroso), `hola.chocodonut`. Cuando lo abres y ejecutas `:set filetype?`, como no tiene una extensión común que Vim pueda reconocer, este devuelve lo siguiente `filetype=`.

Necesitas enseñar a Vim que todos los archivos que acaban con `.chocodonut` es un archivo de tipo "chocodonut". Crea un directorio llamado `ftdetect/` en la ruta `~/.vim/`. Dentro crea un archivo y llámalo `chocodonut.vim` (`~/.vim/ftdetect/chocodonut.vim`). Dentro de ese archivo, añade:

```
autocmd BufNewFile,BufRead *.chocodonut set filetype=chocodonut
```

`BufNewFile` y `BufRead` son ejecutados cada vez que creas o abres un nuevo *buffer*. `*.chocodonut` significa que este evento solo será ejecutado is el *buffer* abierto tiene `.chocodonut` como extensión en el nombre del archivo. Finalmente el comando, `set filetype=chocodonut` establece el tipo de archivo para que sea de tipo chocodonut.

Reinicia Vim. Ahora abre el archivo `hola.chocodonut` y ejecuta `:set filetype?`. Y ahora contestará `filetype=chocodonut`.

¡Delicioso! Puedes poner tantos archivos como quieras dentro de `ftdetect/`. En el futuro, quizás puedas añadir `ftdetect/fresadonut.vim`, `ftdetect/glasedodonut.vim`, etc., si decides expandir tus tipos de archivos de donuts.

En realizidad hay dos manera de establecer el tipo de archivo en Vim. Una es la que acabas de utilizar `set filetype=chocodonut`. La otra manera es ejecutar `setfiletype chocodonut`. El primer comando `set filetype=chocodonut` *siempre* establecerá el tipo de archivo al tipo chocodonut, mientras que el segundo comando `setfiletype chocodonut` solo establecerá el tipo si todavía no se ha establecido el tipo de archivo.

#### Archivo Filetype

El segundo método de detección de archivo requiere que crees un archivo llamado `filetype.vim` en el directorio raíz de tu instalación de Vim (`~/.vim/filetype.vim`). Añade esto dentro del archivo:

```
autocmd BufNewFile,BufRead *.plaindonut set filetype=plaindonut
```

Crea un archivo `hola.plaindonut`. Cuando lo abres y ejecutas `:set filetype?`, Vim muestra el tipo de archivo personalizado correcto `filetype=plaindonut`.

¡Madre mía, funciona! Por cierto, si experimentas un poco con `filetype.vim`, puedes notar que este archivo está siendo ejecutado múltiples veces cuando abres `hola.plaindonut`. Para prevenir esto, puedes añadir un sistema para que el *script* principal solo se ejecute una vez. Añade lo siguiente en el archivo `filetype.vim`:

```
if exists("did_load_filetypes")
  finish
endif

augroup donutfiletypedetection
  autocmd! BufRead,BufNewFile *.plaindonut setfiletype plaindonut
augroup END
```

`finish` es un comando de Vim para detener la ejecución del resto del *script*. La expresión `"did_load_filetypes"` *no* es una función propia de Vim. Es realmente una variable global de dentro de `$VIMRUNTIME/filetype.vim`. Si te puede la curiosidad, ejecuta `:e $VIMRUNTIME/filetype.vim`. Encontrará estas líneas dentro:

```
if exists("did_load_filetypes")
  finish
endif

let did_load_filetypes = 1
```

Cuando vim llama a este archivo, este define una variable `did_load_filetypes` y la establece a 1. 1 es equiparable a verdadero en Vim. Deberías también leer el resto de `filetype.vim`. Comprueba si puedes entender qué hace cuando Vim la llama.

### *Script* de tipo de archivo

Vamos a aprender a detectar y asignar un tipo de archivo basándonos en el contenido del archivo.

supongamos que tienes unos cuantos archivos sin una extensión que les identifique. La única cosa que tienen en común esos archivos es que todos ellos comienzan con la palabra "donutify" en su primera línea. Quieres asignar estos archivos a un tipo de archivo llamado `donut`. Crea unos nuevos archivos llamados `sugardonut`, `glazeddonut` y `frieddonut` (sin extensión). Dentro de cada archivo, añade esta línea:

```
donutify
```

Cuando ejecutas `:set filetype?` dentro de `sugardonut`, Vim no sabe qué tipo de archivo asignar a este archivo. Así que devuelve `filetype=`.

En la ruta raíz de los ejecutables de Vim, añade un archivo llamado `scripts.vim` (`~/.vim/scripts.vim`). Dentro del archivo, añade estas líneas:

```
if did_filetype()
  finish
endif

if getline(1) =~ '^\\<donutify\\>'
  setfiletype donut
endif
```

La función `getline(1)` devuelve el texto de la primera línea. Esta comprueba si la primera línea comienza con la palabra "donutify". La función `did_filetype()` es una función propia de Vim. Esta devolverá "true" cuando un evento relacionado con un tipo de archivo se ejecute la primera vez. Es utilizado como medida de seguridad para impedir volver a ejecutar un evento de tipo de archivo.

Abre el archivo `sugardonut` y ejecuta `:set filetype?`, Vim ahora muestra `filetype=donut`. Si abre otro archivo de tipo "donut" (`glazeddonut` y `frieddonut`), Vim también identifica esos archivos como de tipo `donut`.

Ten en cuenta que `scripts.vim` solo es ejecutado cuando Vim abre un archivo con un tipo de archivo desconocido. Si Vim abre un archivo con un tipo de archivo conocido `scripts.vim` no se ejecutará.

## Tipo de archivo complemento

¿Qué ocurre si quieres que Vim ejecute unos *scripts* específicos de archivos *chocodonut* cuando abres un archivo chocodonut y no quieres que ejecute esos *scripts* cuando abres archivos de tipo plaindonut?

Puedes hacer esto con tipos de archivos de complementos en la ruta de los ejecutables de Vim (`~/.vim/ftplugin/`). Vim busca dentro de este directorio un archivo con el mismo nombre que el tipo de archivo que acabas de abrir. Crea un archivo llamado `chocodonut.vim` (`~/.vim/ftplugin/chocodonut.vim`):

```
echo "Mensaje desde chocodonut ftplugin"
```

Crea otro archivo ftplugin, `plaindonut.vim` (`~/.vim/ftplugin/plaindonut.vim`):

```
echo "Mensaje desde plaindonut ftplugin"
```

Ahora cada vez que abras un tipo de archivo chocodonut, Vim ejecuta los *scripts* desde la ruta `~/.vim/ftplugin/chocodonut.vim`. Cada vez que abras un archivo de tipo plaindonut, Vim ejecuta los *scripts* desde la ruta `~/.vim/ftplugin/plaindonut.vim`.

Una advertencia: estos archivos son ejecutados cada vez que un *buffer* de este tipo de archivo se establece (`set filetype=chocodonut` por ejemplo). Si abres 3 archivos diferentes de tipo chocodonut, los *scripts* serán ejecutados un *total* de tres veces.

## Archivos de sangrado de márgenes (*indentado*)

Vim tiene una ruta para ejecutables para el sangrado que funciona de manera similar a ftplugin, donde Vim busca un archivo llamado de igual manera que el tipo de archivo abierto. El propósito de esta ruta de ejecutables de sangrado es almacenar los códigos relacionados con el sangrado. Si tienes el archivo `~/.vim/indent/chocodonut.vim`, este será ejecutado solo cuando abres un archivo de tipo chocodonut. Puedes almacenar códigos relacionados con el sangrado en esta ubicación.

## Colores

Vim tiene una ruta para los ejecutables de los colores (`~/.vim/colors/`) para almacenar los esquemas de colores. Cualquier archivo que este dentro de este directorio será mostrado al ejecutar el comando `:color`.

Si tienes un archivo `~/.vim/colors/beautifulprettycolors.vim`, cuando ejecutas `:color` y presionas la tecla tabulador, verás `beautifulprettycolors` como una de las opciones que puedes escoger con el color. Si prefieres añadir tu propio esquema de color, este es el lugar para hacerlo.

Si quieres echar un vistazo a los esquemas de colores que han hecho otras personas, un buen lugar para visitar es [vimcolors](https://vimcolors.com/).

## Resaltado de sintaxis

Vim tiene una ruta para los ejecutables de sintaxis (`~/.vim/syntax/`) para definir el resaltado de sintaxis.

Supongamos que tenemos el archivo `hello.chocodonut`, dentro del archivo tienes las siguientes expresiones:

```
(donut "sabroso")
(donut "salado")
```

Aunque ahora Vim conoce el tipo de archivo correcto, todos los textos tienen el mismo color. Vamos a añadir una regla de resaltado de sintaxis para resaltar la palabra clave "donut". Crea un nuevo archivo de sintaxis llamado chocodonut, `~/.vim/syntax/chocodonut.vim`. Dentro añade lo siguiente:

```
syntax keyword donutKeyword donut

highlight link donutKeyword Keyword
```

Ahora vuelve a abrir el archivo `hola.chocodonut`. Las palabras claves `donut` ahora están resaltadas.

En este capítulo no entraremos en profundidad sobre el tema de resaltado de sintaxis. Es un tema muy amplio. Si tienes curiosidad, echa un vistazo a `:h syntax.txt`.

El complemento [vim-polyglot](https://github.com/sheerun/vim-polyglot) es un gran complemento que ofrece resaltado para los lenguajes de programación más populares.

## Documentación

si creas un complemento, tendrás que crear tu propia documentación de cómo funciona. Para eso deberás utilizar la ruta de la documentación.

Vamos a crear una documentación básica para las palabras clave chocodonut y plaindonut. Crea un archivo `donut.txt` (`~/.vim/doc/donut.txt`). Y dentro añade los siguientes textos:

```
*chocodonut* Delicioso donut con chocolate

*plaindonut* Sin chocolate, pero aún así sigue siendo delicioso
```

Si tratas de buscar ayuda para `chocodonut` o `plaindonut` (`:h chocodonut` o `:h plaindonut`), no encontrarás nada.

Primero, necesitas ejecutar `:helptags` para generar nuevas entradas de la ayuda. Ejecuta `:helptags ~/.vim/doc/`

Ahora si ejecutas `:h chocodonut` o `:h plaindonut`, encontrarás estas nuevas entradas de la ayuda de Vim. Ten en cuenta que ahora el archivo es de solo lectura y tiene asignado un tipo de archivo de ayuda ("help").

## Lazy Loading Scripts

All of the runtime paths that you learned in this chapter were run automatically. If you want to manually load a script, use the autoload runtime path.

Create an autoload directory (`~/.vim/autoload/`). Inside that directory, create a new file and name it `tasty.vim` (`~/.vim/autoload/tasty.vim`). Inside it:

```
echo "tasty.vim global"

function tasty#donut()
  echo "tasty#donut"
endfunction
```

Note that the function name is `tasty#donut`, not `donut()`. The pound sign (`#`) is required when using the autoload feature. The function naming convention for the autoload feature is:

```
function fileName#functionName()
  ...
endfunction
```

In this case, the file name is `tasty.vim` and the function name is (technically) `donut`.

To invoke a function, you need the `call` command. Let's call that function with `:call tasty#donut()`.

The first time you call the function, you should see *both* echo messages ("tasty.vim global" and "tasty#donut"). The subsequent calls to` tasty#donut` function will only display "testy#donut" echo.

When you open a file in Vim, unlike the previous runtime paths, autoload scripts aren't loaded automatically. Only when you explicitly call `tasty#donut()`, Vim looks for the `tasty.vim` file and loads everything inside it, including the `tasty#donut()` function. Autoload is the perfect mechanism for functions that use extensive resources but you don't use often.

You can add as many nested directories with autoload as you want. If you have the runtime path `~/.vim/autoload/one/two/three/tasty.vim`, you can call the function with `:call one#two#three#tasty#donut()`.

## After Scripts

Vim has an after runtime path (`~/.vim/after/`) that mirrors the structure of `~/.vim/`. Anything in this path is executed last, so developers usually use these paths for script overrides.

For example, if you want to overwrite the scripts from `plugin/chocolate.vim`, you can create `~/.vim/after/plugin/chocolate.vim` to put the override scripts. Vim will run the `~/.vim/after/plugin/chocolate.vim` *after* `~/.vim/plugin/chocolate.vim`.

## $VIMRUNTIME

Vim has an environment variable `$VIMRUNTIME` for default scripts and support files. You can check it out by running `:e $VIMRUNTIME`.

The structure should look familiar. It contains many runtime paths you learned in this chapter.

Recall in Chapter 21, you learned that when you open Vim, it looks for a vimrc files in seven different locations. I said that the last location Vim checks is `$VIMRUNTIME/default.vim`. If Vim fails to find any uservimrc files, Vim uses a `default.vim` as vimrc.

Have you ever tried running Vim without syntax plugin like vim-polyglot and yet your file is still syntatically highlighted? That is because when Vim fails to find a syntax file from the runtime path, Vim looks for a syntax file from `$VIMRUNTIME` syntax directory.

To learn more, check out `:h $VIMRUNTIME`.

## Runtimepath Option

To check your runtimepath, run `:set runtimepath?`

If you use Vim-Plug or popular external plugin managers, it should display a list of directories. For example, mine shows:

```
runtimepath=~/.vim,~/.vim/plugged/vim-signify,~/.vim/plugged/base16-vim,~/.vim/plugged/fzf.vim,~/.vim/plugged/fzf,~/.vim/plugged/vim-gutentags,~/.vim/plugged/tcomment_vim,~/.vim/plugged/emmet-vim,~/.vim/plugged/vim-fugitive,~/.vim/plugged/vim-sensible,~/.vim/plugged/lightline.vim, ...
```

One of the things plugin managers does is adding each plugin into the runtime path. Each runtime path can have its own directory structure similar to `~/.vim/`.

If you have a directory `~/box/of/donuts/` and you want to add that directory to your runtime path, you can add this to your vimrc:

```
set rtp+=$HOME/box/of/donuts/
```

If inside `~/box/of/donuts/`, you have a plugin directory (`~/box/of/donuts/plugin/hello.vim`) and a ftplugin (`~/box/of/donuts/ftplugin/chocodonut.vim`), Vim will run all scripts from `plugin/hello.vim` when you open Vim. Vim will also run `ftplugin/chocodonut.vim` when you open a chocodonut file.

Try this yourself: create an arbitrary path and add it to your runtimepath. Add some of the runtime paths you learned from this chapter. Make sure they work as expected.

## Learn Runtime The Smart Way

Take your time reading it and play around with these runtime paths. To see how runtime paths are being used in the wild, go to the repository of one of your favorite Vim plugins and study its directory structure. You should be able to understand most of them now. Try to follow along and discern the big picture. Now that you understand Vim directory structure, you're ready to learn Vimscript.
