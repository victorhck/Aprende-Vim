# Capítulo 17: Plegado de texto (*Fold*)

Cuando lees un archivo, a menudo hay mucho texto irrelevante que te impide entender lo que realiza ese archivo. Para ocultar ese *ruido* innecesario, utiliza el plegado de texto que ofrece Vim (Vim Fold).

En este capítulo, aprenderás las diferentes formas de plegar y desplegar partes de un archivo.

## Plegado manual

Imagina que estás doblando una hoja de papel para cubrir una parte de texto. El texto actual no ha desaparecido, todavía sigue ahí. El plegado de Vim funciona de una manera similar. Este pliega una parte del texto, evitando mostrarlo pero sin eliminarlo del archivo.

El operador para el plegado es `z` (cuando pliegas un papel, este tiene forma de la letra z).

Supón que tienes el siguiente texto:

```
Fold me
Hold me
```

Con el cursor situado en la primera línea, escribe `zfj`. Vim plegará las dos líneas en una sola. Deberías ver algo similar a esto:

```
+-- 2 lines: Fold me -----
```

Vamos a ver en detalle el comando:
- `zf` es el operador del operador del plegado.
- `j` es el indicador de movimiento para el operador del plegado.

Puedes abrir un texto plegado con `zo`. Para cerrarlo utiliza `zc`.

El comando para plegar texto en Vim es un operador, por lo que cumple las reglas gramaticales de Vim (`verbo + sustantivo`). Puedes pasarle al operador de plegado un movimiento o un objeto de texto. Para plegar el párrafo en el que se encuentra el cursor, ejecuta `zfip`. Para plegar desde la posición actual hasta el final del archivo, ejecuta `zfG`. Para plegar los textos entre `{` y `}`, ejecuta `zfa{`.

Puedes plegar texto desde el modo visual. Selecciona el área que quieres plegar (puedes hacerlo con `v`, `V` o `Ctrl-v`), y después ejecuta `zf`.

También puedes ejecutar un plegado de texto desde la línea de comandos con el comando `:fold`. Para plegar la línea actual y la línea posterior, ejecuta:

```
:,+1fold
```

`,+1` es el rango. Si no le pasas parámetros al rango, el comando de manera predeterminada lo aplica a la línea actual. `+1` es el indicador de rango para la línea siguiente. Para plegar las líneas de la 5 a la 10, ejecuta `:5,10fold`. Para plegar desde la posición actual hasta el final de la línea, ejecuta `:,$fold`.

Hay muchos otros comandos para gestionar el plegado de texto. Creo que son muchos para recordar cuando estás comenzando. Los más útiles son:
- `zR` para abrir todos los plegados.
- `zM` para cerrar todos los plegados.
- `za` para alternar el estado en el que se encuentra un plegado.

Puedes ejecutar `zR` y `zM` estando en cualquier línea, pero `za` solo funciona cuando estás en una línea que pertenece a un plegado de texto. Para aprender más sobre los comandos de los plegados de texto, echa un vistazo a `:h fold-commands`.

## Diferentes métodos de plegado de texto

La sección anterior trata sobre el plegado de texto manual de Vim. Hay seis métodos de plegado de texto en Vim:
1. Manual (Manual)
2. Sangría (Indent)
3. Expresión (Expression)
4. Sintaxis (Syntax)
5. Diferencia (Diff)
6. Marcador (Marker)

Para ver el método de plegado de texto que estás utilizando actualmente, ejecuta `:set foldmethod?`. De manera predeterminada, Vim utiliza el método `manual`.

En el resto del capítulo, aprenderás los otros cinco métodos. Vamos a empezar con el plgado por sangría (o en inglés *indent*).

## Plegado por sangría (*Indent Fold*)

Para utilizar el plegado de texto por sangría de margen, cambia el método de plegado `'foldmethod'` a *indent*. Ejecuta:

```
:set foldmethod=indent
```

Supongamos que tenemos este texto:

```
Uno
  Dos
  Dos de nuevo
```

