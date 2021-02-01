# Capítulo 16: Etiquetas

Una característica muy útil a la hora de editar texto es poder ser capaz de ir a cualquier definición rápidamente. En este capítulo aprenderás cómo utilizar las etiquetas de Vim para hacer eso.

## Descripción general de las etiquetas

Supongamos que alguien te ha enviado un nuevo código, por ejemplo:

```
one = One.new
one.donut
```

¿`One`? ¿`donut`? Bueno, estos nombre podrían ser obvios para el desarrollador o desarrolladora que escribieran el código hace tiempo, pero ahora esas personas ya no están aquí y te corresponde a tí entender estas líneas de código tan enrevesadas. Una manera de ayudar a entender esto es seguir el código fuente donde `One` y `donut` están definidas.

Puedes buscarlas tanto con `fzf` como con `grep`, pero es más rápido utilizar etiquetas.

Piensa en que las etiquetas son como una libreta de direcciones de contactos:

```
Nombre   Direcciones
Iggy1    1234 Calle felicidad, 11111
Iggy2    9876 Avenida alegría, 2222
```

En vez de tener un par de nombres y direcciones, las etiquetas almacenan las definiciones emparejadas con direcciones.

Vamos a asumir que tenemos estos dos archivos escritos en lenguaje Ruby que están guardados dentro del mismo directorio:

```
# one.rb
class One
  def initialize
    puts "Initialized"
  end

  def donut
    puts "Bar"
  end
end
```

y

```
# two.rb
require './one'

one = One.new
one.donut
```

Para saltar a una definición, puedes utilizar la combinación de teclas `Ctrl-]` estando en el modo normal. Dentro del archivo `two.rb`, ve a la línea donde aparece `one.donut` y posiciona el cursor encima de la palabra `donut`. Pulsa `Ctrl-]`.

¡Vaya! Vim no puede encontrar el archivo de etiquetas. Necesitas primero generar el archivo de etiquetas.

## Generador de etiquetas

El Vim actual no trae incorporado un generador de etiquetas, así que tendrás que descargar un generador de etiquetas externo. Hay varias opciones entre las que escoger:

- ctags = solo para C. disponible casi para todas las plataformas.
- exuberant ctags = Uno de los más populares. Admite muchos lenguajes. 
- universal ctags = Similar a ctags, pero más moderno.
- etags = Para Emacs. Hmm...
- JTags = Java
- ptags.py = Python
- ptags = Perl
- gnatxref = Ada

