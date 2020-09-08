# El comando del punto

Cuando estás editando un texto, evita todo lo que puedas, volver a hacer lo que ya hiciste. En este capítulo, aprenderás a utilizar el comando del punto para volver a realizar los cambios previos de una manera sencilla. Es el comando más simple y más versátil para reducir las repeticiones.

# Uso

Tal como sugiere su nombre, puedes utilizar el comando del punto, simplemente presionando la tecla del signo del punto (`.`). 

Por ejemplo, si quieres reemplazar todo el texto "let" con "const" en el siguiente código:
```
let one = "1";
let two = "2";
let three = "3";
```

Primero, utiliza `/let` para colocarte encima de la primera coincidencia que encuentre Vim. Segundo, utiliza `cwconst<esc>` para reemplazar "let" con "const" . Tercero, utiliza `n` para encontrar la siguiente coincidencia en el texto, utilizando la búsqueda que hemos realizado previamente. Finalmente, repite la acción de sustitución que realizaste con el comando del punto (`.`). Continua haciendo `n . n .` hasta que reemplaces todas las coincidencias que quieras.

En este caso el comando del punto, repite la secuencia `cwconst<esc>`. Esto te ahorra el tener que realizar ocho pulsaciones de teclas en cada cambio teniendo que pulsar solo una tecla, el punto.

# ¿Qué es un cambio?

Si buscas en Vim la definición del comando del punto (`:h .`), verás que menciona que el comando repite el último cambio. ¿Qué es un cambio?

Cada vez que realizas una actualización del contenido del _buffer_ actual (añadir, modificar o eliminar) utilizando cualquier comando del *modo normal*, estás realizando un cambio. Las excepciones son actualizaciones realizadas mediante comandos ejecutados en la línea de comandos (los comando que comienzan con `:`), esos no cuentan como cambios.

En el primer ejemplo, comprobaste que `cwconst<esc>` fue el cambio. Ahora supongamos que tenemos esta frase:

```
tarta, patatas, zumo de frutas,
```

Ahora vamos a borrar, el texto desde el comienzo de la línea hasta donde encontremos la primera coma. Puedes realizar esto mediante `df,`. Repite la acción con `.` dos veces más hasta que borres todas las palabras.

Probemos otro ejemplo
```
tarta, patatas, zumo de frutas,
```

Esta vez solo necesitas borrar la coma, no la palabra que la precede. Con el cursor en el inicio de la frase, ve hasta la primera coma de la frase mediante el comando `f,`. Elimina el signo de puntuación bajo el cursor con el comando `x`. Repite mediante `.` la acción un par de veces más. ¿Sencillo, verdad? Espera, ¡no funcionó! ¿Por qué?

En Vim, los cambios no incluyen los movimientos, porque estos no actualizan el contenido del *buffer*. Cuando ejecutamos `f,x`, realizamos dos acciones diferentes: el comando `f,` mueve el cursor y `x` actualiza el *buffer*. Solo este último realiza un cambio. A diferencia que con `df,` del ejemplo anterior. En este, `f,` indica al operador de borrado donde eliminar el contenido. Es una parte de todo la operación de borrado, `df,`.

Finalicemos la última tarea. Después de ejecutar `f,` seguido por `x`, ve a la siguiente coma mediante `;` para repetir la última búsqueda realizada con `f`. Después utiliza `.` para eliminar el caracter bajo el cursor. Repite la secuencia `; . ; .` hasta que todo este eliminado. El comando completo sería `f,x;.;.`.

Probemos otro ejemplo:

```
tarta
patatas
zumo de frutas
```

Vamos a añadir una coma al final de cada línea. Empezando por la primera línea, vamos a ejecutar lo siguiente `A,<esc>j`. A estas alturas, ya te das cuenta que `j` no realiza ningún cambio. El cambio es solo realizado con `A,`. Te puedes mover y repetir el cambio con `j . j .`. El comando completo sería `A,<esc>j.j.`.

Cada acción desde el momento que presionas el operador del comando de insertar (`A`) hasta que lo abandonas con el comando (`<esc>`) es considerado un cambio. Vim te permite controlar no solo que texto añadir, también *donde* añadirlo. También puedes añadirlo antes del cursor (`i`), después del cursor (`a`), en una nueva línea debajo del cursor (`o`), en una línea sobre el cursor (`O`), al final de la línea actual (`A`) o al comienzo de la línea actual (`I`). Para refrescar estos conceptos, echa un vistazo al capítulo [Insert Mode](./ch6_insert_mode.md) chapter.


# Repetir en múltiples líneas

Supongamos que tenemos el siguiente texto:
```
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
```
zlet zzone = "1";
zlet zztwo = "2";
zlet zzthree = "3";
let four = "4";
```

Vamos a eliminar todas las letras "z". Primero, seleccionaremos visualmente solamente las letras "z" del comienzo de las tres primeras líneas con el modo de selección visual en bloque (`Ctrl-vjj`). Si no estás familiarizado con el modo de selección en bloque, lo trataremos en un próximo capítulo. Una vez que hayas seleccionado las tres letras "z" en modo selección de bloque visual, las borraremos con el operador de borrado (`d`). Después nos moveremos hasta la siguiente palabra (`w`) hasta la siguiente "z". Repetiremos el cambio dos veces más (`..`). El comando completo es `Ctrl-vjjdw..`.

Cuando has borrado la columna de las tres letras "z" (`Ctrl-vjjd`), esto fue tomado en cuenta como un cambio. La selección del modo visual puede ser utilizada para seleccionar múltiples líneas como parte de un cambio.

# Including a Motion in a Change

Let's revisit the first example in this chapter. Recall that the command `/letcwconst<esc>` followed by `n . n .`  replaced all "let" with "const" in the following expressions:
```
let one = "1";
let two = "2";
let three = "3";
```
There is a faster way to accomplish this. When deleting, instead of using the `w` as a noun, use `gn`.

`gn` is a motion that searches forward for the last search pattern (in this case, `/let`) and automatically does a visual mode selection on the match. To replace the next occurrence, you no longer have to move and repeat the change ( `n . n .`), but only repeat (`. .`). You do not have to move anymore because searching the next match is now part of the change!

When you are editing, always be on the lookout for a motion that can do several things at once like `gn` whenever possible.

# Learn the Dot Command the Smart Way

The dot command's power comes from exchanging several keystrokes for one. It is probably not a profitable exchange to use the dot command for one-keyed-operations like `x`. If your last change requires a complex operation like `cgnconst<esc>`, the dot command reduces nine keypresses into one, a very good trade-off.

When editing, ask if the action you are about to do is repeatable. For example, if I need to remove the next three words, is it more economical to use `d3w` or to do `dw` then `.` two times? Will you be deleting a word again? If so, then it makes sense to use `dw` and repeat it several times instead of `d3w` because `dw` is more reusable than `d3w`. Keep a "change-driven" mindset while editing.

The dot command is an easy and versatile command to start automating simple tasks. In the later chapter, you will learn how to automate more complex actions with Vim macros. But first, let's learn about registers to store and retrieve texts.
