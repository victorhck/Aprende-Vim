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

### File Type Script

Let's learn how to detect and assign a file type based on the file content.

Suppose you have a collection of files without an agreeable extension. The only thing these files have in common is that they all start with the word "donutify" on the first line. You want to assign these files to a `donut` file type. Create new files named `sugardonut`, `glazeddonut`, and `frieddonut` (without extension). Inside each file, add this line:

```
donutify
```

When you run the `:set filetype?` from inside `sugardonut`, Vim doesn't know what file type to assign this file with. It returns `filetype=`.

In the runtime root path, add a `scripts.vim` file (`~/.vim/scripts.vim`). Inside it, add these:

```
if did_filetype()
  finish
endif

if getline(1) =~ '^\\<donutify\\>'
  setfiletype donut
endif
```

The function `getline(1)` returns the text on the first line. It checks if the first line starts with the word "donutify". The function `did_filetype()` is a Vim built-in function. It will return true when a file type related event is triggered at least once. It is used as a guard to stop re-running file type event.

Open the `sugardonut` file and run `:set filetype?`, Vim now returns `filetype=donut`. If you open another donut files (`glazeddonut` and `frieddonut`), Vim also identifies their file types as `donut` types.

Note that `scripts.vim` is only run when Vim opens a file with an unknown file type. If Vim opens a file with a known file type, `scripts.vim` won't run.

## File Type Plugin

What if you want Vim to run chocodonut-specific scripts when you open a chocodonut file and to not run those scripts when opening plaindonut file?

You can do this with file type plugin runtime path (`~/.vim/ftplugin/`). Vim looks inside this directory for a file with the same name as the file type you just opened. Create a `chocodonut.vim` (`~/.vim/ftplugin/chocodonut.vim`):

```
echo "Calling from chocodonut ftplugin"
```

Create another ftplugin file, `plaindonut.vim` (`~/.vim/ftplugin/plaindonut.vim`):

```
echo "Calling from plaindonut ftplugin"
```

Now each time you open a chocodonut file type, Vim runs the scripts from `~/.vim/ftplugin/chocodonut.vim`. Each time you open a plaindonut file type, Vim runs the scripts from `~/.vim/ftplugin/plaindonut.vim`.

One warning: these files are run each time a buffer file type is set (`set filetype=chocodonut` for example). If you open 3 different chocodonut files, the scripts will be run a *total* of three times.

## Indent Files

Vim has an indent runtime path that works similar to ftplugin, where Vim looks for a file named the same as the opened file type. The purpose of these indent runtime paths is to store indent-related codes. If you the file `~/.vim/indent/chocodonut.vim`, it will be executed only when you open a chocodonut file type. You can store indent-related codes for chocodonut files here.

## Colors

Vim has a colors runtime path (`~/.vim/colors/`) to store color schemes. Any file that goes inside the directory will be displayed in the `:color` command-line command.

If you have a `~/.vim/colors/beautifulprettycolors.vim` file, when you run `:color` and press tab, you will see `beautifulprettycolors` as one of the color options. If you prefer to add your own color scheme, this is the place to go.

If you want to check out the color schemes other people made, a good place to visit is [vimcolors](https://vimcolors.com/).

## Syntax Highlighting

Vim has a syntax runtime path (`~/.vim/syntax/`) to define syntax highlighting.

Suppose you have a `hello.chocodonut` file, inside it you have the following expressions:

```
(donut "tasty")
(donut "savory")
```

Although Vim now knows the correct file type, all texts have the same color. Let's add a syntax highlighting rule to highlight the "donut" keyword. Create a new chocodonut syntax file, `~/.vim/syntax/chocodonut.vim`. Inside it add:

```
syntax keyword donutKeyword donut

highlight link donutKeyword Keyword
```

Now reopen `hello.chocodonut` file. The `donut` keywords are now highlighted.

This chapter won't go over syntax highlighting in depth. It is a vast topic. If you are curious, check out `:h syntax.txt`.

The [vim-polyglot](https://github.com/sheerun/vim-polyglot) plugin is a great plugin that provides highlights for many popular programming languages.

## Documentation

If you create a plugin, you will have to create your own documentation. You use the doc runtime path for that.

Let's create a basic documentation for chocodonut and plaindonut keywords. Create a `donut.txt` (`~/.vim/doc/donut.txt`). Inside, add these texts:

```
*chocodonut* Delicious chocolate donut

*plaindonut* No choco goodness but still delicious nonetheless
```

If you try to search for `chocodonut` and `plaindonut` (`:h chocodonut` and `:h plaindonut`), you won't find anything.

First, you need to run `:helptags` to generate new help entries. Run `:helptags ~/.vim/doc/`

Now if you run `:h chocodonut` and `:h plaindonut`, you will find these new help entries. Notice that the file is now read-only and has a "help" file type.

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
