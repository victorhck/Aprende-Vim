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
f    Busca hacia adelante una coincidencia en la línea actual
F    Busca hacia atrás una coincidencia en la línea acual
t    Busca hacia adelante una coincidencia en la línea actual, posicionando el cursor antes de la coincidencia
T    Busca hacia atrás una coincidencia en la línea actual, posicionando el cursor antes de la coincidencia
;    Repite la última búsqueda en la línea actual
,    Repite la última búsqueda en la línea actual en dirección contraria
```

Volviendo al ejemplo previo

```
const hello = "world";
```

Con el curso al comienzo de la línea, puedes ir al último caracter de la línea actual (";) pulsando una única tecla: `$`. Si quieres ir a la letra "w" en la palabra "world", puedes utilizar `fw`. Un buen truco para ir a cualquier punto de la línea actual es echar un vistazo a ver si existe alguna letra poco común como "w", "x" o "z" cerca de tu objetivo.

# Navegación por frase y párrafo

Las siguientes dos unidades por las que podemos navegar por un texto son la frase y el párrafo.

Primero vamos a hablar sobre lo que es una frase. Una frase termina con alguno de estos signos de puntuación `. ! ?` seguido por un final de línea, un espacio en blanco o una tabulación. Puedes saltar a la siguiente frase mediante `)` y a la frase previa con `(`. 
```
(    Salta a la frase previa
)    Salta a la siguiente frase
```

Echemos un vistazo a algunos ejemplos. ¿Qué frases crees que lo son y cuales no? Trata de navegar con `(` y `)` en Vim!

```
Yo soy una frase. Yo soy otra frase porque termino con un punto. ¡Yo también soy una frase porque acabo con un signo de exclamación! ¿Qué pasa con el signo de interrogación? Yo no soy una frase, debido al guión - punto y coma ; o los dos puntos :

Hay una línea vacía encima de mí.
```

Por cierto, si tienes problemas con Vim porque no considera uan frase frases separadas por `.` seguido de una linea simple, quizás estás en el modo `'compatible'`. Ejecutando `:set nocompatible` los solucionará. En Vi, una frase termina en `.` seguido de **dos** espacios en blanco. Debería tener al ajuste `nocompatible` en todo momento.

Ahora, vamos a hablar sobre lo que es un párrafo. Un párrafo comienza después de cada línea vacía y también en cada conjunto de una macro de párrafo especificada por los pares de caracteres en la opción de párrafos.

```
{    Salta al párrafo previo
}    Salta al párrafo siguiente

```

Si no estás seguro de qué es un macro de párrafo, no te preocupes. Lo importante es que un párrafo comienza y termina después de una línea vacía. Esto debería ser cierto en la mayoría de las veces.

Echemos un vistazo a este ejemplo. Trata de navegar por el texto mediante `}` y `{` (¡también puedes practicar con la navegación por frases `( )`)

```
Hola. ¿Cómo estás? ¡Estoy genial, gracias!
Vim es genial.
Puede no ser sencillo de aprender al principio...- pero estamos en esto juntos. ¡Buena suerte!

Hola de nuevo.

Trata de moverte por el texto con ), (, }, y {. Siente cómo funcionan.
Lo tienes.
```

Echa un vistazo a la ayuda de Vim con `:h sentence` y `:h paragraph` para aprender más. 

# Navegación entre parejas

Los programadores a menudo editan archivos que continen código. Puede contener muchos paréntesis, llaves y corchetes y puede ser confuso saber dentro de qué pareja de paréntesis se encuentra el cursor. Muchos lenguajes de programación utilizan paréntesis, llaves y corchetes y te puedes perder entre ellos. Si estás dentro de una pareja de estos signos, puedes saltar a la otra pareja del mismo (si existe) mediante `%`. También puedes usar esto para averiguar si tienes paréntesis, llaves y corchetes que hagan pareja o falta alguna.

```
%    Navega de una a otra pareja, normalmente funciona con (), [], {}
```

Echemos un vistazo a este código de ejemplo en Scheme, porque hace bastante uso de los paréntesis. Vamos a movernos con `%` dentro de los diferente paréntesis.
```
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else
          (+ (fib (- n 1)) (fib (- n 2)))
        )))
