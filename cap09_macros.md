# Capítulo 9: Macros

Cuando estás editando archivos, puede que te encuentres repitiendo las mismas acciones. ¿No sería genial si pudieras realizar esas acciones solo una vez y después poder reproducirlas cada vez que lo necesitaras? Gracias a las macros de Vim, puedes grabar tus acciones y almacenarlas dentro de los registros de Vim.

En este capítulo, aprenderás a utilizar macros para automatizar las tareas más triviales \(además es genial ver cómo el propio archivo se edita a si mismo\).

## Macros básicas

Aquí puedes ver la sintaxis básica de una macro de Vim:

```text
qa                            Comienza a grabar una macro en el registro a
q (mientras está grabando)    Detiene la grabación de la macro
```

Puedes utilizar cualquier letra minúscula \(a-z\) para almacenar las macros. Una vez guardada una macro en uno de los registros disponibles, puedes ejecutarla cada vez que quieras mediante:

```text
@a    Ejecuta una macro almacenada en el registro a
@@    Ejecuta la última macro ejecutada
```

Supongamos que tienes el siguiente texto y quieres poner en mayúsculas todo el texto de todas las líneas:

```text
hola
las
macros
de
vim
son
geniales
```

Con el cursor situado al comienzo de la línea con el texto "hola", ejecuta:

```text
qa0gU$jq
```

Vamos a diseccionar el comando anterior para ver qué realiza:

* `qa` comienza la grabación de la macro en el registro a.
* `0` sitúa el cursor al inicio de la línea.
* `gU$` convierte en mayúsculas el texto desde la posición actual hasta el final de la línea.
* `j` baja el cursor una línea.
* `q` detiene la grabación de la macro.

Para volver a ejecutar la macro cada vez que lo necesites, ejecuta `@a`. Como en la mayoría de comandos de Vim, puedes pasarle como argumento un número a las macros. Por ejemplo, puedes ejecutar `3@a` para ejecutar la macro a tres veces. Puedes ejecutar `3@@` para ejecutar la última macro ejecutada tres veces.

## Guarda de seguridad

La ejecución de la macro termina automáticamente cuando encuentra un error. Supongamos que tienes el siguiente texto:

```text
a. chocolate donut
b. mochi donut
c. powdered sugar donut
d. plain donut
```

Si quieres convertir en mayúsculas la primera letra de la primera palabra de cada línea, esta macro debería funcionar:

```text
qa0W~jq
```

Veamos paso por paso lo que realiza el comando anterior:

* `qa` comienza la grabación de la macro en el registro a.
* `0` posiciona el cursor al inicio de la línea.
* `W` se posiciona en la siguiente PALABRA.
* `~` cambia a mayúsculas la primera letra bajo el cursor.
* `j` baja una línea.
* `q` detiene la grabación de la macro.

Me gusta sobre dimensionar las veces que ejecuto la llamada a una macro, así que normalmente suelo llamar noventa y nueve veces a la macro \(`99@a`\). Con este comando, no implica que de manera obligatoria Vim ejecute el comando 99 veces. Cuando Vim llega a la última línea y ejecuta la acción `j`, encuentra que ya no hay más líneas que bajar, se encuentra con un error, y detiene la ejecución de la macro.

El hecho que la ejecución de una macro se detenga cuando encuentra el primer error es una buena funcionalidad, de otra forma Vim continuaría ejecutando este macro 99 veces incluso aunque haya llegado al final del archivo.

## Macros en la línea de comandos de Vim

Ejecutar `@a` en el modo normal no es la única forma de ejecutar macros en Vim. También puedes ejecutar `:normal @a` en la línea de comandos. `:normal` (o `:norm` de manera abreviada) permite al usuario ejecutar cualquier comando del modo normal dado como argumento. Pasándole el argumento `@a`, sería lo mismo que ejecutar `@a` en el modo normal.

El comando `:normal` acepta un rango como argumentos. Puedes ejecutar una macro en los rangos seleccionados. Si quieres ejecutar la macro a entre la línea 2 y 3, puedes ejecutar `:2,3 normal @a`.

## Ejecutando una macro a través de múltiples archivos

Supongamos que tenemos múltiples archivos de extensión `.txt`, cada uno de los cuales contiene diferentes listas. Además, necesitas poner en mayúsculas la primera letra de la primera palabra solo de las líneas que contienen al palabra "donut". ¿Cómo podemos ejecutar macros a través de múltiple archivos en las líneas seleccionadas?

Primer archivo:

```text
## savory.txt
a. cheddar jalapeno donut
b. mac n cheese donut
c. fried dumpling
```

Segundo archivo:

```text
## sweet.txt
a. chocolate donut
b. chocolate pancake
c. powdered sugar donut
```

Tercer archivo:

```text
## plain.txt
a. wheat bread
b. plain donut
```

Aquí veremos cómo lo podemos hacer:

* `:args *.txt` para encontrar todos los archivos `.txt` en tu directorio actual.
* `:argdo g/donut/normal @a` ejecuta el comando global `g/donut/normal @a` en cada archivo dentro de `:args`.
* `:argdo update` ejecuta el comando `update` para guardar cada archivo dentro de `:args` cuando el _buffer_ ha sido modificado.

