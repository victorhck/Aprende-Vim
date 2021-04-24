# Capítulo 7: El comando del punto

Cuando estás editando un texto, evita todo lo que puedas, volver a hacer lo que ya hiciste. En este capítulo, aprenderás a utilizar el comando del punto para volver a realizar los cambios previos de una manera sencilla. Es el comando más simple y más versátil para reducir las repeticiones.

## Uso

Tal como sugiere su nombre, puedes utilizar el comando del punto, simplemente presionando la tecla del signo del punto \(`.`\).

Por ejemplo, si quieres reemplazar todo el texto "let" con "const" en el siguiente código:

```text
let one = "1";
let two = "2";
let three = "3";
```

Primero, utiliza `/let` para colocarte encima de la primera coincidencia que encuentre Vim. Segundo, utiliza `cwconst<esc>` para reemplazar "let" con "const" . Tercero, utiliza `n` para encontrar la siguiente coincidencia en el texto, utilizando la búsqueda que hemos realizado previamente. Finalmente, repite la acción de sustitución que realizaste con el comando del punto \(`.`\). Continua haciendo `n . n .` hasta que reemplaces todas las coincidencias que quieras.

En este caso el comando del punto, repite la secuencia `cwconst<esc>`. Esto te ahorra el tener que realizar ocho pulsaciones de teclas en cada cambio teniendo que pulsar solo una tecla, el punto.

## ¿Qué es un cambio?

Si buscas en Vim la definición del comando del punto \(`:h .`\), verás que menciona que el comando repite el último cambio. ¿Qué es un cambio?

Cada vez que realizas una actualización del contenido del _buffer_ actual \(añadir, modificar o eliminar\) utilizando cualquier comando del _modo normal_, estás realizando un cambio. Las excepciones son actualizaciones realizadas mediante comandos ejecutados en la línea de comandos \(los comando que comienzan con `:`\), esos no cuentan como cambios.

En el primer ejemplo, comprobaste que `cwconst<esc>` fue el cambio. Ahora supongamos que tenemos esta frase:

```text
tarta, patatas, zumo de frutas,
```

Ahora vamos a borrar, el texto desde el comienzo de la línea hasta donde encontremos la primera coma. Puedes realizar esto mediante `df,`. Repite la acción con `.` dos veces más hasta que borres todas las palabras.

Probemos otro ejemplo

```text
tarta, patatas, zumo de frutas,
```

Esta vez solo necesitas borrar la coma, no la palabra que la precede. Con el cursor en el inicio de la frase, ve hasta la primera coma de la frase mediante el comando `f,`. Elimina el signo de puntuación bajo el cursor con el comando `x`. Repite mediante `.` la acción un par de veces más. ¿Sencillo, verdad? Espera, ¡no funcionó! ¿Por qué?

En Vim, los cambios no incluyen los movimientos, porque estos no actualizan el contenido del _buffer_. Cuando ejecutamos `f,x`, realizamos dos acciones diferentes: el comando `f,` mueve el cursor y `x` actualiza el _buffer_. Solo este último realiza un cambio. A diferencia que con `df,` del ejemplo anterior. En este, `f,` indica al operador de borrado donde eliminar el contenido. Es una parte de todo la operación de borrado, `df,`.

Finalicemos la última tarea. Después de ejecutar `f,` seguido por `x`, ve a la siguiente coma mediante `;` para repetir la última búsqueda realizada con `f`. Después utiliza `.` para eliminar el caracter bajo el cursor. Repite la secuencia `; . ; .` hasta que todo este eliminado. El comando completo sería `f,x;.;.`.

Probemos otro ejemplo:

```text
tarta
patatas
zumo de frutas
```

Vamos a añadir una coma al final de cada línea. Empezando por la primera línea, vamos a ejecutar lo siguiente `A,<esc>j`. A estas alturas, ya te das cuenta que `j` no realiza ningún cambio. El cambio es solo realizado con `A,`. Te puedes mover y repetir el cambio con `j . j .`. El comando completo sería `A,<esc>j.j.`.