Si echas un vistazo a tutoriales de Vim en internet, muchos recomendarán [Exuberant ctags](http://ctags.sourceforge.net/). Admite [41 lenguajes de programación](http://ctags.sourceforge.net/languages.html). Yo lo utilizaba y funcionaba genial. Sin embargo, debido a que ya no está mantenido desde 2009, **Universal ctags** sería una opción más adecuada. Funciona de manera similar a **Exuberant ctags** y actualmente está mantenido.

No entraré en detalles de cómo instalar ctags. Echa un vistazo al repositorio de [universal ctags](https://github.com/universal-ctags/ctags) para consultar las instrucciones. Una vez que tengas ctags instalado, ejecuta `ctags --version`. Esto debería mostrar:

```
Universal Ctags 0.0.0(b43eb39), Copyright (C) 2015 Universal Ctags Team
Universal Ctags is derived from Exuberant Ctags.
Exuberant Ctags 5.8, Copyright (C) 1996-2009 Darren Hiebert
```

Asegúrate de que se muestra "`Universal Ctags`".

A continuación, vamos a generar un archivo de etiquetas básico. Ejecuta lo siguiente:

```
ctags -R .
```

La opción `R` le dice a `ctags` que ejecute un escaneo desde tu ubicación actual (`.`). Ahora se debería poder ver un archivo `tags` en tu directorio actual. Dentro del archivo verás algo parecido a esto:

```
!_TAG_FILE_FORMAT	2	/extended format; --format=1 will not append ;" to lines/
!_TAG_FILE_SORTED	1	/0=unsorted, 1=sorted, 2=foldcase/
!_TAG_OUTPUT_FILESEP	slash	/slash or backslash/
!_TAG_OUTPUT_MODE	u-ctags	/u-ctags or e-ctags/
!_TAG_PATTERN_LENGTH_LIMIT	96	/0 for no limit/
!_TAG_PROGRAM_AUTHOR	Universal Ctags Team	//
!_TAG_PROGRAM_NAME	Universal Ctags	/Derived from Exuberant Ctags/
!_TAG_PROGRAM_URL	<https://ctags.io/>	/official site/
!_TAG_PROGRAM_VERSION	0.0.0	/b43eb39/
One	one.rb	/^class One$/;"	c
donut	one.rb	/^  def donut$/;"	f	class:One
initialize	one.rb	/^  def initialize$/;"	f	class:One
```

El contenido de tu archivo puede ser algo diferente, dependiendo de los ajustes de tu Vim y del generador ctags. Una etiqueta está compuesta por dos partes: los metadatos de la etiqueta y la lista de etiquetas. Estos metadatos (`!TAG_FILE...`) son normalmente controlados por el generador ctags. No entraré en detalle sobre eso aquí, pero ¡no dudes en echasr un vistazo a su documentación!

Ahora ve al archivo `two.rb`, y coloca el cursor encima de la palabra `donut`, y escribe `Ctrl-]`. Vim te llevará al archivo `one.rb` a la línea donde está `def donut`. ¡Eureka! ¿Pero cómo hizo esto Vim?

## Antatomía de las etiquetas

Echemos un vistazo a la etiqueta del elemento `donut`:

```
donut	one.rb	/^  def donut$/;"	f	class:One
```

El elemento de la etiqueta está compuesto por cuatro componentes: un nombre de etiqueta o `tagname`, un nombre de archivo de la etiqueta o `tagfile`, una dirección de etiqueta o `tagaddress` y opciones de la etiqueta. (N. del T: Me referiré por sus nombre en inglés porque quizás es la nomenclatura más utilizada en los tutoriales y por quienes lo usan).

- `donut` es el `tagname`. Cuando tu cursor está encima de la palabra "donut", Vim busca el archivo dentro del archivo de la etiqueta una línea que contenga la cadena "donut".
- `one.rb` es el `tagfile`. Vim busca un archivo llamado `one.rb`.
- `/^ def donut$/` es el `tagaddress`. `/.../` es un indicador patrón. `^` es un patrón para el primer elemento de una línea. Está seguido por dos espacios y después la cadena `def donut`. Finalmente, `$` es el patrón para el último elemento de una línea.
- `f class:One` es la opción de la etiqueta que le dice a Vim que la función `donut` es una función (`f`) y es parte de la clase `One`.

Echemos un vistazo de otro elemento de la lista de etiquetas:

```
One	one.rb	/^class One$/;"	c
```

Esta línea funciona de la misma manerá que el patrón `donut`:

- `One` es el `tagname`. Ten en cuenta que con las etiquetas, el primer escaneo tiene en cuenta las mayúsculas y minúsculas. Si tienes `One` y `one` en la lista, Vim priorizará `One` sobre `one`.
- `one.rb` es el `tagfile`. Vim buscará un archivo `one.rb`.
- `/^class One$/` es el patrón de `tagaddress`. Vim buscará una línea que comience (`^`) con `class` y termine (`$`) con `One`.
- `c` es una de las opciones de etiqueta posible. Como `One` es una clase de Ruby y no un procedimiento, lo marca con una `c`.

Dependiendo de qué generador de etiquetas utilices, el contenido de tu archivo de etiquetas puede tener un aspecto algo diferente. Como mínimo, un archivo de etiquetas debe tener uno de estos formatos:

```
1.  {tagname} {TAB} {tagfile} {TAB} {tagaddress}
2.  {tagname} {TAB} {tagfile} {TAB} {tagaddress} {término} {campo} ..
```

## El archivo de etiquetas

Has aprendido que un nuevo archivo, `tags`, es creado después de ejecutar `ctags -R .`. ¿Cómo sabe Vim dónde buscar el archivo de etiquetas?

Si ejecutas `:set tags?`, podrás ver `tags=./tags,tags` (dependiendo de tus ajustes de Vim, esto podría ser diferente). Aquí Vim busca todas las etiquetas en la ruta del archivo actual para el caso de `./tags` y dentro del directorio actual (la raíz de tu proyecto) para el caso de `tags`.

También en el caso de `./tags`, Vim primero buscará un archivo de etiquetas dentro de la ruta de tu archivo actual independientemente de lo anidado que esté, después buscará un archivo de etiquetas del directorio actual (la raíz del proyecto). Vim detiene la búsqueda después de encontrar la primera coincidencia.

Si tu archivo `'tags'` ha dicho `tags=./tags,tags,/user/iggy/mytags/tags`, entonces Vim también buscará en el directorio `/user/iggy/mytags` un archivo de etiquetas después de finalizar la búsqueda en los directorios `./tags` y `tags`. No es necesario que almacenes tu archivo de etiquetas dentro de tu proyecto, puedes mantenerlo de manera separada.

Para añadir una ubicación de un archivo de etiquetas, simplemente ejecuta:

```
:set tags+=ruta/a/mi/archivode/etiquetas
```

## Generating Tags For a Large Project.

If you tried to run ctags in a large project, it may take a long time because Vim also looks inside every nested directories. If you are a Javascript developer, you know that `node_modules` can be very large. Imagine if you have a five sub-projects and each contains its own `node_modules` directory. If you run `ctags -R .`, ctags will try to scan through all 5 `node_modules`. You probably don't need to run ctags on `node_modules`.

To run ctags excluding the `node_modules`, run:

```
 ctags -R --exclude=node_modules .
```

This time it should take less than a second. By the way, you can use the `exclude` option multiple times:

```
ctags -R --exclude=.git --exclude=vendor --exclude=node_modules --exclude=db --exclude=log .
```

## Tags Navigation

You can get good milage using only `Ctrl-]`, but let's learn a few more tricks. The tag jump key `Ctrl-]` has an command-line mode alternative: `:tag my-tag`. If you run:

```
:tag donut
```

Vim will jump to the `donut` method, just like doing `Ctrl-]` on "donut" string. You can autocomplete the argument too, with `<Tab>`:

```
:tag d<Tab>
```

Vim lists all tags that starts with "d". In this case, "donut".

In a real project, you may encounter multiple methods with the same name. Let's update the two files. Inside `one.rb`:

```
# one.rb
class One
  def initialize
    puts "Initialized"
  end

  def donut 
    puts "one donut"
  end

  def pancake
    puts "one pancake"
  end
end
```

And `two.rb`:

```
# two.rb
require './one.rb'

def pancake
  "Two pancakes"
end

one = One.new
one.donut
puts pancake
```

If you are coding along, don't forget to run `ctags -R .` again since you now have several new procedures. You have two instances of the `pancake` procedure. If you are inside `two.rb` and you pressed `Ctrl-]`, what would happen?

Vim will jump to `def pancake` inside `two.rb`, not the `def pancake` inside `one.rb`. This is because Vim sees the `pancake` procedure inside `two.rb` as having a higher priority than the other `pancake` procedure.

## Tag Priority

Not all tags are equal. Some tags have higher priorities. If Vim is presented with duplicate item names, Vim checks the priority of the keyword. The order is:

1. A fully matched static tag in the current file.
2. A fully matched global tag in the current file.
3. A fully matched global tag in a different file.
4. A fully matched static tag in another file.
5. A case-insensitively matched static tag in the current file.
6. A case-insensitively matched global tag in the current file.
7. A case-insensitively matched global tag in the a different file.
8. A case-insensitively matched static tag in the current file.

According to the priority list, Vim prioritizes the exact match found on the same file. That's why Vim chooses the `pancake` procedure inside `two.rb` over the `pancake` procedure inside `one.rb`. There are some exceptions to the priority list above depending on your `'tagcase'`, `'ignorecase'`, and `'smartcase'` settings, but I will not discuss them here. If you are interested, check out `:h tag-priority`.

## Selective Tag Jumps

It would be nice if you can choose which tag items to jump to instead of always going to the highest priority tag item. Maybe you actually need to jump to the `pancake` method in `one.rb` and not the one in `two.rb`. To do that, you can use `:tselect`. Run:

```
:tselect pancake
```

You will see, on the bottom of the screen:

```
# pri kind tag               file
1 F C f    pancake           two.rb
             def pancake
2 F   f    pancake           one.rb
             class:One
             def pancake
```

If you type `2` then `<Return>`, Vim will jump to the procedure in `one.rb`. If you type `1` then `<Return>`, Vim will jump to the procedure in `two.rb`.

Pay attention to the `pri` column. You have `F C` on the first match and `F` on the second match. This is what Vim uses to determine the tag priotity. `F C` means a fully-matched (`F`) global tag in the current (`C`) file. `F` means only a fully-matched (`F`) global tag. `F C` always have a higher priority than `F`.

If you run `:tselect donut`, Vim also prompts you to select which tag item to jump to, even though there is only one option to choose from. Is there a way for Vim to prompt the tag list only if there are multiple matches and to jump immediately if there is only one tag found?

Of course! Vim has a `:tjump` method. Run:

```
:tjump donut
```

Vim will immediately jump to the `donut` procedure in `one.rb`, much like running `:tag donut`. Now run:

```
:tjump pancake
```

Vim will prompt you tag options to choose from, much like running `:tselect pancake`. With `tjump` You get the best of both methods.

Vim has a normal mode key for `tjump`: `g Ctrl-]`. I personally like `g Ctrl-]` better than `Ctrl-]`.

## Autocompletion With Tags

Tags can assist autocompletions. Recall from chapter 6, Insert Mode, that you can use `Ctrl-X` sub-mode to do various autocompletions. One autocompletion sub-mode that I did not mention was `Ctrl-]`. If you do `Ctrl-X Ctrl-]` while in the insert mode, Vim will use the tag file for autocompletion.

If you go into the insert mode and type `Ctrl-X Ctrl-]`, you will see:

```
One
donut
initialize
pancake
```

## Tag Stack

Vim keeps a list of all the tags you have jumped to and from in a tag stack. You can see this stack with `:tags`. If you had first tag-jumped to `pancake`, followed by `donut`, and run `:tags`, you will see:

```
  # TO tag         FROM line  in file/text
  1  1 pancake            10  ch16_tags/two.rb
  2  1 donut               9  ch16_tags/two.rb
>
```

Note the `>` symbol above. It shows your current position in the stack. To "pop" the stack to go back to one previous stack, you can run `:pop`. Try it, then run `:tags` again:

```
  # TO tag         FROM line  in file/text
  1  1 pancake            10  puts pancake
> 2  1 donut               9  one.donut

```

Note that the `>` symbol is now on line two, where the `donut` is. `pop` one more time, then run `:tags` again:

```
  # TO tag         FROM line  in file/text
> 1  1 pancake            10  puts pancake
  2  1 donut               9  one.donut
```

In normal mode, you can run `Ctrl-T` to achieve the same effect as `:pop`.

## Automatic Tag Generation

One of the biggest drawbacks of Vim tags is that each time you make a significant change, you have to regenerate the tag file. If you recently renamed the `pancake` procedure to the `waffle` procedure, the tag file did not know that the `pancake` procedure had been renamed. It still stored `pancake` in the list of tags. You have to run `ctags -R .` to create an updated tag file. Recreating a new tag file can be cumbersome.

Luckily there are several methods you can employ to generate tags automatically. My goal in this section is not to create a foolproof process, but to suggest some ideas so you can expand them.

### Generate a Tag on Save

Vim has an autocommand (`autocmd`) method to execute any command on an event trigger. You can use this to generate tags on each save. Run:

```
:autocmd BufWritePost *.rb silent !ctags -R .
```

Breakdown:

- `autocmd` is Vim's autocommand method. It accepts an event name, file pattern, and a command.
- `BufWritePost` is an event for saving a buffer. Each time you save a file, you trigger a `BufWritePost` event.
- `.rb` is a file pattern for ruby (`rb`) files.
- `silent` is actually part of the command you are passing. Without this, Vim will display `press ENTER or type command to continue` each time you trigger the autocommand.
- `!ctags -R .` is the command to execute. Recall that `!cmd` from inside Vim executes terminal command.

Now each time you save from inside a ruby file, Vim will run `ctags -R .`.

Add a new procedure in `two.rb`:

```
def waffle
  "Two waffles"
end
```

Save the file. If you check the tag file, you will see `waffle` as part of the tags. Success!

### Using Plugins

There are several plugins to generate ctags automatically:

- [vim-gutentags](https://github.com/ludovicchabant/vim-gutentags)
- [vim-tags](https://github.com/szw/vim-tags)
- [vim-easytags](https://github.com/xolox/vim-easytags)
- [vim-autotag](https://github.com/craigemery/vim-autotag)

I use vim-gutentags. If you use a Vim plugin manager ([vim-plug](https://github.com/junegunn/vim-plug), [vundle](https://github.com/VundleVim/Vundle.vim), [dein.vim](https://github.com/Shougo/dein.vim), etc), just install the plugin and it will work right ouf of the box!

### Ctags and Git Hooks

Tim Pope, author of many great Vim plugins, wrote a blog suggesting to use git hooks. [Check it out](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html).

## Learn Tags the Smart Way

A tag is useful once configured properly. If you are like me and you forget things easily, tags can help you visualize a project.

Suppose you are faced with a new codebase and you want to understand what `functionFood` does, you can easily read it by jumping to its definition. Inside it, you learn that it also calls `functionBreakfast`. You follow it and you learn that it calls `functionPancake`. Your function call graph looks something like this:

```
functionFood -> functionBreakfast -> functionPancake
```

This gives you insight that this code flow is related to having a pancake for breakfast.

To learn more about tags, check out `:h tags`. Now that you know how to use tags, let's explore a different feature: folding.
