# Operaciones en múltiples archivos

Ser capaz de actualizar en múltiples archivos es otra útil herramienta de edición para utilizar. Anteriormente has aprendido cómo actualizar múltiples textos mediante `cfdo`. En este capítulo, aprenderás las diferentes formas con las que puedes editar múltiples archivos en Vim.

## Diferentes formas de ejecutar un comando en múltiples archivos

Vim tiene ocho formas de ejecutar comando en múltiples archivos:
- lista de arg (`argdo`)
- lista de buffer (`bufdo`)
- lista de ventana (`windo`)
- lista de pestaña (`tabdo`)
- lista de quickfix (`cdo`)
- lista de quickfix inteligente de archivo (`cfdo`)
- lista de ubicación (`ldo`)
- lista de ubicación inteligente de archivo (`lfdo`)

En la práctica, probablemente utilizarás solo una o dos de estas ocho formas la mayor parte del tiempo (personalmente utilizo `cdo` y `argdo` más que las demás), pero es bueno aprender sobre todas las opciones disponibles y utilizar aquellas que se adecuen mejor a tu estilo de editar. 

Aprender ocho comando puede sonar algo abrumador. Pero en realidad, estos comandos funcionan de manera similar. Después de aprender uno, aprender el resto será más sencillo. Todas las formas comparten la misma gran idea: hacer una lista de sus respectivas categorías y después pasársela al comando que quieres ejecutar.

### Lista de argumento

La lista de argumento es la lista más básica. Crea una lista de archivos. Para crear una lista de archivo1, archivo2 y archivo3, puedes ejecutar:

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

la lista de argumentos es útil si necesitas agrupar un tipo específico de archivos o unos cuantos archivos. Quizás necesitas actualizar todos los "donut" a "tortitas" dentro de todos los archivos `yml`, puedes ejecutar:

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

If you run `:arga` without any argument, Vim will add your current buffer into the current arg list. If you already have `file1`, `file2`, and `file3` in your arg list and your current buffer is on `file5`, running `:arga` adds `file5` into the list.

Once you have the list, you can pass it with any command-line commands of your choosing. You've seen it being done with substitution (`:argdo %s/donut/pancake/g`). Some other examples:
- To delete all lines that contain "dessert" across the arg list, run `:argdo g/dessert/d`.
- To execute macro a (assuming you have recorded something in macro a) across the arg list, run `:argdo norm @a`.
- To write "hello " followed by the filename on the first line, run `:argdo 0put='hello ' .. @:`.

Once you're done, don't forget to save them with `:update`.

Sometimes you need to run the commands only on the first n items of the argument list. If that's the case, just pass to the `argdo` command an address. For example, to run the substitute command  only on the first 3 items from the list, run `:1,3argdo %s/donut/pancake/g`.

## Buffer List

The buffer list will be organically created when you edit new files because each time you create a new file / open a file, Vim saves it in a buffer (unless you explicitly delete it). So if you already opened 3 files: `file1.rb file2.rb file3.rb`, you already have 3 items in your buffer list. To display the buffer list, run `:buffers` (alternatively: `:ls` or `:files`). To traverse forward and backward, use `:bnext` and `:bprev`. To go to the first and last buffer from the list, use `:bfirst` and `:blast` (having a blast yet? :D).

By the way, here's a cool buffer trick unrelated to this chapter: if you have a number of items in your buffer list, you can show all of them with `:ball` (buffer all). The `ball` command displays all buffers horizontally. To display them vertically, run `:vertical ball`.

Back to the topic, the mechanics to run operation across all buffers is similar to the arg list. Once you have created your buffer list, you just need to prepend the command(s) that you want to run with `:bufdo` instead of `:argdo`. So if you want to substitute all "donut" with "pancake" across all buffers then save the changes, run `:bufdo %s/donut/pancake/g | update`.

## Window and Tab List

The windows and tabs list are also similar to the arg and buffer list. The only differences are their context and syntax. 

Window operations are performed on each open window and performed with `:windo`. Tab operations are performed on each tab you have opened and performed with `:tabdo`. For more, check out `:h list-repeat`, `:h :windo`, and `:h :tabdo`.

For example, if you have three windows opened (you can open new windows with `Ctrl-W v` for a vertical window and `Ctrl-W s` for a horizontal window) and you run `:windo 0put ='hello' . @%`, Vim will output "hello" + filename to all open windows.

## Quickfix List

In the previous chapters (Ch3 and Ch19), I have spoken about quickfixes. Quickfix has many uses. Many popular plugins use quickfixes, so it's good to spend more time to understand them.

If you're new to Vim, quickfix might be a new concept. Back in the old days when you actually have to explicitly compile your code, during the compilation phase you would encounter errors. To display these errors, you need a special window. That's where quickfix comes in. When you compile your code, Vim displays error messages in the quickfix window so you can fix them later. Many modern languages don't require an explicit compile anymore, but that doesn't make quickfix obsolete. Nowadays, people use quickfix for all sorts of things, like displaying a virtual terminal output and storing search results. Let's focus on the latter one, storing search results.

In addition to the compile commands, certain Vim commands rely on quickfix interfaces. One type of command that use quickfixes heavily are the search commands. Both `:vimgrep` and `:grep` use quickfixes by default.

For example, if you need to search for "donut" in all Javascript files recursively, you can run:

```
:vimgrep /donut/ **/*.js
```

The result for the "donut" search is stored in the quickfix window. To see these match results' quickfix window, run:

```
:copen
```

To close it, run:

```
:cclose
```

To traverse the quickfix list forward and backward, run:

```
:cnext
:cprev
```

To go to the first and the last item in the match, run:

```
:cfirst
:clast
```

Earlier I mentioned that there were two quickfix commands: `cdo` and `cfdo`. How do they differ? `cdo` executes command for each item in the quickfix list while `cfdo` executes command for each *file* in the quickfix list.

Let me clarify. Suppose that after running the `vimgrep` command above, you found:
- 1 result in `file1.js`
- 10 results in `file2.js`

If you run `:cfdo %s/donut/pancake/g`, this will effectively run `%s/donut/pancake/g` once in `file1.js` and once in `file2.js`. It runs *as many times as there are files in the match.* Since there are two files in the results, Vim executes the substitute command once on `file1.js` and once more on `file2.js`, despite the fact that there are 10 matches in the second file. `cfdo` only cares about how many total files are in the quickfix list.

If you run `:cdo %s/donut/pancake/g`, this will effectively run `%s/donut/pancake/g` once in `file1.js` and *ten times* in `file2.js`. It runs as many times as there are actual items in the quickfix list. Since there is only one match found in `file1.js` and 10 matches found in `file2.js`, it will run a total of 11 times.

Since you ran `%s/donut/pancake/g`, it would make sense to use `cfdo`. It did not make sense to use `cdo` because it would run `%s/donut/pancake/g` ten times in `file2.js` (`%s` is a file-wide substitution). Running `%s` once per file is enough. If you used `cdo`, it would make more sense to pass it with `s/donut/pancake/g` instead.

When deciding whether to use `cfdo` or `cdo`, think of the command scope that you are passing it to. Is this a file-wide command (like `:%s` or `:g`) or is this a line-wise command (like `:s` or `:!`)?

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
