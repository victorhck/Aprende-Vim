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

El valor predeterminado de Vim de `'shiftwidth'` es 2. En el texto del ejemplo anterior, hay dos espacios al comienzo de la línea y los textos "Dos" y "Dos de nuevo". Cuando Vim comprueba el número de espacios al inicio de la líea y el valor 2 de `'shiftwidth'`, Vim cosidera que las líneas tienen que ser plegadas en un primer nivel.

Supón esta vez que solo tienes un espacio entre el comienzo de la línea y el texto:

```
Uno
 Dos
 Dos de nuevo
```

Ahora mismo si ejecutas `:set foldmethod=indent`, Vim no plegará el texto sangrado debido a que no tiene el margen indicado en cada línea. Un solo espacio no es considerado dentro de sangrado de margen. Sin embargo, si cambias el valor de la variable `'shiftwidth'` a 1:

```
:set shiftwidth=1
```

El texto ahora sí se podrá plegar, ya que entra dentro de los valores de sangrado de márgenes.

Devuelve de nuevo el valor de `shiftwidth` a 2 y los espacios de las líneas de nuevo a dos. Y además añade otras dos líneas de texto, como en el ejemplo:

```
Uno
  Dos
  Dos de nuevo
    Tres
    Tres de nuevo
```

Ejecuta el plegado (`zM`), y verás:

```
Uno
+-- 4 lines: Dos -----
```

Despliega las líneas plegadas (`zR`), y después situa el cursor sobre la palabra "Tres" y alterna el estado del plegado del texto (`za`):

```
Uno
  Dos
  Dos de nuevo
+-- 2 lines: Tres -----
```

¿Qué es esto? ¿Un plegado dentro de otro plegado de texto?

Los plegados anidados son válidos. El texto "Dos" y "Dos de nuevo" están plegados en un primer nivel. El texto "Tres" y "Tres de nuevo" están plegados en un segundo nivel. Si tienes un texto plegado con un nivel de plegado mayor con texto plegado dentro, tienes múltiples capas de plegados.

## Plegado por marcador (*Marker Fold**)

Para utilizar el plegado por marcador, ejecuta:

```
:set foldmethod=marker
```

Supongamos que tenemos el siguiente text:

```
Hello

{{{
world
vim
}}}
```

Al ejecutar `zM`, verás:

```
hello

+-- 4 lines: -----
```

Vim ve `{{{` y `}}}` como indicadores de plegado y pliega el texto que hay entre ellos. Con el plegado por marcador, Vim busca los marcadores especiales, definidos por la opción `'foldmarker'`, para marcar las áreas de plegado. Para ver qué marcadores utiliza Vim, ejecuta:

```
:set foldmarker?
```

De manera predeterminada, Vim utiliza `{{{` y `}}}` como indicadores. Si quieres cambiar el indicador a otros textos como "coffee1" y "coffee2":

```
:set foldmarker=coffee1,coffee2
```

Si tienes el texto:

```
hello

coffee1
world
vim
coffee2
```

Ahora Vim utiliza `coffee1` y `coffee2` como los nuevos marcadores de plegado de texto. Como nota complementaria, un indicador debe ser una cadena literal y no puede ser una expresión regular.

## Plegado por sintaxis (*Syntax Fold*)

El plegado por sintaxis está determinado por el resaltado de lenguaje de sintaxis. Si utilizas un complemento de sintaxis de lenguaje como [vim-polyglot](https://github.com/sheerun/vim-polyglot), el plegado por sintaxis funcionará sin necesidad de configurar nada. Simplemente cambia el método de plegado a sintaxis:

```
:set foldmethod=syntax
```

Vamos a asumir que estás editando un archivo en lenguaje JavaScript y tienes el complemento vim-polyglot instalado. Si tienes un array como el siguiente:

```
const nums = [
  uno,
  dos,
  tres,
  cuatro
]
```

Esto será plegado con un plegado por sintaxis. Cuando defines un resaltado de sintaxis para un lenguaje en particular (normalmente dentro del directorio `syntax/`), puedes añadir un atributo `fold` para hacer que puede ser plegado. A continuación tienes una porción de código del archivo de sintaxis de vim-polyglot para JavaScript. Ten en cuenta la palabra clave `fold` al final de la línea.

```
syntax region  jsBracket                      matchgroup=jsBrackets            start=/\[/ end=/\]/ contains=@jsExpression,jsSpreadExpression extend fold
```

Esta guía no tratará de manera detallada la funcionalidad de `syntax`. Si tienes más curiosidad por esto, echa un vistazo a la ayuda de Vim `:h syntax.txt`.

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
