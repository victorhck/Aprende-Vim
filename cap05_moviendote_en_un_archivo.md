# Capítulo 5: Moviéndote por un archivo

Al principio, moverte utilizando el teclado te puede parecer algo incómodo e increíblemente lento, ¡pero no desistas! Una vez que te acostumbres, podrás moverte a cualquier parte de un archivo de manera más rápida que utilizando el ratón.

En este capítulo, aprenderás los movimientos esenciales y cómo utilizarlos de manera eficiente. Ten en mente que este capítulo **no** es todo lo que Vim puede ofrecer en cuanto a la hora de moverse por un archivo. La meta aquí es presentar los movimientos más útiles para volverte productivo rápidamente. Si necesitas aprender más, echa un vistazo a la ayuda de Vim `:h motion.txt`.

## Navegando por caracteres

La unidad más básica de movimiento es moverse un carácter a la izquierda, abajo, arriba y a la derecha mediante las teclas h, j, k ,l.

```text
h   Izquierda
j   Abajo
k   Arriba
l   Derecha
```

También puedes moverte con las flechas de dirección de tu teclado. Si estás empezando, siéntete libre de utilizar cualquier método con el que te sientas más cómodo.

Personalmente prefiero la clásica combinación `hjkl` porque mi mano derecha permanece encima del teclado. Al hacer esto, hace que este más cerca de las teclas circundantes del teclado. Para _obligarme_ a utilizar esta combinación de teclas, he inhabilitado las flechas de dirección del teclado al usar Vim añadiendo estas líneas en el archivo `~/.vimrc`:

```text
noremap <Up> <NOP>
noremap <Down> <NOP>
noremap <Left> <NOP>
noremap <Right> <NOP>
```

