# Capítulo 11: Modo visual

Resaltar texto y aplicar cambios a esa porción de texto es una funcionalidad muy común en muchos editores y procesadores de texto. Vim puede hacer esto utilizando el modo visual. En este capítulo, aprenderás cómo utilizar el modo visual para manipular textos de manera eficiente.

## Los tres tipos de modos visuales

Los tres modos son:

```text
v         Modo visual de selección de carácter
V         Modo visual de selección de líneas
Ctrl-v    Modo visual de selección de bloque
```

Si tienes el siguiente texto:

```text
uno
dos
tres
```

El modo visual de selección de carácter es utilizado para seleccionar caracteres de forma individual. Pulsa `v` en el primer carácter de la primera línea. Después baja hasta la línea siguiente con `j`. Esto resaltará todo el texto desde "uno" hasta donde este ubicado el cursor. Ahora pulsa `gU`, Vim convertirá a mayúsculas todos los caracteres resaltados.

El modo visual de selección de línea funciona con líneas como unidades de selección. Pulsa `V` y observa como Vim selecciona la línea completa en la que esté ubicado el cursor. Igual que en el modo visual de selección de carácter, si ejecutas `gU`, Vim cambiará a mayúsculas las líneas resaltadas.

El modo visual de selección de bloque funciona con filas y columnas. Te da más libertad de movimientos que los dos modos anteriores. Pulsando `Ctrl-V` verás que Vim resalta el carácter bajo el cursor de igual manera que el modo visual de selección de carácter, excepto que ahora en vez de resaltar cada carácter hasta el final de la línea antes de seguir en la línea siguiente, puedes dirigir el cursor hasta la línea siguiente sin que se marquen todos los caracteres de la línea actual. Intenta moverte mediante las teclas `h/j/k/l` y observa los movimientos del cursor y las zonas que se marcan.

En la esquina inferior izquierda de la ventana de Vim, verás que se muestra `-- VISUAL --`, `-- VISUAL LINE --` o `-- VISUAL BLOCK --` para indicar el modo visual en que te encuentras.

Mientras estás dentro de un modo visual, puedes cambiar a otro de los modos pulsando `v`, `V` o `Ctrl-V`. Por ejemplo, si estás en el modo visual de selección de línea y quieres cambiar al modo visual de selección de bloque, ejecuta `Ctrl-V`. ¡Inténtalo!

Hay tres maneras de salir del modo visual: `<Esc>`, `Ctrl-C` y con la misma tecla con la que accediste al modo visual actual.

Esto último significa, que si estás en el modo visual de selección de línea \(`V`\), podrás salir de ese modo pulsando `V` de nuevo. Si estás en el modo visual de selección de carácter, podrás salir pulsando `v`. Si estás en el modo visual de selección de bloque, pulsa `Ctrl-V` para salir de ese modo.

Hay una forma más de entrar en el modo visual:

```text
gv    Go to the previous visual mode
```

Este comando seleccionará el mismo modo visual y el mismo resaltado de texto de la última vez.

## Navegación en el modo visual

Mientras estás en el modo visual, puedes expandir el bloque de texto resaltado con los movimientos de Vim.

Vamos a utilizar el mismo texto que hemos utilizado anteriormente:

```text
uno
dos
tres
```

Esta vez vamos a empezar desde la línea "dos". Pulsa `v` para comenzar el modo visual de selección de carácter (aquí los corchetes `[]` representan el carácter resaltado):

```text
uno
[d]os
tres
```

Pulsa `j` y Vim resaltará todo el texto desde la línea "dos" hasta el primer carácter de la línea "tres".

```text
uno
[dos
t]res
```

Supongamos que nos hemos dado cuenta que también necesitamos resaltar la línea "uno", así que pulsas la tecla `k`. Para tu asombro, esto ahora excluye "tres". Al pulsar `k` esto reduce la zona resaltada, no la expande.

```text
uno
[d]os
tres
```

¿Hay alguna manera de expandir libremente la selección visual para ir a cualquier dirección que quieras?

La respuesta es sí. Regresemos a la situación anterior donde teníamos las líneas "dos" y "tres" resaltadas.

```text
uno
[dos
t]res    <-- cursor
```

El resaltado visual sigue el movimiento del cursor. Si quiere expandirlo hacia arriba a la línea "uno", necesitas mover el cursor hacia arriba de la línea "dos". Ahora tu cursor está en la línea "tres". Para moverlo, cambia la localización del cursor tanto con `o` u `O`.

```text
uno
[dos     <-- cursor
t]res
```

Ahora pulsa `k`, esto ya no reducirá la selección si no que la expandirá hacia arriba.

```text
[uno
dos
t]res
```

Al pulsar `o` u `O` en el modo visual, esto hará que nuestro cursor salte del inicio al final del bloque resaltado, sin eliminar las partes resaltadas y podremos añadir más texto a esa selección.

## Gramática en el modo visual

El modo visual comparte muchas de sus operaciones con el modo normal.

