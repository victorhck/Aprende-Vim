# Capítulo 11: Modo visual

Con los editores de texto con interfaz gráfica (como LibreOffice Writer o Microsoft Word) probablemente sabes que puedes resaltar un bloque de texto y aplicarle cambios. Vim también puede hacerlo, con el modo visual. Vim tiene tres modos visuales diferentes para usar. En este capítulo, aprenderás cómo utilizar cada uno de los diferestes modos visuales para manipular bloques de texto de manera eficiente.

## Los tres tipos de modos visuales
Los tres modos son:

```
v         Modo visual de selección de caracter
V         Modo visual de selección de líneas
Ctrl-v    Modo visual de selección de bloque
```

Si tienes el siguiente texto:
```
uno
dos
tres
```

El modo visual de selección de caracter es utilizado para seleccionar caracteres de forma individual. Pulsa `v` en el primer caracter de la primera línea. Después baja hasta la línea siguiente con `j`. Esto resaltará todo el texto desde "uno" hasta donde este ubicado el cursor. Ahora pulsa `gU`, Vim convertirá a mayúsculas todos los caracteres resaltados.

El modo visual de selección de línea funciona con líneas como unidades de selección. Pulsa `V` y observa como Vim selecciona la línea completa en la que esté ubicado el cursor. Igual que en el modo visual de selección de caracter, si ejecutas `gU`, Vim cambiará a mayúsculas las líneas resaltadas.

El modo visual de slección de bloque funciona con filas y columnas. Te da más libertad de movimientos que los dos modos anteriores. Pulsando `Ctrl-V` verás qie Vim resalta el caracter bajo el cursor de igual manera que el modo visual de selección de caracter, excepto que ahora en vez de resaltar cada caracter hasta el final de la línea antes de seguir en la línea siguiente, puedes dirigir el cursor hasta la línea siguiente sin que se marquen todos los caracteres de la línea actual. Intenta moverte mediante las teclas `h/j/k/l` y observa los movimientos del cursor y las zonas que se marcan.

En la esquina inferior izquierda de la ventana de Vim, verás que se muestra `-- VISUAL --`, `-- VISUAL LINE --` o `-- VISUAL BLOCK --` para indicar el modo visual en que te encuentras.

Mientras estás dentro de un modo visual, puedes cambiar a otro de los modos pulsando `v`, `V` o `Ctrl-V`. Por ejemplo, si estás en el modo visual de selección de línea y quieres cambiar al modo visual de selección de bloque, ejecuta `Ctrl-V`. ¡Inténtalo!

Hay tres maneras de salir del modo visual: `esc`, `Ctrl-C` y con la misma tecla con la que accediste al modo visual actual.

Esto último significa, que si estás en el modo visual de selección de línea (`V`), podrás salir de ese modo pulsando `V` de nuevo. Si estás en el modo visual de selección de caracter, podrás salir pulsando `v`. Si estás en el modo visual de selección de bloque, pulsa `Ctrl-V` para salir de ese modo.

Hay una forma más de entrar en el modo visual:
```
gv    Go to the previous visual mode
```

Este comando seleccionará el mismo modo visual y el mismo resaltado de texto de la última vez.

## Navegación en el modo visual

Mientras estás en el modo visual, puedes expandir el bloque de texto resaltado con los movimientos de Vim.

Vamos a utilizar el mismo texto que hemos utilizado anteirormente:

```
uno
dos
tres
```

Esta vez vamos a empezar desde la línea "dos". Pulsa `v` para comenzar el modo visual de selección de caracter:

```
uno
[d]os
tres
```

Pulsa `j` y Vim altará todo el texto desde la línea "dos" hasta el primer caracter de la línea "tres".

```
uno
[dos
t]res
```

Supongamos que nos hemos dado cuenta que también necesitamos resaltar la línea "uno", así que pulsas la tecla `k`. Para tu asombro, esto ahora excluye "tres". Al pulsar `k` esto reduce la zona resaltada, no la expande.

```
uno
[d]os
tres
```

¿Hay alguna manera de expandir libremente la selección visual para ir a cualquier dirección que quieras? 