También hay complementos para tratar de romper ese mal hábito. Uno de ellos es [vim-hardtime](https://github.com/takac/vim-hardtime). Para mi sorpresa, solo lleva unos pocos días el habituarse al uso de `hjkl`.

Si te preguntas porque Vim utiliza la combinación de teclas `hjkl` para moverse por el texto, es debido que el terminal Lear-Siegler ADM-3A donde Bill Joy empezó a desarrollar Vi, no tenía teclas con flechas en el teclado y utilizó `hjkl` como izquierda/abajo/arriba/derecha.

## Numeración relativa

Creo que es útil el tener establecido las opciones de `number` y `relativenumber`. Puedes hacerlo escribiendo lo siguiente en el archivo `.vimrc`:

```text
set relativenumber number
```

Esto muestra la línea actual en la que se encuentra el cursor y los números de línea relativos desde la posición de este.

¿Por qué esto puede ser útil? Esto me permite rápidamente ver cuantas a líneas de diferencia estoy desde la posición a la que quiero ir. Con esto, puedo ver fácilmente que mi objetivo está 12 líneas por debajo de mi posición actual, así que puedo ejecutar `12j`. Además, si estoy en la línea 69 y mi objetivo es desplazar el cursor a la línea 81, esto me lleva a tener que hacer un cálculo mental \(81 - 69 = 12\). Esto consume muchos recursos mentales. Cuanto menos tenga que pensar sobre donde quiero ir, mejor.

Esto es 100% una preferencia personal. Experimenta con las opciones `relativenumber` / `norelativenumber`, `number` / `nonumber` ¡y utiliza cualquier método que encuentres más útil!

## Cuenta tu movimiento

Hablemos sobre el argumento "contar". Los movimientos de Vim aceptan un argumento precedente numérico. He mencionado anteriormente que puedes desplazarte 12 líneas hacia abajo en el texto mediante `12j`. El 12 en `12j` es el número que lleva la cuenta.

La sintaxis para utilizar el número de conteo en tu movimiento es:

```text
[número] + movimiento
```

Puedes aplicar esto a todos tus movimientos. Si quieres moverte 9 caracteres a la derecha, en vez de presionar la tecla `l` 9 veces, puedes ejecutar simplemente `9l`.

## Navegación por palabras

Vamos a movernos por una unidad de movimiento mayor: _palabra_. Puedes mover el cursor al comienzo de la siguiente palabra \(`w`\), al final de la siguiente palabra \(`e`\), al comienzo de la palabra anterior \(`b`\)y al final de la palabra anterior \(`ge`\).

Además de esto, también está la _PALABRA_, distinta de palabra. Puedes mover el cursor al principio de la siguiente PALABRA \(`W`\), al final de la siguiente PALABRA \(`E`\), al comienzo de la PALABRA previa \(`B`\) y al final de la PALABRA previa \(`gE`\). Para hacerlo sencillo de recordar, PALABRA utiliza las mismas letras de movimiento que palabra excepto que son en mayúsculas.

```text
w     Mueve el cursor hacia adelante al comienzo de la siguiente palabra
W     Mueve el cursor hacia adelante al comienzo de la siguiente PALABRA
e     Mueve el cursor hacia adelante una palabra hasta el final de la siguiente palabra
E     Mueve el cursor hacia adelante una palabra hasta el final de la siguiente PALABRA
b     Mueve el cursor hacia atrás al principio de la palabra previa
B     Mueve el cursor hacia atrás al principio de la PALABRA previa
ge    Mueve el cursor hacia atrás al final de la palabra previa
gE    Mueve el cursor hacia atrás al final de la PALABRA previa
```

Entonces, ¿cuáles son las similitudes y diferencias entre una palabra y una PALABRA? Tanto una palabra como una PALABRA están separadas por espacios en blanco. Una palabra es una secuencia de caracteres que contienen _únicamente_ este grupo de caracteres `a-zA-Z0-9_`. Una PALABRA es una secuencia que incluyen todos los caracteres excepto el espacio en blanco \(cuando me refiero a espacio en blanco, esto incluye tanto un espacio, una separación por tabulador o un fin de línea\) Para aprender más, echa un vistazo a la ayuda en Vim sobre este tema con estos comandos: `:h word` o `:h WORD`.

Por ejemplo, supongamos que tenemos la siguiente frase:

```text
const hello = "world";
```

Con el cursor al comienzo de la línea, ve al final de la línea con `l`, te llevará pulsar 21 veces la tecla. Utilizando `w`, solo te llevará pulsar 6 veces. Utilizando `W`, solo te llevará 4 pulsaciones. Tanto palabra como PALABRA son buenas opciones para mover el cursor en pequeñas distancias.

Sin embargo, puedes pasar de "c" a ";" con una pulsación de tecla con la navegación de línea actual.

## Navegación de línea actual

Mientras editas, a menudo necesitas navegar horizontalmente en la misma línea. Para saltar al primer carácter de la línea actual, utiliza `0`. Para ir al último carácter de la línea actual, utiliza `$`. Además, también puedes utilizar `^` para ir al primer carácter, que no sea un espacio en blanco de la línea actual y `g_` para ir al último carácter que no sea un espacio en blanco en la línea actual. Si quieres ir a la columna `n` en la línea actual, puedes utilizar `n|`, donde "n" será el número de la columna donde quieres desplazar el cursor.

```text
0     Ir al primer caracter de la línea actual
^     Ir al primer caracter que no es un espacio en blanco en la línea actual
g_    Ir al último caracter que no es un espacio en blanco en la línea actual
$     Ir al último caracter de la línea actual
n|    Ir a la columna n en la línea actual
```

También puedes realizar una búsqueda en la línea actual con `f` y `t`. Ambas opciones buscan hacia adelante en la línea actual, la diferencia entre `f` y `t` es que `f` situa el cursor en el mismo lugar de la primera letra de la primera coincidencia encontrada y `t` te lleva *hasta justo* antes de la primera letra de la primera coincidencia encontrada. Así que si quieres realizar una búsqueda y que el cursor se sitúe sobre la letra "h", utiliza `fh`. Si quieres realizar una búsqueda de la primera "h" y quieres que el cursor se sitúe justo antes de esa primera coincidencia utiliza `th`. Si después quieres seguir navegando hacia la _próxima_ coincidencia de esa misma búsqueda, utiliza `;`. Para ir a la coincidencia previa de esa misma búsqueda en la línea actual, utiliza `,`.

`F` y `T` son las opciones similares a `f` y `t` pero para buscar hacia atrás en el texto. Para buscar hacia atrás una "h", ejecuta `Fh`. Para seguir buscando "h" en la misma dirección, usa `;`. Ten en cuenta que `;` después de `Fh` continua la búsqueda hacia atrás y `,` después de `Fh` busca hacia adelante.

```text
f    Busca hacia adelante una coincidencia en la línea actual
F    Busca hacia atrás una coincidencia en la línea acual
t    Busca hacia adelante una coincidencia en la línea actual, posicionando el cursor antes de la coincidencia
T    Busca hacia atrás una coincidencia en la línea actual, posicionando el cursor antes de la coincidencia
;    Repite la última búsqueda en la línea actual en la misma dirección
,    Repite la última búsqueda en la línea actual en dirección contraria
```

Volviendo al ejemplo previo:

```text
const hello = "world";
```

Con el curso al comienzo de la línea, puedes ir al último carácter de la línea actual \(";"\) pulsando una única tecla: `$`. Si quieres ir a la letra "w" en la palabra "world", puedes utilizar `fw`. Un buen truco para ir a cualquier punto de la línea actual es echar un vistazo a ver si existe alguna letra poco común como "w", "x" o "z" cerca de tu objetivo.

## Navegación por frase y párrafo

Las siguientes dos unidades por las que podemos navegar por un texto son la frase y el párrafo.

Primero vamos a hablar sobre lo que es una frase. Una frase termina con alguno de estos signos de puntuación `. ! ?` seguido por un final de línea, un espacio en blanco o una tabulación. Puedes saltar a la siguiente frase mediante `)` y a la frase previa con `(`.

```text
(    Salta a la frase previa
)    Salta a la siguiente frase
```

Echemos un vistazo a algunos ejemplos. ¿Qué frases crees que lo son y cuales no? ¡Trata de navegar con `(` y `)` en Vim!

```text
Yo soy una frase. Yo soy otra frase porque termino con un punto. ¡Yo también soy una frase porque acabo con un signo de exclamación! ¿Qué pasa con el signo de interrogación? Yo no soy una frase, debido al guión - punto y coma ; o los dos puntos :

Hay una línea vacía encima de mí.
```

Por cierto, si tienes problemas con Vim porque no considera una frase frases separadas por `.` seguido de una linea simple, quizás estás en el modo `'compatible'`. Añade `:set nocompatible` en vimrc. En Vi, una frase termina en `.` seguido de **dos** espacios en blanco. Deberías tener al ajuste `nocompatible` en todo momento.

Vamos a hablar sobre lo que es un párrafo. Un párrafo comienza después de cada línea vacía y también en cada conjunto de una macro de párrafo especificada por los pares de caracteres en la opción de párrafos.

```text
{    Salta al párrafo previo
}    Salta al párrafo siguiente
```

Si no estás seguro de qué es un macro de párrafo, no te preocupes. Lo importante es que un párrafo comienza y termina después de una línea vacía. Esto debería ser cierto en la mayoría de las veces.

Echemos un vistazo a este ejemplo. Trata de navegar por el texto mediante `}` y `{` \(¡también puedes practicar con la navegación por frases `( )`!\)

```text
Hola. ¿Cómo estás? ¡Estoy genial, gracias!
Vim es genial.
Puede no ser sencillo de aprender al principio...- pero estamos en esto juntos. ¡Buena suerte!

Hola de nuevo.

Trata de moverte por el texto con ), (, }, y {. Siente cómo funcionan.
Lo tienes.
```

Echa un vistazo a la ayuda de Vim con `:h sentence` y `:h paragraph` para aprender más.

## Navegación entre parejas

Los programadores a menudo editan archivos que contienen código. Puede contener muchos paréntesis, llaves y corchetes. Te puedes perder fácilmente entre ellos. Si estás dentro de uno, puedes saltar a la otra pareja \(si existe\) con `%`. También puedes utilizar esto para encontrar cualquier pareja de paréntesis, llaves o corchetes.

```text
%    Navega de una a otra pareja, normalmente funciona con (), [], {}
```

Echemos un vistazo a este código de ejemplo en Scheme, porque hace bastante uso de los paréntesis. Vamos a movernos con `%` dentro de los diferentes paréntesis.

```text
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else
          (+ (fib (- n 1)) (fib (- n 2)))
        )))
```

Personalmente, me gusta complementar `%` utilizando complementos que añadan un indicador visual como [vim-rainbow](https://github.com/frazrepo/vim-rainbow). Para más información, echa un vistazo a la ayuda en Vim con `:h %`.

## Navegación por número de línea

Puedes saltar a un número de línea `n` con `nG`. Por ejemplo, si quieres saltar a la línea 7, utiliza `7G`. Para saltar a la primera línea del texto, puedes utilizar tanto `1G` o `gg`. Para saltar a la última línea del texto, utiliza `G`.

A menudo no sabes exactamente en qué línea está el sitio al que te quieres dirigir, pero sabes que aproximadamente se encuentra al 70% del archivo completo. En ese caso, puedes ejecutar `70%`. Para saltar a la mitad del archivo, puedes ejecutar `50%`.

```text
gg    Va a la primera línea
G     Va a la última línea
nG    Va a la línea n
n%    Va al n% del archivo
```

Por cierto, si quieres ver el total de líneas de un archivo puedes utilizar `Ctrl-g`.

## Navegación por la ventana

Para ir rápidamente a la parte superior, central o inferior de tu _ventana_, puedes utilizar las teclas `H`, `M`, o `L`. \(_N.d.T: una regla para acordarse de estos atajos puede ser su correspondencia en inglés. `H` de High, `M` de Medium y `L` de Low_\).

También se puede pasar un número de conteo a `H` y `L`. Si utilizas `10H`, irás 10 líneas por debajo de la parte superior de la ventana. Si utilizas `3L`, se colocará el cursor 3 líneas por encima de la última línea de la ventana.

```text
H     Ir a la parte superior de la ventana
M     Ir a la parte media de la ventana
L     Ir a la parte inferior de la ventana
nH    Va a la línea n desde la parte superior de la ventana
nL    Va a la línea n desde la parte inferior de la ventana
```

## Desplazándose

Para desplazarse por el texto \(o hacer _scroll_\), tienes 3 velocidades incrementales: Pantalla completa \(`Ctrl-F/Ctrl-B`\), media pantalla \(`Ctrl-D/Ctrl-U`\), y línea a línea \(`Ctrl-E/Ctrl-Y`\).

```text
Ctrl-E    Desplaza el texto hacia arriba una línea
Ctrl-D    Desplaza media pantalla hacia arriba
Ctrl-F    Desplaza una pantalla completa hacia arriba el texto
Ctrl-Y    Desplaza el texto hacia abajo una línea
Ctrl-U    Desplaza media pantalla hacia abajo
Ctrl-B    Desplaza una pantalla completa hacia abajo el texto
```

También puedes desplazarte de manera relativa en función de la línea actual donde se encuentre el cursor:

```text
zt    LLeva la línea actual donde está el cursor cerca de la parte superior de la pantalla
zz    LLeva la línea actual donde está el cursor a la parte media de la pantalla
zb    LLeva la línea actual donde está el cursor cerca de la parte inferior de la pantalla
```

## Navegación por búsqueda

Muy a menudo, sabes que existe una frase concreta dentro del archivo. Puede utilizar la navegación por búsqueda para llegar hasta tu objetivo rápidamente. Para buscar una frase, puedes utilizar `/` para hacer una búsqueda hacia adelante en el texto o `?` para buscar hacia atrás desde la posición del cursor. Para repetir la última búsqueda, puedes utilizar `n`. Para repetir la última búsqueda pero en dirección opuesta, puedes utilizar `N`.

```text
/    Busca hacia adelante una coincidencia
?    Busca hacia atrás una coincidencia
n    Repite la última búsqueda (en la misma dirección que la búsqueda previa)
N    Repite la última búsqueda (en la dirección opuesta que la búsqueda previa)
```

Supongamos que tenemos el siguiente texto:

```text
let one = 1;
let two = 2;
one = "01";
one = "one";
let onetwo = 12;
```

Si estás buscando la cadena "let", puedes hacerlo mediante `/let`. Para buscar de nuevo "let" rápidamente, simplemente presiona `n`. Para buscar "let" de nuevo, pero esta vez en dirección contraria, hazlo mediante la tecla `N`. Si utilizas `?let` para realizar la búsqueda, buscará hacia atrás en el texto. Si usas `n`, seguirá realizando la búsqueda en esa dirección en el texto, `N` realizará ahora la búsqueda hacia adelante.

Puedes habilitar el resaltado del texto buscado mediante el ajuste `:set hlsearch`. Ahora cuando busques `/let`, resaltará _todas_ las coincidencias que haya en todo el texto. Además, puedes configurar una búsqueda incremental con `:set incsearch`. Esto resaltara el patrón de búsqueda mientras estás escribiéndolo. De manera predeterminada, la cadena buscada permanecerá resaltada, hasta que hagas otra búsqueda. Esto puede convertirse rápidamente en un inconveniente. Para inhabilitar ese resaltado, puedes ejecutar `:nohlsearch`. Como utilizo esa funcionalidad de quitar el resaltado de manera frecuente, he creado un mapeado de esa funcionalidad. Para ello he añadido la siguiente línea en el archivo .vimrc:

```text
nnoremap <esc><esc> :noh<return><esc>
```

También puedes buscar rápidamente el texto que se encuentra bajo el cursor con `*` para buscar esa coincidencia hacia adelante en el texto, y con `#` realizará la búsqueda hacia atrás. Si tu cursor, está sobre el texto "one", pulsando `*` hará lo mismo que si hubieras escrito `/\<one\>`.

Tanto `\<` como `\>` en `/\<one\>` significa que haga una búsqueda de la palabra completa. Y que no encuentre "one" dentro de una palabra mayor. Es decir, encontrará "one" pero no "onetwo". Si tu cursor está sobre "one" y quieres buscar las coincidencias de la palabra completa o que forme parte de otras palabras más grandes, necesitas utilizar `g*` en vez de `*`.

```text
*     Busca la palabra completa bajo el cursor hacia adelante
#     Busca la palabra completa bajo el cursor hacia atrás
g*    Busca la palabra bajo el cursor hacia adelante
g#    Busca la palabra bajo el cursor hacia atrás
```

## Marcando la posición

Puedes utilizar marcas para guardar la posición actual del cursor y poder volver a esa posición más tarde. Es como un marcador para la edición de texto. Puedes establecer un marcador con `mx`, donde `x` puede ser cualquier letra del alfabeto `a-zA-Z`. Existen dos maneras de volver a la marca establecida: de manera exacta \(línea y columna\) mediante ```x`` y a la línea con `'x`.

```text
ma    Marca una posición, estableciendo la marca "a" en la posición actual del cursor
`a    Salta a la línea y columna donde se encuentra "a"
'a    Salta a la línea donde se encuentra "a"
```

Existe una diferencia entre establecer marcas con letras minúsculas \(a-z\) y mayúsculas \(A-Z\). Las marcas con letras minúsculas, son marcas locales y las marcas con letras mayúsculas son marcas globales \(a veces conocidas como marcas de archivo\).

Vamos a hablar sobre las marcas locales. Cada _buffer_ puede tener su propio juego de marcas locales. Si tengo dos ficheros abiertos, puedo establecer una marca "a" \(`ma`\) en el primer archivo y otra marca "a" \(`ma`\) en el segundo archivo.

A diferencia de las marcas locales con las que puedes tener un juego de marcas en cada _buffer_, solo puedes tener un juego de marcas globales. Si estableces una marca global "A" \(`mA`\) dentro de `miArchivo.txt`, la próxima vez que ejecutes `mA` en un archivo diferente, sobreescribirá la marca "A". Una ventaja de las marcas globales es que puedes saltar a cualquier marca global incluso si está dentro de un proyecto completamente diferente. Las marcas globales pueden viajar a través de los archivos.

Para ver todas las marcas establecidas puedes hacerlo mediante `:marks`. Puedes comprobar que en la lista de marcas hay más marcas que las de `a-zA-Z`. Algunas de ellas son:

```text
''    Salta hacia atrás a la última línea donde se encontraba el cursor en el *buffer* actual antes de saltar
``    Salta hacia atrás a la última posición en el *buffer* actual a la última posición en el *buffer* actual antes de saltar
`[    Salta al comienzo del texto previamente cambiado / pegado
`]    Salta al final del texto previamente cambiado / pegado
`<    Salta al comienzo de la última selección visual
`>    Salta al final de la última selección visual
`0    Salta hacia atrás al último archivo editado cuando salió de Vim
```

Hay más marcas que las listadas aquí. No se tratarán todas aquí, porque creo que muchas no son muy comunes y se utilizan muy poco, pero si tienes curiosidad al respecto, echa un vistazo a la ayuda de Vim con: `:h marks`.

## Saltar

Por último, hablemos de los saltos en Vim. En Vim, puede "saltar" a un archivo diferente o a una parte diferente de un archivo con ciertos movimientos. Sin embargo, no todos los movimientos cuentan como un salto. Bajar con `j` no cuenta como un salto, incluso si bajas 10 pasos con `10j`. Ir a la línea 10 con `10G` si cuenta como un salto.

Aquí tienes los comandos que Vim considera como comandos de "salto":

```text
'       Ir a la línea marcada
`       Ir a la posición marcada
G       Ir a la línea
/       Buscar hacia adelante
?       Buscar hacia atrás
n       Repetir la úlrima búsqueda, en la misma dirección
N       Repetir la última búsqueda, en la dirección opuesta
%       Encontrar la pareja
(       Ir a la frase anterior
)       Ir a la frase siguiente
{       Ir al párrafo anterior
}       Ir al párrafo siguiente
L       Ir a la última línea mostrada en la ventana
M       Ir a la línea media mostrada en la ventana
H       Ir a la línea superior mostrada en la ventana
[[      Ir a la sección previa
]]      Ir a la sección siguiente
:s      Substituir
:tag    Saltar a la etiqueta de definición
```

No recomiendo memorizar esta lista. Una buena regla para recordarla es, que cualquier movimiento que mueve el cursor más de una palabra o más de la línea actual, probablemente sea un salto. Vim memoriza los lugares por los que te has movido y puedes ver esto dentro de `:jumps`. Para más información echa un vistazo a la ayuda de Vim con `:h jump-motions`.

¿Por qué los saltos son útiles? Porque puedes navegar por la lista de saltos realizados mediante `Ctrl-O` para moverte hacia arriba por la lista de saltos y con `Ctrl-I` para moverte hacia abajo en la lista. También puedes saltar por diferentes archivos, lo que veremos en la siguiente parte.

## Aprender a navegar de la manera más inteligente

Si acabas de llegar a Vim, aquí tienes mucho para aprender. No espero que nadie recuerde todo de manera inmediata. Lleva su tiempo el ejecutar estos comandos sin que tengas que parar a pensarlos.

Creo que la mejor manera de empezar es memorizar algunos comandos de movimiento esenciales. Recomiendo empezar con estos 10 movimientos: `h, j, k, l, w, b, G, /, ?, n`. Repítelos suficientemente como para usarlos sin pensar en ellos.

Para ir mejorando en la navegación, te ofrezco dos sugerencias:

1. Estate alerta de acciones repetidas que realices. Si encuentras que ejecutas `l` de manera repetida, busca un movimiento que te lleve de una manera más rápida a tu destino. Encontrarás que puedes utilizar `w` para moverte entre palabras. Si descubres que ejecutas `w` de manera repetitiva, busca si hay un comando de movimiento que te lleve al final de la línea de manera inmediata. Encontrarás que puedes utilizar `$`. Si puedes describir de manera verbal lo que necesitas, hay muchas posibilidades de que Vim tenga una manera de hacer lo que necesitas. 
2. Cada vez que aprendas un movimiento, pasa un tiempo considerable hasta que lo puedas realizar de manera automática sin necesidad de pensarlo.

Finalmente, no necesitas conocer cada uno de los comandos de movimiento de Vim para ser productivo. La mayoría de los usuarios de Vim no los conocen. Yo tampoco. Aprende los comandos que te ayudarán a realizar tu tarea en cada momento.

Toma tu tiempo. La habilidad para navegar en Vim es muy importante. Aprende una pequeña cosa cada día y apréndela bien.

