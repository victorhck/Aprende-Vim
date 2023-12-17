# Capítulo 22: Buscar dentro de archivos con Vimgrep

Al comenzar a utilizar Vim, quizás eches en falta un buscador propio de archivos potente que tienen otros entornos de edición o editores. Te vienen a la mente preguntas similares a: "¿Cómo podría buscar todos los archivos que contienen la palabra _echo_? ¿Cómo podría buscar la cadena de texto _const_ solo en los archivos de extensión `.js`?"

Pues tengo buenas noticias para ti. Resulta que en Vim de hecho ya viene integrado un potente buscador en archivos listo para ser utilizado.

Hay dos manera de realizar búsquedas en archivos en Vim: `:grep` y `:vimgrep`. La primera utiliza el comando externo `grep` y la segunda es la propia herramienta que incluye Vim. En este artículo vamos a ver cómo utilizar el comando de Vim `:vimgrep`.

## ¿Por qué aprender el uso de Vimgrep?

La habilidad para realizar búsquedas de manera rápida y eficiente puede mejorar tu productividad. 

Es cierto que para Vim hay disponibles un montón de complementos muy útiles que realizan la tarea de manera cómoda, algunos de esos complementos son ctrlp, denite o fzf.vim. Estos complementos son realmente muy buenas herramientas, entonces ¿por qué tengo que aprender a usar vimgrep?

Simplemente porque esta utilidad propia de Vim no sea reciente, no quiere decir que no sea buena. Hay unas cuantas ventajas al aprender a utilizar vimgrep.

La primera es que el comando `:vimgrep` está integrado en Vim, por lo que no tendrás que preocuparte de instalar dependencias, ni tener que lidiar con los problemas que a veces deriva de eso. Si alguna vez has tenido que utilizar un Vim sin los complementos a los que estás acostumbrado (por ejemplo si estás en una sesión SSH en un servidor remoto, o utilizando el equipo de otra persona o quizás desde un dispositivo móvil, etc) en esos casos puedes estar seguro que `:vimgrep` siempre estará allí instalado listo para ser usado.

Segundo, vimgrep utiliza el motor de expresiones regulares (_regex_) propio de Vim. Esto puede parecer un contra más que una ventaja para algunas personas, pero ya verás como cambias de opinión cuanto más uses Vim y vimgrep. Al utilizar el mismo motor de expresiones regulares que Vim, esto significa que no hay diferencias al utilizar la búsqueda de Vim (`/`) y al utilizar `:vimgrep`. Tu cerebro no tendrá que cambiar a un modo diferente. Cuanto menos tengas que utilizar tu materia gris para editar, más la podrás utilizar para cosas más divertidas e interesantes.

Pero vimgrep tiene algunas desventajas. La mayor es que al realizar búsquedas las carga todas en la memoria, por lo que si tienes un resultado grande de la búsqueda, esto puede ralentizar el uso de Vim. Sin embargo, si trabajas en proyectos medios, es lo suficientemente potente.

## Uso básico

El comando a utilizar sería `:vimgrep` pero en este artículo utilizaremos la opción corta que es `:vim`. Sí, vim dentro de Vim

El comando `:vim` se utiliza siguiendo la siguiente sintaxis:

```
:vim /patrón/indicador ruta
```

-   `patrón` es el patrón de tu búsqueda.
-   `indicador` es un indicador que se le puede pasar al patrón de búsqueda.
-   `ruta` es el argumento que indica donde buscar. Se le pueden pasar múltiples argumentos.

Bueno, visto lo básico, vamos a aprender con ejemplos a utilizar vimgrep.

## Buscar una cadena de texto dentro de un archivo en particular

Si necesitas encontrar la cadena de texto "hola" dentro de todos los archivo de ruby con extensión .rb dentro de la carpeta app/controllers/ , ejecuta el siguiente comando:  

```
:vim /hola/ app/controllers/**/*.rb
```

Utilizo mucho los comodines `*` y `**`. El comodín, `**`, busca de manera recursiva, es decir dentro de por ejemplo estas carpetas: `app/controllers/dir/`, `app/controllers/otro/dir/`, `app/controllers/archivo`). El comodín, `*`, reemplaza una cadena de cualquier longitud de caracteres. En este caso `*.rb` reemplaza a cualquier cadena de texto que termine con `.rb` (como `hola_controller.rb`, `loquesea.rb`).

