# Moviéndote por un archivo

Al principio, moverte utilizando el teclado te puede hacer sentir incómodo e increíblemente lento, ¡pero no desistas! Una vez que te acostumbres, podrás moverte a cualquier parte de un archivo de manera más rápida que utilizando el ratón.

En este capítulo, aprenderás los movimientos esenciales y cómo utilizarlos de manera eficiente. Ten en mente que este capítulo **no** es todo lo que Vim puede ofrecer en cuanto a la hora de moverse por un archivo. La meta aquí es presentar los movimientos más útiles para volverte productivo rápidamente. Si necesitas aprender más, echa un vistazo a la ayuda de Vim `:h motion.txt`.

# Navegando por caracteres

La unidad más básica de movimiento es moverse un caracter a la izquierda, abajo, arriba y a la derecha.

```
h   Izquierda
j   Abajo
k   Arriba
l   Derecha
```

También puedes moverte con las flechas de dirección de tu teclado. Si estás empezando, sientete libre de utilizar cualquier método con el que te sientas más cómodo.

Personalmente prefiero la clásica combinación `hjkl` porque mi mano derecha permanece encima del teclado. Al hacer esto, hace que este más cerca de las teclas circundantes del teclado.
Para *obligarme* a utilizar esta combinación de teclas, he inhabilitado las flechas de dirección del teclado al usar Vim añadiendo estas líneas en el archivo `~/.vimrc`:

```
noremap <Up> <NOP>
noremap <Down> <NOP>
noremap <Left> <NOP>
noremap <Right> <NOP>
```

