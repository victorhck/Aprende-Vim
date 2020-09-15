# Deshacer

Deshacer es una funcionalidad esencial en cualquier software moderno. El sistema de deshacer de Vim no solo es capaz de deshacer y rehacer errores, también te permite manipular y recuperar texto a través del tiempo. En este capítulo, aprenderás como deshacer y volver a hacer los cambios en tu texto, navegar por una rama de deshacer, deshacer de forma persistente y viajar a través del tiempo.

# Deshacer, rehacer y DESHACER

Para realizar una tarea básica de deshacer, puedes utilizar `u` o ejecutar `:undo`.

Si tienes este texto:
```
one

```

Añade otro texto:
```
one
two
```

Si ejecutas `u`, Vin deshacer la escritura del texto "two". 

¿Cómo sabe Vim cuanto tiene que deshacer? Vim deshace un solo "cambio" cada vez, similar al cambio del comando del punto (a diferencia del comando del punto, los comandos ejecutados en la línea de comandos también cuentan como cambios).

Para volver s rehacer el último cambi, ejecuta `Ctrl-R` o `:redo`. Después de haber deshecho  en el texto anterior la eliminación de "two", puedes ejecutar `Ctrl-R` para volver a traer de vuelta el texto eliminado.

Vim también tiene DESHACER que puedes ejecutar con `U`. Este comando deshace todos los últimos cambios. 

¿En qué `U` es diferente de `u`? Primero, `U` elimina *todos* los cambios de la última línea cambiada, mientras que `u` solo elimina un cambio cada vez. Segundo, mientras que ejecutar `u` no cuenta como cambio, ejecutar `U` cuenta como cambio.

Volvamos a este ejemplo:
```
one
two
```

Cambia la segunda línea con "three" (`ciwthree<esc>`):

```
one
three
```

Cambia la segunda línea de nuevo y reemplaza ahora el texto con "four" (`ciwfour<esc>`):

```
one
four
```

Si ahora en modo normal presionas `u`, verás "three". Si presionas `u` de nuevo, verás "two".
Sin embargo, si en vez de presionar `u` cuando todavía tienes el texto "four", hubieras presionado `U`, verías:

```
one

```

`U` pasa por alto todos los cambios intermedios y va directamete al estado original cuando comenzaste (la línea vacía debajo del texto "one"). Además, como DESHACER crea un nuevo cambio en Vim, puedes DESHACER tu acción de DESHACER. `U` seguido por `U` se deshará a sí mismo. Puede presionar `U`, después `U`, después `U`, para siempre, y verá los mismos dos textos alternados.

I personally do not use `U` because it is hard to remember the original state (I seldom ever need it). The most I have ever used `U` is when I accidentally pressed `Shift-u`.

Vim sets a maximum number of how many times you can undo in `undolevels` option variable. You can check it with `:echo &undolevels`. I have mine set to be 1000. To change yours to 1000, run `:set undolevels=1000`. Feel free to set it to any number you like.

# Breaking the Blocks

I mentioned earlier that `u` undoes a single "change" similar to the dot command's change. Any text entered between entering the insert mode and exiting is counted as a change.

If you do `ione two three<esc>` then press `u`, Vim removes the entire "one two three" text because it is considered a change. This would have been acceptable if you have a short text, but what if you have written several paragraphs under one insert mode session without exiting and later you realized you made a mistake? If you press `u`, everything you had written would be removed. Wouldn't it be useful if you can press `u` to remove only a section of your text?

Luckily, you can break the undo blocks. When you are typing in insert mode, pressing `Ctrl-G u`  creates an undo breakpoint. For example, if you do `ione <Ctrl-G u>two <Ctrl-G u>three<esc>`, then press `u`, you will only lose the text "three". Press `u` one more time to remove "two". When you write a long text, use `Ctrl-G u` strategically. The end of each sentence, between two paragraphs, or after each line of code are good locations to add undo breakpoints to make it easier to undo your mistakes if you ever make one.

It is also useful to create an undo breakpoint when deleting chunks in insert mode with `Ctrl-W` (delete the word before the cursor) and `Ctrl-U` (delete all text before the cursor). A friend suggested to use the following mappings:
```
inoremap <c-u> <c-g>u<c-u>
inoremap <c-w> <c-g>u<c-w>
```
With these, you can easily recover the deleted texts.

# Undo Tree

Vim stores every change ever written in an undo tree. If you start a new empty file:

```

```

Add a new text:

```
one

```

Add a new text:

```
one
two
```

Undo once:

```
one

```

Add a different text:

```
one
three
```

Undo again:
```
one

```

And add another different text:
```
one
four
```


Now if you undo, you will lose the text "four" you just added:
```
one

```

If you undo one more time:

```

```