Por ejemplo, si tenemos el siguiente texto y quieres eliminar las primeras dos líneas del modo visual:

```text
uno
dos
tres
```

Resalta las líneas "uno" y "dos" con el modo visual de selección de línea \(`V`\):

```text
[uno
dos]
tres
```

Al pulsar `d` esto eliminará la selección, de manera similar al modo normal. Cabe destacar que la regla gramatical del modo normal de verbo + sustantivo, aquí no se aplica. El mismo verbo sigue estando ahí \(`d`\), pero no existe un sustantivo en el modo visual. La regla gramática en el modo visual es sustantivo + verbo, donde el sustantivo en este caso es reemplazado por el texto resaltado. Primero selecciona el texto, después opera sobre el.

En el modo normal, hay algunos comandos que no requieren de una tecla de movimiento, como por ejemplo `x` para eliminar un único carácter bajo el cursor o `r` para reemplazar el carácter bajo el cursor (`rx` reemplaza el texto bajo el cursor con la letra "x"). En el modo visual, estos comandos son ahora aplicados al texto resaltado por completo en vez de a un único carácter. Regresemos al texto que hemos resaltado anteriormente:

```text
[uno
dos]
tres
```

Al ejecutar el comando `x` esto eliminará todo el texto resaltado.

Puedes utilizar esta funcionalidad para crear rápidamente un encabezado en un texto en formato Markdown. Supongamos que necesitas cambiar rápidamente el siguiente texto a un encabezado de primer nivel de Markdown ("==="):

```text
Capítulo uno
```

Primero copiamos la línea con `yy`, después lo pegamos con `p`:

```text
Capítulo uno
Capítulo uno
```

Ahora coloquemos el cursor en la segunda línea y la seleccionamos con el modo visual de selección de línea:

```text
Capítulo uno
[Capítulo uno]
```

En Markdown puedes crear un encabezado añadiendo una serie de `=` debajo del texto, así que reemplacemos el texto completo resaltado mediante `r=`. ¡Voila! Esto te ahora el tener que escribir todos esos "=" de manera manual:

```text
Capítulo uno
============
```

Para aprender más sobre los operadores en el modo visual, echa un vistazo a la ayuda de Vim mediante: `:h visual-operators`.

## El modo visual y comandos Ex

Puedes aplicar de manera selectiva comandos Ex a un bloque de texto resaltado. Si tienes estas expresiones y quieres sustituir "const" con "let" solo en las dos primeras líneas:

```text
const one = "one";
const two = "two";
const three = "three";
```

Resalta las primeras dos línea con _cualquier_ modo visual y ejecuta el comando de sustituir `:s/const/let/g`:

```text
let one = "one";
let two = "two";
const three = "three";
```

Ten en cuenta que he dicho que puedes hacer esto con _cualquier_ modo visual. No es necesario resaltar las líneas completas para ejecutar el comando Ex en esas líneas. Con tan solo seleccionar al menos un carácter de cada línea, el comando Ex será aplicado.

## Añadir texto en múltiples líneas

Puedes editar texto en múltiples líneas en Vim utilizando el modo visual de selección de bloque. Si necesitas añadir un punto y coma al final de cada línea:

```text
const one = "one"
const two = "two"
const three = "three"
```

Con tu cursor en la primera línea:

* Ejecuta el modo visual de selección de bloque y baja dos líneas \(`Ctrl-V jj`\).
* Resalta hasta el final de la línea \(`$`\).
* Añade \(`A`\) y después escribe ";".
* Sal del modo visual \(`<Esc>`\).

Deberías ver que se ha añadido ";" en cada línea. ¡Genial! Hay dos maneras de entrar en el modo insertar desde el modo visual de selección de bloque: `A` para añadir el texto después del cursor o `I` para introducir el texto después del cursor. No confundirlos con `A` (añadir texto al final de la línea) o `I` (insertar texto después del primer carácter que no sea un espacio en blanco en la línea) del modo normal.

De manera alternativa, también puedes utilizar el comando `:normal`:

* Resalta las tras líneas \(`vjj`\).
* Escribe `:normal! A;`.

Recuerda, el comando `:normal` ejecuta comandos del modo normal. Le puedes pedir que ejecute `A;` para añadir el texto ";" al final de la línea.

## Incrementar números

Vim tiene los comandos `Ctrl-X` y `Ctrl-A` para decrementar e incrementar números. Cuando son usados en el modo visual, puedes incrementar o decrementar números en múltiples líneas.

Si tienes estos elementos HTML:

```text
<div id="app-1"></div>
<div id="app-1"></div>
<div id="app-2"></div>
<div id="app-1"></div>
<div id="app-1"></div>
```

Es una mala práctica el tener muchas _ids_ con el mismo número, así que vamos a incrementarlos para hacerlos únicos:

* Mueve tu cursor al número "1" de la segunda línea de las mostradas.
* Comienza el modo visual de selección de bloques y baja 3 líneas \(`Ctrl-V 3j`\). Esto resalta los números "1" siguientes.
* Ejecuta `g Ctrl-A`.