```
Personalmente, me gusta complementar `%` utilizando complementos que añadan un indicador visual como [vim-rainbow](https://github.com/frazrepo/vim-rainbow).  Para más información, echa un vistazo a la ayuda en Vim con `:h %`.

# Navegación por número de línea

Puedes saltar a un número de línea `n` con `nG`. Por ejemplo, si quieres saltar a la línea 7, utilza `7G`. Para saltar a la primera línea del texto, puedes utilizar tanto `1G` o `gg`. Para saltar a la última línea del texto, utiliza `G`.

A menudo no sabes exactamente en qué línea está el sitio al que te quieres dirigir, pero sabes que aproximadamente se encuentra al 70% del archivo completo. En ese caso, puedes ejecutar `70%`. Para saltar a la mitad del archivo, puedes ejecutar `50%`.

```
gg    Va a la primera línea
G     Va a la última línea
nG    Va a la línea n
n%    Va al n% del archivo
```

Por cierto, si quieres ver el total de líneas de un archivo puedes utilizar `CTRL-G`.

# Navegación por la ventana

Para ir rápidamente a la parte superior, central o inferior de tu *ventana*, puedes utilizar las teclas `H`, `M`, o `L`. 

También se puede pasar un número de conteo a `H` y `L`. Si utilizas `10H`, irás 10 líneas por debajo de la parte superior de la ventana. Si utilizas `3L`, se colocará el cursor 3 líneas por encima de la última línea de la ventana.
```
H     Ir a la parte superior de la ventana
M     Ir a la parte media de la ventana
L     Ir a la parte inferior de la ventana
nH    Va a la línea n desde la parte superior de la ventana
nL    Va a la línea n desde la parte inferior de la ventana
```

# Desplazándose

Para desplazarse por el texto (o hacer *scroll*), tienes 3 velocidades incrementales: Pantalla completa (`CTRL-F/CTRL-B`), media pantalla (`CTRL-D/CTRL-U`), y línea a línea (`CTRL-E/CTRL-Y`).

```
Ctrl-e    Desplaza el texto hacia arriba una línea
Ctrl-d    Desplaza media pantalla hacia arriba
Ctrl-f    Desplaza una pantalla completa hacia arriba el texto
Ctrl-y    Desplaza el texto hacia abajo una línea
Ctrl-u    Desplaza media pantalla hacia abajo
Ctrl-b    Desplaza una pantalla completa hacia abajo el texto
```
También puedes desplazrte de manera relativa en función de la línea actual donde se encuentre el cursor:
```
zt    LLeva la línea actual donde está el cursor cerca de la parte superior de la pantalla
zh    LLeva la línea actual donde está el cursor a la parte media de la pantalla
zb    LLeva la línea actual donde está el cursor cerca de la parte inferior de la pantalla
```

# Navegación por búsqueda

Muy a menudo, sabes que existe una frase concreta dentro del archivo. Puede utilizar la navegación por búsqueda para llegar hasta tu objetivo rápidamente. PAra buscar una frase, puedes utilizar `/` para hacer una búsqueda hacia adelante en el texto o `?` para buscar hacia atrás. Para repetir la última búsqueda, puedes utilizar `n`. Para repetir la última búsqueda pero en dirección opuesta, puedes utilizar `N`.

```
/    Busca hacia adelante una coincidencia
?    Busca hacia atrás una coincidencia
n    Repite la última búsqueda en la misma dirección que la búsqueda previa 
N    Repite la última búsqueda en la dirección opuesta que la búsqueda previa
```

Supongamos que tenemos el siguiente texto:

```
let one = 1;
let two = 2;
one = "01";
one = "one";
let onetwo = 12;
```

Si estás buscando la cadena "let", puedes hacerlo mediante `/let`. Para buscar de nuevo "let" rápidamente, simplemente presiona `n`. Para buscar "let" de nuevo, pero esta vez en dirección contraria, hazlo mediante la tecla `N`. Si utilizas `?let` para realizar la búsqueda, buscará hacia atrás en el texto. Si usas `n`, seguirá realizando la búsqueda en esa dirección en el texto, `N` realizará ahora la búsqueda hacia adelante.

Puedes habilitar el resaltado del texto buscado mediante el ajuste `:set hlsearch`. Ahora cuando busques `/let`, resaltará *todas* las coincidencias que haya en todo el texto. Además, puedes configurar una búsqueda incremental con `:set incsearch`. Esto resaltara el patrón de búsqueda mientras estás escribiéndolo. De manera predeterminada, la cadena buscada permanecerá resaltada, hasta que hagas otra búsqueda. Esto puede convertirse rápidamente en un inconveniente. Para inhabilitar ese reslatado, puedes ejecutar `:nohlsearch`. Como utilizo esa funcionalidad de quitar el reslatado de manera frecuente, he creado un mapedado de esa funcionalidad. Para ello he añadido la siguiente línea en el archivo .vimrc: 

```
nnoremap <esc><esc> :noh<return><esc>
```

También puedes buscar rápidamente el texto que se encuentra bajo el cursor con `*` para buscar esa coincidencia hacia adelante en el texto, y con `#` realizará la búsqueda hacia atrás. Si tu cursor, está sobre el texto "one", pulsando `*` hará lo mismo que si hubieras escrito `/\<one\>`.