Si ejecutas `:set foldmethod=indent`, verás algo así:

```
Uno
+-- 2 lines: Dos -----
```

Con este método de plegado de texto, Vim mira cuantos espacios tiene cada línea al comienzo y lo compara con la opción `'shiftwidth'` para determinar cómo lo puede plegar. `'shiftwidth'` devuelve el número de espacios requeridos para cada paso del sangrado de línea. Si ejecutas:

```
:set shiftwidth?
```

Vim's default `'shiftwidth'` value is 2. On the text above, there are two spaces between the start of the line and the text "Two" and "Two again". When Vim sees the number of spaces and that the `'shiftwidth'` value is 2, Vim considers that line to have an indent fold level of one.

Suppose this time you only one space between the start of the line and the text:

```
One
 Two
 Two again
```

Right now if you run `:set foldmethod=indent`, Vim does not fold the indented line because there isn't sufficient space on each line. One space is not considered an indentation. However, if you change the `'shiftwidth'` to 1:

```
:set shiftwidth=1
```

The text is now foldable. It is now considered an indentation. 

Restore the `shiftwidth` back to 2 and the spaces between the texts to two again. In addition, add two additional texts:

```
One
  Two
  Two again
    Three
    Three again
```

Run fold (`zM`), you will see:

```
One
+-- 4 lines: Two -----
```

Unfold the folded lines (`zR`), then put your cursor on "Three" and toggle the text's folding state (`za`):

```
One
  Two
  Two again
+-- 2 lines: Three -----
```

What's this? A fold within a fold?

Nested folds are valid. The text "Two" and "Two again" have fold level of one. The text "Three" and "Three again" have fold level of two. If you have a foldable text with a higher fold level within a foldable text, you will have multiple fold layers.

## Marker Fold

To use a marker fold, run:

```
:set foldmethod=marker
```

Suppose you have the text:

```
Hello

{{{
world
vim
}}}
```

Run `zM`, you will see:

```
hello

+-- 4 lines: -----
```

Vim sees `{{{` and `}}}` as fold indicators and folds the texts between them. With the marker fold, Vim looks for special markers, defined by `'foldmarker'` option, to mark folding areas. To see what markers Vim uses, run:

```
:set foldmarker?
```

By default, Vim uses `{{{` and `}}}` as indicators. If you want to change the indicator to another texts, like "coffee1" and "coffee2":

```
:set foldmarker=coffee1,coffee2
```

If you have the text:

```
hello

coffee1
world
vim
coffee2
```

Now Vim uses `coffee1` and `coffee2` as the new folding markers. As a side note, an indicator must be a literal string and cannot be a regex.

## Syntax Fold