Por cierto, no confundas los comodines con los asteriscos en las expresiones regulares. Son dos cosas diferentes. El patrón del asterisco en las expresiones regulares significa cero o más del patrón siguiente (por ejemplo: `a*` significa ninguna o más "a"), mientras que el comodín no necesita de un patrón a continuación (`*` significa cualquier cadena de texto de cualquier longitud).

El resultado de la búsqueda de `:vim` se mostrará en una ventana _quickfix_. Si no estás familiarizado con esto, por ahora piensa en ello como en un conjunto de elementos. En este caso en particular, en un conjunto de resultados de la búsqueda.

Después de ejecutar el comando de búsqueda, ejecuta el comando `:copen` para abrir la ventana _quickfix_.

Si no conoces esta ventana de Vim, para empezar a manejarte con ella, estos son los comandos de _quickfix_ más útiles y que vamos a utilizar más a menudo:  

```
:copen        Abre la ventana _quickfix_
:cclose       Cierra la ventana _quickfix_
:cnext        Va a la ubicación siguiente
:cprevious    Va a la ubicación previa
```

Es una explicación muy básica, pero suficiente para aprender lo básico sin perder el hilo de vimgrep. Para aprender más sobre _quickfix_ puedes usar la ayuda de Vim, escribiendo `:h quickfix`.

## Buscar una cadena de texto dentro de una extensión particular de archivo

Si necesitas buscar la cadena "echo" pero solo dentro de los archivos con extensión `.sh`:

```
:vim /echo/ **sh
```

El comodín anterior del doble asterisco puede resultar muy útil a la hora de buscar dentro de una estructura de archivos anidada. Al utilizar `**` encontrará la coincidencia tanto en `./un_archivo.sh` como en  `./una/ruta/muy/larga/hasta/el_archivo.sh`.

Si solo quieres buscar en archivos que estén en la misma carpeta (como  `./hey.sh` o `./un_rchivo.sh`, pero no en  `./una/ruta/muy/larga/hasta/el_archivo.sh`), en vez de usar el comodín del doble asterisco, utiliza el asterisco simple `*sh`.

## Buscar una cadena de texto dentro de archivos que terminan con una extensión en particular

Si necesitas buscar la cadena de texto "echo" pero solo dentro de los archivos que acaben en `.sh` o en `.rb`:

```
:vim /echo/ **{sh,rb}
```

## Pasar múltiples archivos dentro de los que buscar

Recuerda que al principio comenté que vimgrep acepta múltiple argumentos en los archivos. Hasta ahora solo hemos estado utilizando un argumento. Sin embargo, podemos pasarle más de un argumento.

Si necesitas buscar la cadena "echo" dentro de `.sh` o `.rb`:  

```
:vim /echo/ **sh **rb
```

Si necesitas buscar la cadena "echo" dentro de `app/controllers/` o `Rakefile`:  

```
:vim /echo/ app/controllers/** Rakefile
```

No estás limitado a solo dos argumentos de archivos, puedes pasarle al comando tantos argumentos como desees. Si necesitas buscar "echo" dentro del directorio `app/controllers/` , en `Rakefile` en el directorio actual, en el archivo `.json` en el directorio actual y en el archivo `sh` dentro de alguna parte del directorio `bin/` de tu sistema:

```
:vim /echo/ app/controllers/** Rakefile *.json bin/**/*sh
```

## Encontrar múltiples coincidencias en la misma línea del archivo

Cuando ejecutamos `:vim /echo/ app/controllers/**`, Vim mostró la primera coincidencia del texto de cada línea del texto que encontró.

Esto significa que si tenemos una línea que contiene la palabra buscada varias veces, como `echo "me gusta el echo echo echo"`, el resultado de la búsqueda solo mostrará un resultado en vez de cuatro. Al utilizar el patrón `/echo/` este solo busca la primera coincidencia del patrón en esa línea.

¿Qué pasa si queremos que nuestra búsqueda muestre en los resultados las cuatro coincidencias dentro de la misma línea?

Para encontrar todas las cadenas de texto de "echo" aunque estén varias en la misma línea, necesitamos utilizar y añadir el indicador global `g`, el mismo que utilizamos al realizar las búsquedas dentro del texto con `/`.  

```
:vim /echo/g app/controllers/**
```

Ahora encontrará todas las coincidencias de la cadena de texto buscada.

Es muy normal añadir este indicador a las búsquedas para que realice una búsqueda completa.