También hay complementos para tratar de romper ese mal hábito. Uno de ellos es [vim-hardtime](https://github.com/takac/vim-hardtime).
Para mi sorpresa, solo lleva unos pocos días el habituarse al uso de `hjkl`.

*Por cierto, si te preguntas porque Vim utiliza la combinación de teclas `hjkl` para moverse por el texto, es debido que el terminal Lear-Siegler ADM-3A donde terminal Bill Joy empezó a desarrollar Vi, no tenía teclas con flechas en el teclado y utilizó `hjkl` como izquierda/abajo/arriba/derecha.*

si quieres moverte a un sitio cercano donde está actualmente el cursor, como moverse de una parte de la palabra a otra parte de la misma palabra, usaría las teclas `h` o `l`. Si quiero moverme arriba o abajo unas pocas líneas dentro de la ventana mostrada, usaría las teclas `j` o `k`. Si quisiera desplazarme a un lugar más lejano de la posición del cursor, utilizaría un tipo de movimiento diferente.

# Numeración relativa

Creo que es útil el tener establecido las opciones de `number` y `relativenumber`. Puedes hacerlo escribiendo lo siguiente en el archivo `.vimrc`:

```
set relativenumber number
```

Esto muestra la línea actual en la que se encuentra el cursor y los números de línea relativos desde la posición de este.

¿Por qué esto puede ser útil? Esto me permite rápidamente ver cuantas a líneas de diferencia estoy desde la posición a la que quiero ir. Con esto, puedo ver fácilmente que mi objetivo está 12 líneas por debajo de mi posición actual, así que puedo ejecutar `12j`. Además, si estoy en la línea 69 y mi objetivo es desplazar el cursor a la línea 81, esto me lleva a tener que hacer un cálculo mental (81 - 69 = 12). Esto consume muchos recursos mentales. Cuanto menos tenga que pensar sobre donde quiero ir, mejor.

Esto es 100% una preferencia personal. Experimenta con las opciones `relativenumber` / `norelativenumber`, `number` / `nonumber` ¡y utiliza cualquier método que encuentre más útil!

# Cuenta tu movimiento

Una cosa más, hablemos sobre el argumento "contar". Los movimientos aceptan un argumento precedente numérico. He mencionado anteriormente que puedes desplazarte 12 líneas hacia abajo en el texto mediante `12j`. El 12 en `12j` es el número que lleva la cuenta.

La sintáxis para utilizar el número de conteo en tu movimiento es:

```
[número] + movimiento
```

Puedes aplicar esto a todos tus movimientos. Si quieres moverte 9 caracteres a la derecha, en vez de presionar la tecla `l` puedes ejecutar simplemente `9l`. Cuantos más movimientos aprendas, trata de darles un argumento de conteo.

# Navegación por palabras

Vamos a movernos por una unidad de movimiento mayor: *palabra*. Puedes moverte al comienzo de la siguiente palabra (`w`), al final de la siguiente palabra (`e`), al comienzo de la palabra anterior (`b`)y al final de la palabra anterior (`ge`).

Además de esto, también está la *PALABRA*, distinta de palabra. Puedes mover el cursor al principio de la siguiente PALABRA (`W`), al final de la siguiente PALABRA (`E`), al comienzo de la PALABRA previa (`B`) y al final de la PALABRA previa (`gE`). Para hacerlo sencillo de recordar, PALABRA utiliza las mismas letras de movimiento que palabra excepto que son en mayúsculas.

```
w     Mueve el cursor hacia adelante al comienzo de la siguiente palabra
W     Mueve el cursor hacia adelante al comienzo de la siguiente PALABRA
e     Mueve el cursor hacia adelante una palabra hasta el final de la siguiente palabra
E     Mueve el cursor hacia adelante una palabra hasta el final de la siguiente PALABRA
b     Mueve el cursor hacia atrás al principio de la palabra previa
B     Mueve el cursor hacia atrás al principio de la PALABRA previa
ge    Mueve el cursor hacia atrás al final de la palabra previa
gE    Mueve el cursor hacia atrás al final de la PALABRA previa
```

Entonces, ¿cuáles son las similitudes y diferencias entre una palabra y una PALABRA? Tanto una palabra como una PALABRA están formadas por caracteres que no son espacios en blanco. Una palabra es una secuencia de caracteres que continen únicamente este grupo de caracteres `a-zA-Z0-9_`. Una PALABRA es una secuencia que incluyen todos los caracteres excepto el espacio en blanco (cuando me refiero a espacio en blanco, esto incluye tanto un espacio, una separación por tabulador o un fin de línea) Para aprender más, echa un vistazo a la ayuda en Vim sobre este tema con estos comandos: `:h word` o `:h WORD`.

Por ejemplo, supongamos que tenemos la siguiente frase:
```
const hello = "world";
```

Con el cursor al comienzo de la línea, ve al final de la línea con `l`, te llevará pulsar 21 teclas. Utilizando `w`, solo te llevará pulsar 6 veces. Utilizando `W`, solo te llevará 4 pulsacione. Tanto palabra como PALABRA sn buenas opciones para mover el cursor en pequeñas distancias.

Sin embargo, puedes pasar de "c" a ";" con una pulsación de tecla con la navegación de línea actual.

# Navegación de línea actual

Mientras editas, a menudo necesitas navegar horizontalmente en la misma línea. Para saltar al primer caracter de la línea actual, utiliza `0`. Para ir al último caracter de la línea actual, utiliza `$`. Además, también puedes utilizar `^` para ir al primer caracter, que no sea un espacio en blanco den la línea actual y `g_` para ir al último caracter que no sea un espacio en blanco en la línea actual. Si quieres ir a la columna `n` en la línea actual, puedes utilizar `n|`.

```
0     Ir al primer caracter de la línea actual
^     Ir al primer caracter que no es un espacio en blanco en la línea actual
g_    Ir al último caracter que no es un espacio en blanco en la línea actual
$     Ir al último caracter de la línea actual
n|    Ir a la columna n en la línea actual
```

También puede realizar una búsqueda en la línea actual con `f` y `t`. La diferencia entre `f` y `t` es que `f` te lleva a la primera letra de la primera conincidencia encontrada y `t` te lleva hasta (justo antes) de la primera letra de la primera coincidencia encontrada. Así que si quieres realizar una búsqueda y que el cursor se situe sobre la letra "h", utiliza `fh`. Si quieres realizar una búsqueda de la primera "h" y quieres que el cursor se situe justo antes de esa primera coincidencia utiliza `th`. Si después quieres seguir navegando hacia la *próxima* coincidencia de esa misma búsqueda, utiliza `;`. Para ir a la coincidencia previa de esa misma búsqueda en la línea actual, utiliza `,`.

Para realizar una búsqueda hacia atrás en el texto para esa letra "h" del ejemplo, utiliza `Fh`. Para seguir buscando más letras "h" en la misma dirección, utiliza `;`. Ten en cuenta que `;` no siempre dirige la búsqueda hacia adelante en el texto. `;`  repite la última dirección de búsqueda en la línea actual. Si utilizas `F`, `;` realizará la búsqueda hacia atrás, mientras que `,` busca hacia adelante en la línea actual. Si utilizas `f`, `;`buscará hacia adelante y `,` lo hará hacia atrás en la línea actual.

```
f    Search forward for a match in the same line
F    Search backward for a match in the same line
t    Search forward for a match in the same line, stopping before match
T    Search backward for a match in the same line, stopping before match
;    Repeat the last search in the same line
,    Repeat the last search in the same line backwards
```

Back at the previous example: 

```
const hello = "world";
```

With your cursor at the start of the line, you can go to the last character in current line (";") with one key press: `$`. If you want to go to "w" in "world", you can use `fw`. A good tip to go anywhere in a line is to look for least-common-letters like "j", "x", "z" near your target.

# Sentence and Paragraph Navigation

Next two navigation units are sentence and paragraph.

Let's talk about what a sentence is first.  A sentence ends with either `. ! ?` followed by an end-of-line, a space, or a tab.  You can jump to the next sentence with `)` and the previous sentence with `(`. 
```
(    Jump to the previous sentence
)    Jump to the next sentence
```

Let's look at some examples. Which phrases do you think are sentences and which aren't? Try navigating with `(` and `)` in Vim!

```
I am a sentence. I am another sentence because I end with a period. I am still a sentence when ending with an exclamation point! What about question mark? I am not quite a sentence because of the hyphen - and neither semicolon ; nor colon :

There is an empty line above me.
```
By the way, if you're having a problem with Vim not counting a sentence for phrases separated by `.` followed by a single line, you might be in `'compatible'` mode. Running `:set nocompatible` will fix it. In Vi, a sentence is a `.` followed by **two** spaces. You should have `nocompatible` set at all times.

Next, let's talk about what a paragraph is. A paragraph begins after each empty line and also at each set of a paragraph macro specified by the pairs of characters in paragraphs option.

```
{    Jump to the previous paragraph
}    Jump to the next paragraph

```

If you're not sure what a paragraph macro is, do not worry. The important thing is that a paragraph begins and ends after an empty line. This should be true most of the time.

Let's look at this example. Try navigating around with `}` and `{` (also, play around with sentence navigations `( )` to move around too!)

```
Hello. How are you? I am great, thanks!
Vim is awesome.
It may not easy to learn it at first...- but we are in this together. Good luck!

Hello again.

Try to move around with ), (, }, and {. Feel how they work.
You got this.
```

Check out `:h sentence` and `:h paragraph` to learn more.

# Match Navigation

Programmers often edit files containing codes. It may contain many parentheses, braces, and brackets and it can get confusing to know which parentheses you're inside of. 
Many programming languages use parentheses, braces, and brackets and you can get lost in them. If you're inside one of them, you can jump to the other pair (if it exists) with `%`. You can also use this to find out whether you have matching parentheses, braces, and brackets.

```
%    Navigate to another match, usually works for (), [], {}
```

Let's look at a Scheme code example because it uses parentheses extensively. Move around with `%` inside different parentheses.
```
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else
          (+ (fib (- n 1)) (fib (- n 2)))
        )))
```
I personally like to complement `%` using visual indicators plugins like [vim-rainbow](https://github.com/frazrepo/vim-rainbow).  For more, check out `:h %`.

# Line Number Navigation

You can jump to line number `n` with `nG`. For example, if you want to jump to line 7, use `7G`. To jump to the first line, use either `1G` or `gg`. To jump to the last line, use  `G`.

Often you don't know exactly which line you are targeting, but you know it's approximately at 70% of the whole file. In this case, you can do `70%`. To jump halfway through the file, you can do `50%`.

```
gg    Go to the first line
G     Go to the last line
nG    Go to line n
n%    Go to n% in file
```

By the way, if you want to see total lines in a file, you can use `CTRL-G`.

# Window Navigation

To quickly go to the top, middle, or bottom of your *window*, you can use `H`, `M`, and `L`. 

You can also pass a count to `H` and `L`. If you use `10H`, you will go to 10 lines below the top of window. If you use `3L`, you will go to 3 lines above the last line of window.
```
H     Go to top of screen
M     Go to medium screen
L     Go to bottom of screen
nH    Go n line from top
nL    Go n line from bottom
```

# Scrolling

To scroll, you have 3 speed increments: full-screen (`CTRL-F/CTRL-B`), half-screen (`CTRL-D/CTRL-U`), and line (`CTRL-E/CTRL-Y`).

```
Ctrl-e    Scroll down a line
Ctrl-d    Scroll down half screen
Ctrl-f    Scroll down whole screen
Ctrl-y    Scroll up a line
Ctrl-u    Scroll up half screen
Ctrl-b    Scroll up whole screen
```
You can also scroll relatively to the current line:
```
zt    Bring the current line near the top of your screen
zh    Bring the current line to the middle (half) of your screen
zb    Bring the current line near the bottom of your screen
```

# Search Navigation

Very often you know that a phrase exists inside a file. You can use search navigation to very quickly reach your target. To search for a phrase, you can use `/` to search forward and `?` to search backward. To repeat the last search you can use `n`. To repeat the last search going opposite direction, you can use `N`.

```
/    Search forward for a match
?    Search backward for a match
n    Repeat last search (same direction of previous search) 
N    Repeat last search (opposite direction of previous search)
```

Suppose you have this text:

```
let one = 1;
let two = 2;
one = "01";
one = "one";
let onetwo = 12;
```

If you are searching for "let", you can do `/let`. To quickly search for "let" again, you can just do `n`. To search for "let" again in opposite direction, you can do `N`. If you used `?let` to search, it will search for it backwards. If you use `n`, it will also search backwards, the same direction as `?let` (`N` will search for "let" forwards now).

You can enable search highlight with `:set hlsearch`. Now when you search for `/let`, it will highlight *all* matching phrases in the file. In addition, you can set incremental search with `:set incsearch`. This will highlight the pattern as you're still typing it. By default, your matching phrases will remain highlighted until you search for another phrase. This can quickly turn into an annoyance. To disable highlight, you can run `:nohlsearch`. Because I use this no-highlight feature frequently, I created a mapping: 

```
nnoremap <esc><esc> :noh<return><esc>
```

You can quickly search for the text under the cursor with `*` to search forward and `#` to search backward. If your cursor is on the string "one", pressing `*` will be the same as if you had done `/\<one\>`.

Both `\<` and `\>` in `/\<one\>` mean whole word search. It does not match "one" if it is a part of a bigger word. It will match for the word "one" but not "onetwo".  If your cursor is over "one" and you want to search forward to match whole or partial words like "one" and "onetwo", you need to use `g*` instead of `*`.

```
*     Search for whole word under cursor forward
#     Search for whole word under cursor backward
g*    Search for word under cursor forward
g#    Search for word under cursor backward

```
# Marking Position

You can use marks to save your current position and return to this position later. It's like a bookmark for text editing. You can set a mark with `mx`, where `x` can be any alphabetical letter `a-zA-Z`. There are two ways to return to mark: exact (line and column) with ```x`` and linewise (`'x`).

```
ma    Mark position with mark "a"
`a    Jump to line and column "a"
'a    Jump to line "a"
```

There is a difference between marking with lowercase letters (a-z) and uppercase letters (A-Z). Lowercase alphabets are local marks and uppercase alphabets are global marks (sometimes known as file marks).

Let's talk about local marks. Each buffer can have its own set of local marks. If I have two files opened, I can set a mark "a" (`ma`)  in the first file and another mark "a" (`ma)` in the second file. 

Unlike local marks where you can have a set of marks in each buffer, you only get one set of global marks. If you set `mA` inside `myFile.txt`, the next time you set `mA` in a different file, it will overwrite the "A" mark. One advantage of global marks is you can jump to any global mark even if you are inside a completely different project. Global marks can travel across files.

To view all marks, use `:marks`. You may notice from the marks list there are more marks other than `a-zA-Z`. Some of them are:

```
''    Jump back to the last line in current buffer before jump
``    Jump back to the last position in current buffer before jump
`[    Jump to beginning of previously changed / yanked text
`]    Jump to the ending of previously changed / yanked text
`<    Jump to the beginning of last visual selection
`>    Jump to the ending of last visual selection
`0    Jump back to the last edited file when exiting vim
```

There are more marks than the ones listed above. I won't cover them here because I think they are rarely used, but if you're curious, check out `:h marks`. 

# Jump

Lastly, let's talk about jumps in Vim. In Vim, you can "jump" to a different file or different part of a file with certain motions. Not all motions count as a jump, though. Going down with `j` does not count as a jump, even if you go 10 steps down with `10j`. Going to line 10 with `10G` counts as a jump. 

Here are the commands Vim consider as "jump" commands: 

```
'       Go to the marked line
`       Go to the marked position
G       Go to the line
/       Search forward
?       Search backward
n       Repeat the last search, same direction
N       Repeat the last search, opposite direction
%       Find match
(       Go to the last sentence
)       Go to the next sentence
{       Go to the last paragraph
}       Go to the next paragraph
L       Go to the the last line of displayed window
M       Go to the middle line of displayed window
H       Go to the top line of displayed window
[[      Go to the previous section
]]      Go to the next section
:s      Substitute
:tag    Jump to tag definition
``` 

I don't recommend memorizing this list. A good rule of thumb is, any motion that moves farther than a word and current line navigation is probably a jump. Vim keeps track of where you've been when you move around and you can see this list inside `:jumps`. For more, check out `:h jump-motions`.

Why are jumps useful? Because you can navigate the jump list with `Ctrl-o` to move up the jump list and `Ctrl-i` to move down the jump list. You can jump across different files, which I will discuss more in the next part.

# Learn Navigation the Smart Way

If you are new to Vim, this is a lot to learn. I do not expect anyone to remember everything immediately. It takes time before you can execute them without thinking.

I think the best way to get started is to memorize a few essential motions. I recommend starting out with `h, j, k, l, w, b, G, /, ?, n`. It should not take long to learn 10 motions and be comfortable with them.


To get better at navigation, I can offer two suggestions:

1. Watch for repeated actions. If you find yourself doing `l` repeatedly, look for a motion that will take you forward faster. You will find that you can use `w` to move between words. If you catch yourself repeatedly doing `w`, look if there is a motion that will take you to the end of the line immediately. You will find that you can use `$`. If you can describe your need verbally, there is a good chance Vim has a way to do it. 
2. Whenever you learn a new move, spend a considerable amount of time until you can do it without thinking.

Finally, you do not need to know every single Vim command to be productive. Most Vim users don't. I don't. Learn the commands that will help you accomplish your task at that moment.

Take your time. Navigation skill is a very important skill in Vim. Learn one small thing every day and learn it well. 