La respuesta es sí. Regresemos a la situación anterior donde teníamos las líneas "dos" y "tres" resaltadas.

```
uno
[dos
t]res    <-- cursor
```

El resaltado visual sigue el movimiento del cursor. Si quiere expandirlo hacia arriba a la línea "uno", necesitas mover el cursor cuando el cursor está en la palabra "dos", no "tres". Ahora tu cursor está en la línea "tres". Para movelo, cambia la loclización del cursor tanto con `o` u `O`.

```
uno
[dos     <-- cursor
t]res
```

Ahora pulsa `k`, esto ya no reducirá la selección si no que la expandirá hacia arriba.

```
[one
two
t]hree
```

Al pulsar `o` u `O` en el modo visual, esto hará que nuestro cursor salte del inicio que bloque resaltado, sin eliminar las partes resaltadas y podremos añadir más texto a esa selección. 

## Visual Mode Grammar

Visual mode is one of Vim's modes. Being a mode means that the same key may work differently than in another mode. Luckily, visual mode shares many common keys with normal mode.

For example, if you have the text:

```
one
two
three
```

Highlight the lines "one" and "two" with the line-wise visual mode (`V`):
```
[one
two]
three
```

Pressing `d` will delete the selection, similar to normal mode. Notice the grammar rule from normal mode, verb + noun, does not apply. The same verb is still there (`d`), but there is no noun in visual mode. The grammar rule in visual mode is noun + verb, where noun is the highlighted text. Select the text block first, then operate.

In normal mode, there are some commands that do not require a motion, like `x` to delete a single character under the cursor and `rx` to replace the character under the cursor with "x". In visual mode, these commands are now being applied to the entire highlighted text instead of a single character. Back at the highlighted text:
```
[one
two]
three
```

Running `x` deletes all highlighted texts.

You can use this behavior to quickly create a header in markdown text. Suppose you have a text in a markdown file:
```
Chapter One
```

You need to quickly turn this header into a header. First you copy the text with `yy`, then paste it with `p`:
```
Chapter One
Chapter One
```

Now go to the second line, select it with line-wise visual mode:
```
Chapter One
[Chapter One]
```

In markdown you can create a header by adding  a series of `=` below a text, so you will replace the entire highlighted text by running `r=`:

```
Chapter One
===========
```

To learn more about operators in visual mode, check out `:h visual-operators`.


## Visual Mode and Ex Commands

You can selectively apply Ex commands on a highlighted text block. If you have these expressions:

```
const one = "one";
const two = "two";
const three = "three";
```

You need to substitute only the first two lines of "const" with "let". Highlight the first two lines with *any* visual mode and run the substitute command `:s/const/let/g`:
```
let one = "one";
let two = "two";
const three = "three";
```
Notice I said you can do this with *any* visual mode. You do not have to highlight the entire line to run Ex command on that line. As long as you select at least a character on each line, the Ex command will be applied.

## Editing Across Multiple Lines 

You can edit text across multiple lines in Vim using the block-wise visual mode. If you need to add a semicolon at the end of each line:
 
```
const one = "one"
const two = "two"
const three = "three"
```

With your cursor on the first line:
- Run block-wise visual mode and go down two lines (`Ctrl-V jj`).
- Highlight to the end of the line (`$`).
- Append (`A`) then type ";".
- Exit visual mode (`esc`).

You should see the appended ";" on each line. By the way, while in block-wise visual mode, to enter the insert mode, you can use either `A` to enter the text after the cursor or `I` to enter the text before the cursor. Do not confuse them with `A` and `I` from normal mode.

Alternatively, you can also use the `:normal` command:

- Highlight all 3 lines (`vjj`).
- Type `:normal! A;`.

Remember, `:normal` command executes normal mode commands. You can instruct it to run `A;` to append text ";" at the end of the line. 

## Incrementing numbers

Vim has `Ctrl-X` and `Ctrl-A` commands to decrement and increment numbers. When used with visual mode, you can increment numbers across multiple lines.