## Búsqueda difusa de una cadena dentro de un archivo en particular

Vimgrep también es capaz de realizar una búsqueda difusa. Solo debemos pasarle el indicador `f`. Para realizar una búsqueda difusa de la cadena "echo" dentro de un archivo `.sh` utilizaremos lo siguiente:

```
:vim /echo/fg  **sh
```

Esto buscará de manera difusa el patrón dentro de `**sh` y mostrará todas las que se _parezcan_ a "echo".

¿En qué se diferencia la búsqueda difusa de vimgrep de una búsqueda normal? Además de encontrar la cadena de texto literal "echo", también encontraría algo similar a: `puts "Checking Homebrew..."` porque la cadena de texto "ec" en "Checking" y "Ho" en "Homebrew" forman la cadena que buscamos "echo".

## Buscar un patrón de una expresión regular en un archivo en particular

Vimgrep permite el uso de expresiones regulares en el patrón del término a buscar. Si quieres buscar tanto "echo" o "ecko" dentro de un archivo de extensión `.sh` :  

```
:vim /ec[hk]o/g **sh
```

El `[]` en `[hk]` es un caracter que marca la sintaxis. En este caso encontrará la palabra que contenga tanto "h" como "k".

Si necesitas buscar un texto que esté entre comillas simples (como `'hello'`, o `'foo'`, o `'1234'`) dentro de un archivo `.sh`:  

```
:vim /'[^']*'/g **sh
```

Este es un pequeño ejemplo de lo que se puede conseguir utilizando las expresiones regulares en Vim.

## Buscar una cadena de texto dentro de un directorio en particular

El comodín de doble asterisco `**` o el comodín simple pueden usarse poniéndolo al principio, en el medio o al final de la ruta que queremos. También puede utilizarse varias veces en el patrón del archivo.

Si quieres buscar "echo" dentro del directorio `/controllers/`:  

```
:vim /echo/g **/controllers/**
```

Si quieres buscar "echo" dentro del directorio `/controllers/` y a su vez dentro de un archivo que empieza por "shipment" y termina con "rb" (por ejemplo: `shipment_domestic.rb`, `shipment_incoming.rb`):  

```
:vim /echo/g **/controllers/**/shipment*rb
```

## Buscar una cadena de texto dentro de un directorio diferente al directorio actual de trabajo

Vimgrep busca dentro de tu directorio de trabajo. Pero si necesitas buscar la cadena de texto "echo" ¿cómo lo harías? Fácil. Simplemente muévete a otro directorio y realiza allí la búsqueda.

Vim tiene un comando `:cd` que cambia de directorio en el que estás actualmente.  

```
:cd somewhere/else
:vim /echo/g **js
```

Cuando termines, simplemente vuelve con `:cd` al directorio previo.

## Buscar en el archivo actual

Puedes utilizar el atajo de Vim en el patrón de búsqueda. En Vim `%` representa el _buffer_ actual activo, es decir, el archivo en el que actualmente te encuentras.

Si necesitas buscar "echo" en el archivo actual:  

```
:vim /echo/g %
```

Puedes leer más sobre este atajo en la ayuda de Vim: `:help :\_%`

## [](https://dev.to/iggredible/vimgrep-tips-and-tricks-54pl#using-other-search-commands)Using Other Search Commands

There are times when we need to perform a more advanced search. We may need to use other commands, like `find`. No problem. To search for "echo" inside all files whose names start with "docker" using the `find` command:  

```
:vim /echo/g `find . -type f -name 'docker*'
```

For more on how `find` works, check out `man find`.

The `git ls-files` is another useful shell command for git-related searches. Assuming that you are inside a git repository, to search for "echo" inside of all modified files only:  

```
:vim /echo/g `git ls-files --modified`
```

For more on how `git ls-files` works, check out `man git-ls-files`.

## [](https://dev.to/iggredible/vimgrep-tips-and-tricks-54pl#searching-infiles-within-arglist)Searching in-Files Within Arglist

The argument list (`arglist`) is a Vim feature wherein Vim stores a list of files.

The gist of arglist is, if you open Vim with multiple files (ex: `vim file1.js file2.rb file3.py`), Vim collects these files inside the arglist. To see them, run `:args`.

Arglist does not necessarily have to be populated on start too. You can create your own arglist while in Vim by running `:args file1.js file2.rb file3.py`. To see them, run `:args`.

Once you have a list of files, you can quickly go to the next or previous arglist files with `:next` or `:prev`.

Ok, so arglist is a collection of files. So how does arglist relate to vimgrep?

Usually when performing a task, you would gather all the relevant files first. Once you have all your relevant files in a collection, you can very quickly navigate between them. Arglist is a very useful feature for that.

Vim has a number of file expansion shortcuts. Just like how `%` represents the current buffer, Vim has one for arglist too! `##` represents the current arglist.