Cada acción desde el momento que presionas el operador del comando de insertar \(`A`\) hasta que lo abandonas con el comando \(`<esc>`\) es considerado un cambio. Vim te permite controlar no solo que texto añadir, también _donde_ añadirlo. También puedes añadirlo antes del cursor \(`i`\), después del cursor \(`a`\), en una nueva línea debajo del cursor \(`o`\), en una línea sobre el cursor \(`O`\), al final de la línea actual \(`A`\) o al comienzo de la línea actual \(`I`\). Para refrescar estos conceptos, echa un vistazo al capítulo [Insert Mode](https://github.com/victorhck/Aprende-Vim/tree/ff7b31d66822f1cf7f945c101048dfa1b862d42a/ch6_insert_mode.md) chapter.

## Repetir en múltiples líneas

Supongamos que tenemos el siguiente texto:

```text
let one = "1";
let two = "2";
let three = "3";
const foo = "bar';
let four = "4";
let five = "5";
let six = "6";
let seven = "7";
let eight = "8";
let nine = "9";
```

Tu objetivo es eliminar todas las líneas excepto la línea que contiene la cadenad "foo". Primero, borraremos las tres primeras líneas con `d2j`. Después iremos a la línea siguiente a la que contiene foo. En la línea siguiente, utiliza el comando del punto un par de veces. El comando completo es `d2jj..`.

Aquí el cambio fue `d2j`. `2j` no fue un movimiento, si no parte del operador de borrado.

Echemos un vistazo a otro ejemplo:

```text
zlet zzone = "1";
zlet zztwo = "2";
zlet zzthree = "3";
let four = "4";
```

Vamos a eliminar todas las letras "z". Primero, seleccionaremos visualmente solamente las letras "z" del comienzo de las tres primeras líneas con el modo de selección visual en bloque \(`Ctrl-vjj`\). Si no estás familiarizado con el modo de selección en bloque, lo trataremos en un próximo capítulo. Una vez que hayas seleccionado las tres letras "z" en modo selección de bloque visual, las borraremos con el operador de borrado \(`d`\). Después nos moveremos hasta la siguiente palabra \(`w`\) hasta la siguiente "z". Repetiremos el cambio dos veces más \(`..`\). El comando completo es `Ctrl-vjjdw..`.

Cuando has borrado la columna de las tres letras "z" \(`Ctrl-vjjd`\), esto fue tomado en cuenta como un cambio. La selección del modo visual puede ser utilizada para seleccionar múltiples líneas como parte de un cambio.

## Incluyedo un movimiento en u cambio

Vamos a volver a echar un vistazo al primer ejemplo de este capítulo. Volviendo a ejecutar el comando `/letcwconst<esc>` seguido por `n . n .` reemplazando todas las cadenas de "let" por "const" en la siguiente ejemplo:

```text
let one = "1";
let two = "2";
let three = "3";
```

Hay una manera más rápida de realizar esta tarea. Cuando borres el texto, en vez de utilizar `w` como sustantivo, utiliza: `gn`.

`gn` es un comando de movimiento que busca hacia adelante el último patrón de búsqueda \(en este caso la cadena de texto `/let`\) y automáticamente hace una selección en modo visual en la coincidencia encontrada. Para reemplazar la siguiente ocurrencia, ya no es necesario que te muevas y repetir el cambio \( `n . n .`\), but only repeat \(`. .`\). Ya no necesitarás moverte más, ya que la búsqueda de la siguiente coincidencia ¡ahora forma parte del cambio a realizar!

Cuando estes editando, siempre debes estar pendiente de buscar un comando de movimiento que pueda realizar diversas cosas en una sola vez como `gn` siempre que sea posible.

## Aprender el comando del punto de la manera más inteligente

El poder del comando del punto viene dado por poder ahorrarte muchas pulsaciones de teclado simplemente en una, pulsando la tecla del punto. Quizás no es un cambio muy rentable el utilizar comando del punto como reemplazo para operaciones que solo requieren una pulsación de tecla como `x`. Si tu último cambio requiere una operación más compleja, como por ejemplo `cgnconst<esc>`, el comando del punto reduce 9 pulsaciones de teclas en una sola, lo que es un buen cambio.

Cuando estes editando, pregúntate si la acción que estás a punto de realizar es propensa de volver a repetirla. Por ejemplo, si necesito eliminar las próximas tres palabras, ¿es más económico utilizar `d3w` o hacer `dw` y después repetir esa acción con `.` dos veces más? ¿Borrarás una palabra de nuevo? Si es así, entonces tiene sentido el utilizar `dw` y repetir la acción varias veces en vez de `d3w` porque `dw` se puede volver a utilizar más veces que `d3w`. Manten una mentalidad "orientada al cambio" mientras estés editando.

El comando del punto es muy sencillo y versátil para empezar a automatizar tareas simples. En el próximo capítulo aprenderás cómo automatizar acciones complejas con las macros de Vim. Pero primero, aprendamos sobre los registros para almacenar y recuperar textos.