If you have these HTML elements:
```
<div id="app-1"></div>
<div id="app-1"></div>
<div id="app-1"></div>
<div id="app-1"></div>
<div id="app-1"></div>
```

It is a bad practice to have several ids having the same name, so let's increment them to make them unique:
- Move your cursor to the *second* "1".
- Start block-wise visual mode and go down 3 lines (`Ctrl-V 3j`). This highlights the remaining  "1"s.
- Run `g Ctrl-A`.

You should see this result:
```
<div id="app-1"></div>
<div id="app-2"></div>
<div id="app-3"></div>
<div id="app-4"></div>
<div id="app-5"></div>
```

`g Ctrl-A` increments numbers on multiple lines. `Ctrl-X/Ctrl-A` can increment letters too. If you run:

```
:set nrformats+=alpha
```

The `nrformats` option instructs Vim which bases are considered as "numbers" for `Ctrl-A` and `Ctrl-X` to increment and decrement. By adding `alpha`, an alphabetical character is now considered as a number. If you have the following HTML elements:
```
<div id="app-a"></div>
<div id="app-a"></div>
<div id="app-a"></div>
<div id="app-a"></div>
<div id="app-a"></div>
```

Put your cursor on the second "app-a". Use the same technique as above (`Ctrl-V 3j` then `g Ctrl-A`) to increment the ids.
```
<div id="app-a"></div>
<div id="app-b"></div>
<div id="app-c"></div>
<div id="app-d"></div>
<div id="app-e"></div>
```
## Selecting the Last Visual Mode Area

You learned that `gv` can quickly highlight the last visual mode highlight. You can also go to the location of the start and the end of the last visual mode with these two special marks:

```
`<    Go to the last place of the previous visual mode highlight
`>    Go to the first place of the previous visual mode highlight
```

I want you to observe something. Earlier, I mentioned that you can selectively execute Ex commands on a highlighted text, like `:s/const/let/g`. When you did that, you should see this:
```
:`<,`>s/const/let/g
```

You were actually executing `s/const/let/g` command using marks as range. You can always edit these marks anytime you wish. If instead you needed to substitute from the start of the highlighted text to the end of the file, you just change the command line to:
```
:`<,$s/const/let/g
```

## Entering Visual Mode from Insert Mode

You can also enter visual mode from the insert mode. To go to character-wise visual mode while you are in insert mode:

```
Ctrl-O v
```

Recall that running `Ctrl-O` while in the insert mode lets you to execute a normal mode command. While in this normal-mode-command-pending mode, run `v` to enter character-wise visual mode. Notice that on the bottom left of the screen, it says `--(insert) VISUAL--`. This trick works with any visual mode operator: `v`, `V`, and `Ctrl-V`.

## Select Mode

Vim has a mode similar to visual mode called the *select mode*. Like visual mode, it also has three different modes:
```
gh         Character-wise select mode
gH         Line-wise select mode
gCtrl-h    Block-wise select mode
```

Select mode emulates a regular editor's text highlighting behavior closer than Vim's visual mode does. 

In a regular editor, after you highlight a text block and type a letter, say the letter "y", it will delete the highlighted text and insert the letter "y".

If you highlight a line of text with line-wise select mode (`gH`) and type "y", it will delete the highlighted text and insert the letter "y", much like the regular text editor.

Contrast this behavior with visual mode: if you  highlight a line of text with line-wise visual mode (`V`) and type "y", the highlighted text will not be deleted and replaced by the literal letter "y". It will only be yanked and stored in the yanked register `"0`.

I personally never used select mode, but it's good to know that it exists.

## Learn Visual Mode the Smart Way

The visual mode is Vim's representation of the text highlighting procedure. 

If you find yourself using visual mode operation far more often than normal mode operations, be careful. I think this is an anti-pattern. It takes more keystrokes to run a visual mode operation than its normal mode counterpart. If you need to delete an inner word, why use four keystrokes, `viwd` (visually highlight an inner word then delete), if you can accomplish it with just three keystrokes (`diw`)? The latter is more direct and concise. Of course, there will be times when visual modes are appropriate, but in general, favor a more direct approach.