Si no estás familiarizado con el comando global `:g/donut/normal @a`, este ejecuta el comando dado \(`normal @a`\) en las líneas que coinciden con el patrón \(`/donut/`\). Veremos más en detalle los comandos globales en un capítulo posterior.

## Macro recursiva

Puedes ejecutar de manera recursiva una macro llamando al mismo registro de la macro mientras estás grabando la macro. Supongamos que tienes esta lista de nuevo y necesitas cambiar a mayúsculas la primera letra de la primera palabra:

```text
a. chocolate donut
b. mochi donut
c. powdered sugar donut
d. plain donut
```

Para hacerlo de manera recursiva, ejecuta:

```text
qaqqa0W~j@aq
```

Este es el desglose del comando en los diferentes pasos:

* `qaq` graba una macro vacía dentro de a. Es necesario grabar una macro vacía en el mismo nombre de registro porque cuando ejecutes de manera recursiva, ejecutará todo lo que haya en el registro.
* `qa` comienza la grabación en el registro a.
* `0` sitúa el cursor en el primer carácter en la línea actual.
* `W` va a la siguiente PALABRA.
* `~` cambia la letra del carácter bajo el cursor.
* `j` baja una línea.
* `@a` ejecuta la macro a.
* `q` detiene la grabación.

Ahora simplemente ejecuta `@a` y mira cómo Vim ejecuta la macro de manera recursiva.

¿Cómo sabe la macro cuando debe parar? Cuando la macro está en la última línea, tratará de ejecutar `j`, encontrará que no hay una línea extra a la que ir y parará la ejecución de la macro.

## Añadiendo acciones a una macro

Si necesitas añadir más acciones a una macro existente, en vez de rehacerla, puedes añadir acciones a la macro ya existente. En el capítulo de los registros, aprendiste que puedes añadir un registro nominal utilizando su símbolo en mayúsculas. Para añadir acciones a una macro en el registro a, utiliza el registro A.

Graba una macro en el registro a: `qa0W~q` (esta secuencia alterna las mayúsculas y minúsculas de la próxima PALABRA en una línea). Si quieres añadir una nueva secuencia para también añadir un punto al final de la línea, ejecuta:

```text
qAA.<Esc>q
```

El desglose:

* `qA` comienza la grabación de la macro en el registro A.
* `A.<Esc>` inserta al final de la línea \(aquí `A` se refiera al comando para añadir texto al final de una línea, no confundir con la macro A\) un punto, después sale del modo insertar \(`<Esc>`\).
* `q` detiene la grabación de la macro.

Ahora cuando ejecutes `@a`, no solo cambiará las mayúsculas de la siguiente PALABRA, y también añadirá un punto al final de la línea.

## Modificar una macro

¿Qué pasa si quieres añadir nuevas acciones en la parte media de una macro?

Supongamos que entre la acción de convertir en mayúscula la primera letra de la primera palabra y añadir un punto al final de la línea, necesitas añadir las palabras "deep fried" justo antes de la palabra "donut" _\(porque la única cosa mejor que un donut normal es un donut frito\)_.

Reutilizaremos el texto de la sección anterior:

```text
a. chocolate donut
b. mochi donut
c. powdered sugar donut
d. plain donut
```

Primero, llamemos a la macro ya existente \(asumamos que has guardado la macro de la sección anterior en el registro a\) con `:put a`:

```text
0W~A.^[
```

¿Qué es el símbolo `^[`? ¿No escribiste `0W~A.<Esc>`? `^[` es la representación con el _código interno_ de Vim para representar `<Esc>`. Con ciertas teclas especiales, Vim muestra la representación de esas teclas en forma de códigos internos. Algunas teclas comunes que tienen representaciones de código interno son `<Esc>`, `<Backspace>`, y `<Enter>`. Hay más teclas especiales, pero eso no entra dentro del objetivo de este capítulo.

Volviendo a la modificación de la macro, justo después del operador para cambiar el estado de un carácter de minúscula a mayúscula \(`~`\), vamos a añadir las instrucciones para ir al final de la línea \(`$`\), regresar una palabra \(`b`\), entrar en el modo de insertar \(`i`\), escribir "deep fried " \(no olvides el espacio en blanco después de la palabra "fried "\), y salir del modo insertar \(`<Esc>`\).

Esto es el aspecto que tendrá la macro finalizada:

```text
0W~$bideep fried <Esc>A.^[
```

Pero hay un pequeño problema. Vim no entiende `<Esc>`. Deberás escribir la representación del comando en su código interno, tal como lo usa Vim, para el `<Esc>` que acabas de añadir. Mientras estás en el modo insertar, presiona `Ctrl-V` seguido de `<Esc>`. Vim mostrará `^[`.`Ctrl-V` es un operador del modo insertar para insertar literalmente el siguiente carácter que no sea un dígito. El código de la macro debería ser así:

```text
0W~$bideep fried ^[A.^[
```

Para añadir la instrucción modificada al registro a, puedes hacerlo de la misma manera que al añadir una nueva entrada a un registro nominal. Al comienzo de la línea ejecuta `"ay$` para almacenar el texto copiado en el registro a.

