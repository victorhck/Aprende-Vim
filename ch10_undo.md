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

`U` pasa por alto todos los cambios intermedios y va directamete al estado original cuando comenzaste (la línea vacía debajo del texto "one"). Además, como DESHACER crea un nuevo cambio en Vim, puedes DESHACER tu acción de DESHACER. `U` seguido por `U` se deshará a sí mismo. Puede presionar `U`, después `U`, después `U`, para siempre, y verás los mismos dos textos alternar hacia adelante y hacia atrás.

Personalmente no utilizo `U` porque es difícil recordar el estado original (rara vez lo he necesitado). Lo más que he utilizado `U` es cuando accidentalmente he pulsado `Shift-u`.

Vim establece un máximo de cuantas veces puedes deshacer en la variable `undolevels`. Puedes comprobarla con `:echo &undolevels`. Tengo establecida la mía para que sea 1000. Para cambiar la tuya a 1000 ejecuta `:set undolevels=1000`. Pero puedes establecerla al número que prefieras.

# Dividiendo los bloques

He mencionado anteriormente que `u` deshace un único "cambio", de manera similar al cambio del comando del punto. Cualquier texto introducido entre entrar en el modo insertar y salir del modo es tomado en cuenta como cambio.

Si ejecutas `ione two three<esc>` y después presionas `u`, Vim elimina el texto entero "one two three" porque es considerado un cambio al completo. Esto sería aceptable cuando se trata de un texto corto, pero ¿qué pasa si has escrito varios párrafos bajo una única sesión del modo insertar sin salir de ella y después te das cuenta que has cometido un error? Si presionas `u`, todo lo que habías escrito sería eliminado. ¿No sería más útil si pudieras presionar `u` para eliminar solo una sección de tu texto?

Afortunadamente, puedes dividir los bloques de deshacer. Cuando estás escribiendo en el modo insertar, presiona `Ctrl-G u` para crear un punto de ruptura del comando deshacer. Por ejemplo, si escribes lo siguiente en el ejemplo anterior `ione <Ctrl-G u>two <Ctrl-G u>three<esc>`, después presiona `u`, así solo perderás es texto "three". Presiona `u` una vez más para eliminar "two". Cuando escribes un texto largo, utiliza `Ctrl-G u` de manera estratégica. Al final de cada frase, entre dos párrafos, o después de cada línea de código son buenas ubicaciones para añadir puntos de ruptua para hacer más sencillo el poder deshacer los errores si los comentes.

También es útil el crear puntos de interrupción de deshacer cuando se borran fragmentos en el modo insertar con las combinaciones de teclas `Ctrl-W` (elimina la palabra anterior al cursor) o `Ctrl-U` (elimina todo el texto anterior al cursor). Un amigo sigirió el utilizar el siguiente mapeado de teclas para que haga la acción automáticamente al pulsar dichas combinaciones de teclas:
```
inoremap <c-u> <c-g>u<c-u>
inoremap <c-w> <c-g>u<c-w>
```
Con esto, puedes recuperar fácilmente los textos eliminados de esta manera.

# El árbol de cambios del comando deshacer

Vim almacena cada cambio que se ha escrito en un árbol del comando deshacer. Si comienzas editando un archivo vacío:

```

```

Y añades un texto nuevo:

```
one

```

Después añades otro texto:

```
one
two
```

Deshaces los cambios una vez:

```
one

```

Añades un texto diferente:

```
one
three
```

Vuelves a deshacer los cambios:
```
one

```

Y añades otro texto diferente:
```
one
four
```


Ahora, si realizas una acción de deshacer, se eliminará el texto "four" que acabas de añadir:
```
one

```

Si deshaces los cambios una vez más:

```

```

Entonces perderás el texto "one". En la mayoría de los editores de texto, volver a recuperar los textos "two" y "three" sería imposible, pero no con Vim. Ejecuta `g+`, verás que vuelve a aparecer el texto "one":

```
one

```

Escribe `g+` de nuevo y verás a un atniguo amigo, el texto "two":
```
one
two
```

Continuemos. Volvamos a presionar `g+`:
```
one
three
```

Presiona `g+` una vez más:
```
one
four
```

En Vim, cada vez que presionas `u` y después haces un cambio diferente, Vim almacena el estado previo del cambio creando una "rama de deshacer". En este ejemplo, después de haber escrito "two" y después presionado `u` y después haber escrito "three", creaste una rama que almacena el estado que contenía el texto "two". En ese momento, el árbol de deshacer contenía al menos dos nodos: el nodo principal que contenía el texto "three" (el cambio más reciente) y el nodo de la rama de deshacer que contenía el texto "two". Si hubieras realizado otra acción de deshacer y escrito la palabra "four", ahora tendrías al menos tres nodos: un nodo principal que contiene el texto "four" y dos nodos conteniendo los textos "three" y "two".

Para recorrer cada nodo del árbol de deshacer, puedes utilizar `g+` para ir a un nuevo estado y `g-` para ir a un estado anterior. La diferencia entre `u`, `Ctrl-R`, `g+`, y `g-` es que tanto `u` como `Ctrl-R` recorren solo los nodos *principales* en el árbol de deshacer, mientras que `g+` y `g-` recorren *todos* los nodos en árbol de cambios del comando deshacer.

El árbol de cambios de deshacer nos es sencillo de visualizar. He encontrado el complemento [vim-mundo](https://github.com/simnalamburt/vim-mundo) muy útil a la hora de ayudar a visualizar el árbol de cambios de deshacer. Dedícale algún tiempo para jugar con él.

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