Syntax fold is determined by syntax language highlighting. If you use a language syntax plugin like [vim-polyglot](https://github.com/sheerun/vim-polyglot), the syntax fold will work right out of the box. Just change the fold method to syntax:

```
:set foldmethod=syntax
```

Let's assume you are editing a JavaScript file and you have vim-polyglot installed. If you have an array like the following:

```
const nums = [
  one,
  two,
  three,
  four
]
```

It will be folded with a syntax fold. When you define a syntax highlighting for a particular language (typically inside the `syntax/` directory), you can add a `fold` attribute to make it foldable. Below is a snippet from vim-polyglot JavaScript syntax file. Notice the `fold` keyword at the end.

```
syntax region  jsBracket                      matchgroup=jsBrackets            start=/\[/ end=/\]/ contains=@jsExpression,jsSpreadExpression extend fold
```

This guide won't cover the `syntax` feature. If you're curious, check out `:h syntax.txt`.

## Expression Fold

Expression fold allows you to define an expression to match for a fold. After you define the fold expressions, Vim scans each line for the value of `'foldexpr'`. This is the variable that you have to configure to return the appropriate value. If the `'foldexpr'` returns 0, then the line is not folded. If it returns 1, then that line has a fold level of 1. If it returns 2, then that line has a fold level of 2. There are more values other than integers, but I won't go over them. If you are curious, check out `:h fold-expr`.

First, let's change the foldmethod:

```
:set foldmethod=expr
```

Suppose you have a list of breakfast foods and you want to fold all breakfast items starting with "p":

```
donut
pancake
pop-tarts
protein bar
salmon
scrambled eggs
```

Next, change the `foldexpr` to capture the expressions starting with "p":

```
:set foldexpr=getline(v:lnum)[0]==\\"p\\"
```

The expression above looks complicated. Let's break it down:
- `:set foldexpr` sets up the `'foldexpr'` option to accept a custom expression.
- `getline()` is a Vimscript function that returns the content of any given line. If you run `:echo getline(5)`, it will return the content of line 5.
- `v:lnum` is Vim's special variable for the `'foldexpr'` expression. Vim scans each line and at that moment stores each line's number in `v:lnum` variable. On line 5, `v:lnum` has value of 5. On line 10, `v:lnum` has value of 10.
- `[0]` in the context of `getline(v:lnum)[0]` is the first character of each line. When Vim scans a line, `getline(v:lnum)` returns the content of each line. `getline(v:lnum)[0]` returns the first character of each line. On the first line of our list, "donut", `getline(v:lnum)[0]` returns "d". On the second line of our list, "pancake", `getline(v:lnum)[0]` returns "p".
- `==\\"p\\"` is the second half of the equality expression. It checks if the expression you just evaluated is equal to "p". If it is true, it returns 1. If it is false, it returns 0. In Vim, 1 is truthy and 0 is falsy. So on the lines that start with an "p", it returns 1. Recall if a `'foldexpr'` has a value of 1, then it has a fold level of 1.

After running this expression, you should see:

```
donut
+-- 3 lines: pancake -----
salmon
scrambled eggs
```

## Diff Fold

Vim can do a diff procedure to compare two or more files.

If you have `file1.txt`:

```
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
```

And `file2.txt`:

```
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
emacs is ok
```

Run `vimdiff file1.txt file2.txt`:

```
+-- 3 lines: vim is awesome -----
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
[vim is awesome] / [emacs is ok]
```

Vim automatically folds some of the identical lines. When you are running the `vimdiff` command, Vim automatically uses `foldmethod=diff`. If you run `:set foldmethod?`, it will return `diff`.

## Persisting Fold

You loses all fold information when you close the Vim session. If you have this file, `count.txt`:

```
one
two
three
four
five
```

Then do a manual fold from line "three" down (`:3,$fold`):

```
one
two
+-- 3 lines: three ---
```

When you exit Vim and reopen `count.txt`, the folds are no longer there!

To preserve the folds, after folding, run:

```
:mkview
```

Then when you open up `count.txt`, run:

```
:loadview
```

Your folds are restored. However, you have to manually run `mkview` and `loadview`. I know that one of these days, I will forget to run `mkview` before closing the file and I will lose all the folds. How can we automate this process?

To automatically run `mkview` when you close a `.txt` file and run `loadview` when you open a `.txt` file, add this in your vimrc:

```
autocmd BufWinLeave *.txt mkview
autocmd BufWinEnter *.txt silent loadview
```

Recall that `autocmd` is used to execute a command on an event trigger. The two events here are:
- `BufWinLeave` for when you remove a buffer from a window.
- `BufWinEnter` for when you load a buffer in a window.

Now after you fold inside a `.txt` file and exit Vim, the next time you open that file, your fold information will be restored.

By default, Vim saves the fold information when running `mkview` inside `~/.vim/view` for the Unix system. For more information, check out `:h 'viewdir'`.

## Learn Fold The Smart Way

When I first started Vim, I neglected ot learn fold because I didn't think it was useful. However, the longer I code, the more useful I find folding is. Strategically placed folds can give you a better overview of the text structure, like a book's table of content.

When you learn fold, start with the manual fold because that can be used on-the-go. Then gradually learn different tricks to do indent and marker folds. Finally, learn how to do syntax and expression folds. You can even use the latter two to write your own Vim plugins.