Ahora cuando ejecutes `@a`, tu macro cambiará a mayúsculas la primera letra de la primera palabra, añadirá "deep fried " antes de "donut" y añadirá "." al final de la línea.

Una manera alternativa de modificar una macro es utilizando una expresión para la línea de comandos. Ejecuta `:let @a="`, y después `Ctrl-R Ctrl-R a`, esto literalmente pegará el contenido del registro a. Finalmente, no olvides encerrarlo todo entre comillas dobles \(`"`\). Deberías tener algo como `:let @a="0W~$bideep fried ^[A.^["`.

## Redundancia de macros

Puedes duplicar fácilmente macros de un registro a otro. Por ejemplo, para duplicar una macro ya existente en el registro a al registro z, simplemente ejecuta lo siguiente `:let @z = @a`. `@a` representa el contenido del registro a. Ahora si ejecutas `@z`, realizará las mismas acciones que `@a`.

Creo que es útil crear macros redundantes de las macros que utilizo con más frecuencia. En mi forma de trabajar, normalmente guardo las macros en las primeras siete letras del alfabeto \(a-g\) y las reemplazo a menudo. Si muevo las macros más útiles a las letras finales del alfabeto, puedo preservarlas sin tener que preocuparme de reemplazarlas de manera accidental.

## Macros en serie vs. en paralelo

Vim puede ejecutar macros en serie o en paralelo. Supongamos que tenemos el siguiente texto:

```text
import { FUNC1 } from "library1";
import { FUNC2 } from "library2";
import { FUNC3 } from "library3";
import { FUNC4 } from "library4";
import { FUNC5 } from "library5";
```

Si quieres grabar una macro para poner en minúsculas todas las mayúsculas de "FUNC", esta macro debería funcionar:

```text
qa0f{gui{jq
```

Desglosemos el funcionamiento de la macro:

* `qa` comienza la grabación en el registro a.
* `0` posiciona el cursor en la primera línea.
* `f{` encuentra la primera ocurrencia del símbolo "{".
* `gui{` pone en minúsculas \(`gu`\) el texto dentro de los corchetes \(`i{`\).
* `j` baja una línea.
* `q` detiene la grabación de la macro.

Ahora puedes ejecutar `99@a` para ejecutarla en las líneas faltantes. Sin embargo, ¿qué pasa si tienes que esta expresión de importación dentro de tu archivo?

```text
import { FUNC1 } from "library1";
import { FUNC2 } from "library2";
import { FUNC3 } from "library3";
import foo from "bar";
import { FUNC4 } from "library4";
import { FUNC5 } from "library5";
```

Al ejecutar `99@a`, solo se ejecutará la macro tres veces. No ejecutará la macro en las dos últimas líneas porque la ejecución falla al ejecutar `f{` en la línea que contiene "foo". Esto es lo que se espera que pase al ejecutar las macros en serie. Siempre puedes ir a la siguiente línea donde está el texto "FUNC4" y volver a ejecutar la macro. Pero ¿qué pasa si quieres que todo se ejecute de una vez?

Ejecuta la macro en paralelo.

Volviendo a la sección anterior en la que vimos que las macros pueden ser ejecutadas utilizando el comando para la línea de comandos `:normal` \(ejemplo: `:3,5 normal @a` para ejecutar la macro a en las líneas 3-5\). Si ejecutas `:1,$ normal @a`, verás que la macro está siendo ejecutada en todas las líneas excepto en la línea que contiene el texto "foo". ¡Funciona!

Aunque internamente Vim no ejecuta las macros en paralelo, externamente, parece que sí lo hace así. Vim ejecuta `@a` _independientemente_ en cada línea desde la primera a la última \(`1,$`\). Ya que Vim ejecuta estas macros de manera independiente, cada línea no sabe que una de las macros en ejecución ha fallado en la línea que contiene el texto "foo".

## Aprendiendo las macros de la manera más inteligente

Muchas cosas que haces mientras estás editando son repetitivas. Para mejorar en la edición de texto, debes habituarte a detectar las acciones repetitivas. Utiliza macros \(o el comando del punto\) así no tendrás que realizar la misma acción dos veces. Casi todo lo que haces en Vim puede ser realizado con macros.

Al comienzo, encontraba muy raro escribir macros, pero no desistí. Con la práctica suficiente, tendrás el hábito de automatizar todo.

Puede que encuentre útil utilizar mnemónicos para ayudarte a recordar tus macros. Si tienes una macro que crea una función, utiliza el registro "f" \(`qf`\). Si tienes una macro para operaciones numéricas, entonces el registro "n" puede ser una buena elección \(`qn`\). Nómbrala con _el primer registro nominal_ que te venga a la cabeza cuando pienses en esa operación. También es útil utilizar el registro "q" como macro predeterminada ya que `qq` no requiere pensar mucho a la hora de ponerle nombre. Por último, me gusta incrementar mis macros en orden alfabético, como `qa`, después `qb`, después `qc`, y así sucesivamente.

Encuentra el método que mejor se adapte a ti.