You will lose the text "one". In most text editor, getting the texts "two" and "three" back would have been impossible, but not with Vim. Run `g+`, you'll get your text "one" back:

```
one

```

Type `g+` again and you will see an old friend:
```
one
two
```

Let's keep going. Press `g+` again:
```
one
three
```

Press `g+` one more time:
```
one
four
```

In Vim, every time you press `u` and then make a different change, Vim stores the previous state's text by creating an "undo branch". In this example, after you typed "two", then pressed `u`, then typed "three", you created an undo leaf branch that stores the state containing the text "two". At that moment, the undo tree contained at least two leaf nodes: the main node containing the text "three" (most recent) and the undo branch node containing the text "two". If you had done another undo and typed the text "four", you now have at least three nodes: a main node containing the text "four" and two nodes containing the texts "three" and "two".

To traverse each node in the undo tree, you can use `g+`  to go to a newer state and `g-` to go to an older state. The difference between `u`, `Ctrl-R`, `g+`, and `g-` is that both `u` and `Ctrl-R` traverse only the *main* nodes in undo tree while `g+` and `g-` traverse *all* nodes in the undo tree.

Undo tree is not easy to visualize. I find [vim-mundo](https://github.com/simnalamburt/vim-mundo) plugin to be very useful to help visualize Vim's undo tree. Give it some time to play around with it.

# Persistent Undo

If you start Vim, open a new file, and immediately press `u`, Vim will probably display "*Already at oldest change*" warning. Vim can preserve your undo history with an undo file with `:wundo`.

Create a file `mynumbers.txt`. Type:

```
one
```

Then type another line (make sure you either exit insert mode first or create an undo block):

```
one
two
```

Type another line:
```
one
two
three
```

Now create your undo file. The syntax is `:wundo myundofile`. If you need to overwrite an existing undo file, you can add `!` after `wundo`.
```
:wundo! mynumbers.undo
```

Then exit Vim. 

By now you should have `mynumbers.txt` and `mynumbers.undo` files in your directory. Open up `mynumbers.txt` again and try pressing `u`. You can't. You haven't made any changes since you opened the file. Now load your undo history by reading the undo file with `:rundo`:

```
:rundo mynumbers.undo
```

Now if you press `u`, Vim removes "three". Press `u` again to remove "two". It is like you never even closed Vim!

If you want to have an automatic undo persistence, one way to do it is by adding these lines in your vimrc:

```
set undodir=~/.vim/undo_dir
set undofile
```

I think it's better to put all the undofiles in one centralized directory, in this case, inside the `~/.vim` directory. The name `undo_dir` is arbitrary. `set undofile` tells Vim to turn on `undofile` feature because it is off by default. Now whenever you save, Vim automatically creates and updates the relevant file inside the `undo_dir` directory (make sure that you create the actual `undo_dir` directory inside `~/.vim` directory before running this).

# Time Travel

Who says that time travel doesn't exist? Vim can travel to a text state in the past with `:earlier` command-line command.

If you have this text:
```
one

```
Then later you write another line:
```
one
two
```

If you had typed "two" less than ten seconds ago, you can go back to the state where "two" didn't exist ten seconds ago with:
```
:earlier 10s
```

You can use `:undolist` to see when the last change was made. `:earlier` also accepts minutes (`m`), hours (`h`), and days (`d`) as arguments. 

```
:earlier 10s    go to the state 10 seconds before
:earlier 10m    go to the state 10 minutes before
:earlier 10h    go to the state 10 hours before
:earlier 10d    go to the state 10 days before
```

In addition, it also accepts a regular `count` as argument to tell Vim to go to the older state `count` times. For example, if you do `:earlier 2`, Vim will go back to an older text state two changes ago. It is the same as doing `g-` twice. Lastly, you can also tell `:earlier` to go to the older text state 10 saves ago with `:earlier 10f`.

The same set of arguments work with `:earlier` counterpart: `:later`.
```
:later 10s    go to the state 10 seconds later
:later 10m    go to the state 10 minutes later
:later 10h    go to the state 10 hours later
:later 10d    go to the state 10 days later
:later 10     go to the newer state 10 times
:later 10f    go to the state 10 saves later
```

# Learn Undo the Smart Way

`u` and `Ctrl-R` are two indispensable Vim commands. Learn them first. I do not use UNDO in my workflow, however I think it's good to be aware that it exists. Next, learn how to use `:earlier` and `:later` using the time argumentsfirst. After that, take your time to understand the undo tree. The [vim-mundo](https://github.com/simnalamburt/vim-mundo) plugin helped me a lot. Type along the texts in this chapter and check the undo tree as you make each change. Once you grasp it, you will never see undo system the same way again.

Prior to this chapter, you learned how to find any text in a project space, with undo, you can now find any text in a time dimension. You are now able to search for any text by its location and time written. You have achieved Vim-omnipresence.