Tanto `\<` como `\>` en `/\<one\>` significa que haga una búsqueda de la palabra completa. Y que no encuentre "one" dentro de una palabra mayor. Es decir, encontrará "one" pero no "onetwo". Si tu cursor está sobre "one" y quieres buscar las coincidencias de la palabra completa o que forme parte de otras palabras más grandes, necesitas utilizar `g*` en vez de `*`.

```
*     Busca la palabra completa bajo el cursor hacia adelante
#     Busca la palabra completa bajo el cursor hacia atrás
g*    Busca la palabra bajo el cursor hacia adelante
g#    Busca la palabra bajo el cursor hacia atrás

```
# Marcando la posición

Puedes utilizar marcas para guardar la posición actual del cursor y poder volver a esa posición más tarde. Es como un marcador para la edición de texto. Puedes establecer un marcador con `mx`, donde `x` puede ser cualquier letra del alfabeto `a-zA-Z`. Existen dos maneras de volver a la marca establecida: de manera exacta (línea y columna) mediante ```x`` y a la línea con `'x`.

```
ma    Marca una posición, estableciendo la marca "a" en la posición actual del cursor
`a    Salta a la línea y columna donde se encuentra "a"
'a    Salta a la línea donde se encuentra "a"
```

Existe una diferencia entre establecer marcas con letras minúsculas (a-z) y mayúsculas (A-Z). Las marcas con letras minúsculas, son marcas locales y las marcas con letras mayúsculas son marcas globales (a veces conocidas como marcas de archivo).

Vamos a hablar sobre las marcas locales. Cada *buffer* puede tener su propio juego de marcas locales. Si tengo dos ficheros abiertos, puedo establecer una marca "a" (`ma`) en el primer archivo y otra marca "a" (`ma)` en el segundo archivo.

A diferencia de las marcas locales con las que puedes tener un juego de marcas en cada *buffer*, solo puedes tener un juego de marcas globales. Si estableces una marca global "A" (`mA`) dentro de `miArchivo.txt`, la próxima vez que ejecutes `mA` en un archivo diferente, sobreescribirá la marca "A". Una ventaja de las marcas globales es que puedes saltar a cualquier marca global incluso si está dentro de un proyecto completamente diferente. Las marcas globales pueden viajar a través de los archivos.

Para ver todas las marcas establecidas puedes hacerlo mediante `:marks`. Puedes comprobar que en la lista de marcas hay más marcas que las de `a-zA-Z`. Algunas de ellas son:

```
''    Salta hacia atrás a la última línea donde se encontraba el cursor en el *buffer* actual antes de saltar
``    Salta hacia atrás a la última posición en el *buffer* actual a la última posición en el *buffer* actual antes de saltar
`[    Salta al comienzo del texto previamente cambiado / pegado
`]    Salta al final del texto previamente cambiado / pegado
`<    Salta al comienzo de la última selección visual
`>    Salta al final de la última selección visual
`0    Salta hacia atrás al último archivo editado cuando salió de Vim
```

Hay más marcas que las listadas aquí. No se tratarán todas aquí, porque creo que muchas no son muy comunes y se utilizan muy poco, pero si tienes curiosidad al respecto, echa un vistazo a la ayuda de Vim con: `:h marks`. 

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