So if we want to search within our arglist files for the string "echo":  

Why is this useful? I mean, it looks like I'm adding an extra step to the search process: first, I need to gather the files to an arglist and second, I run the vimgrep command. That's two steps altogether. Why can't I just run `:vim /echo/g file1.js file2.rb file3.py` and be done with only one step?

Speaking from experience, often I find myself needing to perform multiple keyword searches within the same set of files, like:  

```
:vim /echo1/g file1.js file2.rb file3.py
:vim /foo2/g file1.js file2.rb file3.py
:vim /bar3/g file1.js file2.rb file3.py
```

I find it painful each time I have to re-type the same set of files. This is where arglist can save time!

Why not collect an arglist first:  

```
:arglist file1.js file2.rb file3.py
```

Then reuse it in subsequent vimgrep searches?

Now I can just run:  

```
:vim /echo1/g ##
:vim /foo2/g ##
:vim /bar3/g ##
```

If you need to perform different keyword searches against the same set of files, arglist can save you time.

If you're curious about how `##` works, check out `:h :_##`. If you want to learn more arglist, check out `:h :arglist`.

## [](https://dev.to/iggredible/vimgrep-tips-and-tricks-54pl#quickly-get-the-last-search-pattern)Quickly Get the Last Search Pattern

Sometimes I need to search for a complicated pattern. Before I enter that in the `:vim` command, I like to test it with search (`/`) first.

For example, if you want to search for a string surrounded by a single quote, while _excluding_ the single quotes, you could do `'\zs[^']*\ze'`. But this pattern may not be intuitive at first. When using a semi-complicated pattern, I like to test if it does what I think it does, so before I run `:vim /'\zs[^']*\ze'/g **sh`, I would usually do a quick search command `/'\zs[^']*\ze'` to test if it works.

Once I confirm that the pattern meets my expectations, I would then enter it to the `:vim` command. But do I really want to re-type `'\zs[^']*\ze'` all over again? I mean, look at those brackets and single quotes and backslashes... I could easily mistype them when I am typing them on the `:vim` command.

Moreover, what if my pattern is a lot longer, like `\(["']\)\(\%(\1\@!.\)*\)\1`? Oh boy, look at those backslashes, parentheses, and brackets... what are the chances of me retyping that correctly in the first try?

Luckily, there is a trick that allows you to "paste" your most recently used search command. After typing `:vim /`, type `Ctrl-r` then `/`. The secret is that `Ctrl-r` when used in insert mode (or in this case, command-line mode) invokes the Vim registers. Here we ask Vim for the value from the search register (`/`).

This is where the `:vim` command has an advantage over the regular `:grep` command. The search command (`/`) uses Vim regex flavor, which `:vim` also uses. But the `:grep` command doesn't use Vim regex flavor (it uses whatever grep external command you set up; for more, check out `:h 'grepprg'`). Using the same regex flavor for `/` then `:vim` command means zero friction and a buttery-smooth search experience!

Albeit, performance-wise, `:grep` is faster, but if speed is not a big issue (it isn't really noticeable in most cases), `:vim` offers a better user experience.

## [](https://dev.to/iggredible/vimgrep-tips-and-tricks-54pl#conclusion)Conclusion

This is a good place to stop. You've learned a number of cool tricks for the `:vim` program. I wish I knew half of these tricks when I started Vim. Don't just speed read this and leave. Take your time going through each command. Go through each one of them - tweak it, break it, and understand it. Make it your goal to be able to perform them without much mental effort. This is by no means a comprehensive list of `:vimgrep` tips and tricks. There are so many other combinations that you can do with vimgrep. Don't stop learning!

Vim is a great editor even without plugins. Vim is a universal program that can be found/installed in practically any machine. There may be times when you can't use search plugins. This will be the time where your vanilla Vim knowledge will shine. Don't let the lack of plugins cripple your Vim productivity. Learn to use Vim with and without plugins. Learn `:vimgrep` and also learn `:grep`.

Happy Vimming!