Deberías ver este resultado:

```text
<div id="app-1"></div>
<div id="app-2"></div>
<div id="app-3"></div>
<div id="app-4"></div>
<div id="app-5"></div>
```

`g Ctrl-A` incrementa los números en múltiples líneas. `Ctrl-X/Ctrl-A` también pueden incrementar letras. Si ejecutas:

```text
:set nrformats+=alpha
```

La opción `nrformats` le indica a Vim que bases son consideradas como "números" para `Ctrl-A` y `Ctrl-X` para incrementar y decrementar. Añadiendo `alpha`, un carácter alfanumérico es considerado ahora como un número. Si tienes los siguientes elementos HTML:

```text
<div id="app-a"></div>
<div id="app-a"></div>
<div id="app-a"></div>
<div id="app-a"></div>
<div id="app-a"></div>
```

Coloca tu cursor en la segunda "app-a". Utiliza la misma técnica que en el ejemplo anterior \(`Ctrl-V 3j` después `g Ctrl-A`\) para incrementar los "ids".

```text
<div id="app-a"></div>
<div id="app-b"></div>
<div id="app-c"></div>
<div id="app-d"></div>
<div id="app-e"></div>
```

## Seleccionar la última área del modo visual

Ya has aprendido que con con `gv` puedes resaltar rápidamente la anterior zona marcada en el modo visual. También puedes ir a la ubicación del comienzo y final de la anterior zona del modo visual con estas marcas especiales:

```text
`<    Ir al último lugar de la anterior zona resaltada del modo visual
`>    Ir al comienzo de la anterior zona resaltada del modo visual
```

Anteriormente, mencioné que puedes ejecutar de manera selectiva un comando Ex en un texto resaltado, como `:s/const/let/g`. Cuando ejecutaste ese comando, deberías haber visto esto:

```text
:`<,`>s/const/let/g
```

Realmente ejecutaste un comando en un rango `s/const/let/g` (con las dos marcas como direcciones de inicio y fin del rango). ¡Interesante!

Siempre podrás editar estas marcas en cualquier momento que quieras. Si en vez de sustituir desde el comienzo del texto resaltado al final del archivo, simplemente cambia el comando a este::

```text
:`<,$s/const/let/g
```

## Entrando en el modo visual desde el modo insertar

También puedes entrar en el modo visual desde el modo insertar. Para ir al modo visual de selección de carácter mientras en el modo insertar, ejecuta:

```text
Ctrl-O v
```

Volver a ejecutar `Ctrl-O` mientras estás en el modo insertar te permite ejecutar un comando en el modo normal. Mientras estás en este modo-normal-momentáneo, ejecuta `v` para entrar en el modo visual de selección de carácter. Fíjate que en la parte inferior izquierda de la pantalla se muestra `--(insert) VISUAL--`. Este truco funciona con cualquier operador de cualquier modo visual: `v`, `V`, y `Ctrl-V`.

## Modo seleccionar

Vim tiene un modo similar al modo visual llamado el _modo seleccionar_. De manera similar al modo visual, también tiene tres modos diferentes::

```text
gh         Modo seleccionar de selección de carácter
gH         Modo seleccionar de selección de línea
gCtrl-h    Modo seleccionar de selección de bloque
```

El modo seleccionar emula al comportamiento de un editor de texto normal al seleccionar un texto de una manera más cercana a como lo hace Vim en el modo visual.

En un editor normal, después de seleccionar un bloque de texto y escribir una letra, por ejemplo la letra "y", el editor borrará todo el texto resaltado e insertará la letra escrita, en nuestro ejemplo la letra "y". Si resaltas una línea de texto con el modo de selección de línea \(`gH`\) y escribes "y", se borrará el texto resaltado y se insertará la letra "y".

Este comportamiento contrasta con el modo visual: si resaltas una línea de de texto en el modo visual de selección de línea \(`V`\) y escribes "y", el texto resaltado no será eliminado y reemplazado con la letra "y", solo será copiado. No puedes ejecutar comandos del modo normal en texto resaltado en el modo seleccionar.

Personalmente nunca he utilizado el modo seleccionar, pero es bueno saber que existe.

## Aprende el modo visual de la manera más inteligente

El modo visual es la representación de Vim del procedimiento de resaltado de texto.

Si te encuentras a ti mismo utilizando el modo visual mucho más a menudo que operaciones en el modo normal, ten cuidado. Creo que esto es un anti patrón. Conlleva más pulsaciones de teclas el ejecutar operaciones en el modo visual que hacer la misma operación en el modo normal. Si necesitas eliminar una palabra en la que te encuentras, por qué utilizar cuatro pulsaciones de teclas, `viwd` \(resaltar visualmente una palabra y después eliminarla\), si puedes hacer eso mismo con solo tres pulsaciones de teclas \(`diw`\)? Esto último es más directo y conciso. Por supuesto, habrá ocasiones en las que el modo visual será apropiado, pero en general, es mejor un enfoque más directo.

