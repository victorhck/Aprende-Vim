# Macros

Cuando estás editando archivos, puede que te encuentres repitinedo las mismas acciones. ¿No sería genial si pudieras realizar esas acciones solo una vez y después poder reproducirlas cada vez que lo necesitaras? Gracias a las macros de Vim, puedes grabar tus acciones y almacenarlas dentro de los registros de Vim.

En este capítulo, aprenderás a utilizar macros para automatizar las tareas mundanas (además es genial ver como editar el propio archivo).

# Macros básicas

Aquí puedes ver la sintaxis básica de una macro de Vim:

```
qa                     Comienza a grabar una macro en el registro a
q (mientras está grabando)    Detiene la grabación de la macro
```

Puedes utilizar cualquier letra minúscula (a-z) para almacenar las macros. Una vez guardada una macro en uno de los registros disponibles, puedes ejecutarla cada vez que quieras mediante:

```
@a    ejecuta una macro almacenada en el registro a
@@    Ejecuta las últimas macros ejecutadas
```

Supongamos que tienes el siguiente texto y quieres poner en mayúsculas todo el texto de todas las líneas:

```
hello
vim
macros
are
awesome
```

Con el cursor sutuado al inicio de a línea con el texto "hello", ejecuta:
```
qa0gU$jq
```

Vamos a diseccionar el comando anterior para ver qué realiza:

- `qa` comienza la grabación de la macro en el registro "a".
- `0` situal el cursor al inicio de la línea.
- `gU$` convierte en mayúsculas el texto desde la posición actual hasta el final de la línea.
- `j` baja el cursor una línea.
- `q` detiene la grabación de la macro.

Para volver a ejecutar la macro cada vez que lo necesites, ejecuta `@a`. Como en la mayoría de comandos de Vim, puedes pasarle un número como argumento a las macros. Por ejemplo, puedes ejecutar `3@a` para ejecutar la macro "a" tres veces. Puedes ejecutar `3@@` para ejecutar la última macro ejecutada tres veces.

# Guarda de seguridad

La ejecución de la macro termina automáticamente cuando encuentra un error. Supongamos que tienes el siguiente texto:

```
a. chocolate donut
b. mochi donut
c. powdered sugar donut
d. plain donut
```

Si quieres convertir en mayúsculas la primera letra de la primera palabra de cada línea, esta macro debería funcionar:

```
qa0W~jq
```

Veamos paso por paso lo que realiza el comando anterior:
- `qa` comienza la grabación de la macro en el registro "a".
- `0` posiciona el cursor al inicio de la línea.
- `W` se posiciona en la siguiente PALABRA.
- `~` cambia a mayúsculas la primera letra bajo el cursor.
- `j` baja una línea.
- `q` detiene la grabación de la macro.


Me gusta sobre dimensionar las veces que ejecuto la llamada a una macro, así que normalmente suelo llamar noventa y nueve veces a la macro (`99@a`). Con este comando, no implica que de manera obligatoria Vim ejecute el comando 99 veces. Cuando Vim llega a la última línea y ejecuta la acción `j`, encuentra que ya no hay más líneas que bajar, se encuentra con un error, y detiene la ejecución de la macro.

El hecho que la ejecución de una macro se detenga cuando encuentra el primer error es una buena funcionalidad, de otra forma Vim continuaría ejecutando este macro 99 veces incluso aunque haya llegado al final del archivo.

# Macros en la línea de comandos de Vim

Ejecuta `@a` en el modo normal no es la única forma de ejecutar macros en Vim. También puedes ejecutar `:normal @a` en la línea de comandos. `:normal` permite al usuario ejecutar cualquier comando del modo normal dado como argumento. Pasándole el argumento `@a`, sería lo mismo que ejecutar `@a` en el modo normal.

El comando `:normal` acepta un rango como argumentos. Puedes ejecutar una macro en los rangos seleccionados. Si quieres ejecutar la macro "a" entre la lína 2 y 3, puedes ejecutar `:2,3 normal @a`. Vermos más en detalle los comandos de la línea de comandos en un capítulo posterior.

# Ejecutando una macro a través de múltiples archivos

Supongamos que tenemos múltiples archivos de extensión `.txt`, cada uno de los cuales contiene diferentes listas. Además, necesitas poner en mayúsculas la primera letra de la primera palabra solo de las líneas que contienen al palabra "donut". ¿Cómo podemos ejecutar macros a través de múltiple archivos en las líneas seleccionadas? 

Primer archivo:
```
# savory.txt
a. cheddar jalapeno donut
b. mac n cheese donut
c. fried dumpling
```

Segundo archivo:
```
# sweet.txt
a. chocolate donut
b. chocolate pancake
c. powdered sugar donut
```

Tercer archivo:
```
# plain.txt
a. wheat bread
b. plain donut
```
Aquí everemos cómo lo podemos hacer:
- `:args *.txt` para encontrar todos los archivos `.txt` en tu directorio actual.
- `:argdo g/donut/normal @a` ejecuta el comando global `g/donut/normal @a` en cada archivo dentro de `:args`.
- `:argdo update` ejecuta el comando `update` para guardar cada archivo dentro de `:args` cuando el *buffer* ha sido modificado.

Si no estás familiarizado con el comando global `:g/donut/normal @a`, este ejecuta el comando dado (`normal @a`) en las líneas que coinciden con el patrón (`/donut/`). Veremos más en detalle los comandos globales en un capítulo posterior.

# Macro recursiva

Puedes ejecutar de manera recursiva una macro llamándo al mismo registro de la macro mientras estás grabando la macro. Supongamos que tienes esta lista de nuevo y necesitas cambiar a mayúsculas la primera letra de la primera palabra:

```
a. chocolate donut
b. mochi donut
c. powdered sugar donut
d. plain donut

```
Para hacerlo de manera recursiva, ejecuta:
```
qaqqa0W~j@aq
```

Este es el desglose del comando en los diferentes pasos:
- `qaq` graba una macro vacía dentro de "a". Es necesario grabar una macro vacía en el mismo nombre de registro porque cuando ejecutes más tarde la macro "a", no quieres que ese registro contenga nada más.
- `qa` comienza la grabación en el registro "a".
- `0` situa el cursor en el primer caracter en la línea actual.
- `W` va a la siguiente PALABRA.
- `~` cambia la letra del caracter bajo el cursor.
- `j` baja una línea.
- `@a` ejecuta la macro "a". Cuando grabas esto, `@a` debería estar vacía porque la has llamado con `qaq`.
- `q` detiene la grabación.

Ahora simplemente ejecuta `@a` y mira cómo Vim ejecuta la macro de manera recursiva.

¿Cómo sabe la macro cuando debe parar? Cuando la macro está en la última línea, tartará de ejecutar `j`, encontrará que no hay una línea extra a la que ir y parará la ejecución de la macro.

# Añadiendo acciones a una macro

Si necesitas añadir más acciones a una macro existente, en vez de rehacerla, puedes añadir acciones a la macro ya existente. En el capítulo de los registros, aprendiste que puedes añadir un registro nominal utilizando su símbolo en mayúsculas. Para añadir acciones a una macro en el registro "a", utiliza el registro "A". Supongamos que además de cambiar a mayúsculas la primera palabra, también quieres añadir un punto al final de la línea.

Asumamos que tienes las siguiente acciones almacenadas en una macro en el registro "a":

```
0W~
```
Así es como podrías añadir la nueva acción:
```
qAA.<esc>q
```
El desglose:
- `qA` comienza la grabación de la macro en el registro "A".
- `A.<esc>` inserta un punto (".") al final de la línea (`A`), después sale del modo insertar (`<esc>`).
- `q` detiene la grabación de la macro.

Ahora cuando ejecutemos `@a`, irá al primer caracter de la línea (`0`), se dirige a la siguiente PALABRA (`W`), cambia el caracter de la palabra bajo el cursor (`~`), activa el modo insertar al final de la línea (`A`), escribirá un punto (".") y saldrá del modo insertar (`<esc>`).

# Amending a Macro

Appending is great technique to add new actions at the end of your existing macros, but what if you need to add new actions in the middle of it? In this section, I will show you how to amend a macro.

Suppose between uppercasing the first word and adding a period at the end of the line, you need to add the word "deep fried" right before the word "donut" *(because the only thing better than regular donuts are deep fried donuts)*.

I will reuse the text from earlier section:
```
a. chocolate donut
b. mochi donut
c. powdered sugar donut
d. plain donut
```

First, let's call the existing macro (assume you have kept the macro from the previous section in register "a") with `:put a`:

```
0W~A.^[
```

What is this `^[`? Didn't you do `0W~A.<esc>`? `^[` is Vim's *internal code* representation of `<esc>`. With certain special keys, Vim prints the representation of those keys in the form of internal codes. Some common keys that have internal code representations are `<esc>`, `<backspace>`, and `<enter>`. There are more special keys, but they are not within the scope of this chapter.

Back to the macro, right after the toggle case operator (`~`), let's add the instructions to go to the end of the line (`$`), go back one word (`b`), go to the insert mode (`i`), type "deep fried " (don't forget the space after "fried "), and exit insert mode (`<esc>`).

Here is what you will end up with:

```
0W~$bideep fried <esc>A.^[
```

There is a small problem. Vim does not understand `<esc>`. You will have to write the internal code representation for the `<esc>` you just added. While in insert mode, you press `Ctrl-v` followed by `<esc>`. Vim will print `^[`.` Ctrl-v` is an insert mode operator to insert the next non-digit character *literally*. Your macro code should look like this now:

```
0W~$bideep fried ^[A.^[
```

To add the amended instruction into register "a", you can do it the same way as adding a new entry into a named register. At the start of the line, run `"ay$`. This tells Vim that you're using the named register "a" (`"a`) to store the yanked text from the current position to the end of the line (`y$`).

Now when you execute `@a`, your macro will toggle the case of the first word, add "deep fried " before "donut", and add a "." at the end of the line.

An alternative way to amend a macro is to use a command line expression. Do `:let @a="`, then do `Ctrl-r Ctrl-r a`, this will literally paste the content of register "a". Finally, don't forget to close the double quotes (`"`). If you need to insert special characters using internal codes while editing a command line expression, you can use `Ctrl-v`.

# Macro Redundancy

You can easily duplicate macros from one register to another. For example, to duplicate a macro in register "a" to register "z", you can do `:let @z = @a`. `@a` represents the content of register "a". Now if you run `@z`, it does the exact same actions as `@a`.

I find creating a redundancy useful on my most frequently used macros. In my workflow, I usually record macros in the first seven alphabetical letters (a-g) and I often replace them without much thought. If I move the useful macros towards the end of the alphabets, I can preserve them without worrying that I might accidentally replace them.

# Series vs Parallel Macro

Vim can execute macros in series and parallel. Suppose you have this text:

```
import { FUNC1 } from "library1";
import { FUNC2 } from "library2";
import { FUNC3 } from "library3";
import { FUNC4 } from "library4";
import { FUNC5 } from "library5";
```

If you want to record a macro to lowercase all the uppercased "FUNC", this macro should work:

```
qa0f{gui{jq
```

Here is the breakdown:
- `qa` starts recording in register "a".
- `0` goes to first line.
- `f{` finds the first instance of "{".
- `gui{` lowercases (`gu`) the text inside the bracket text-object (`i{`).
- `j` goes down one line.
- `q` stops macro recording.

Now you can run `99@a` to execute it on the remaining lines. However, what if you have this import expression inside your file?

```
import { FUNC1 } from "library1";
import { FUNC2 } from "library2";
import { FUNC3 } from "library3";
import foo from "bar";
import { FUNC4 } from "library4";
import { FUNC5 } from "library5";
```

Running `99@a`, only executes the macro three times. It does not execute the macro on last two lines because the execution fails to run `f{` on the "foo" line. This is expected when running the macro in series. You can always go to the next line where "FUNC4" is and replay that macro again. But what if you want to get everything done in one go? You can run the macro in parallel.

Recall from earlier section that macros can be executed using the  command line command `:normal` (ex: `:3,5 normal @a` to execute macro "a" on lines 3-5). If you run `:1,$ normal @a`, you will see that the macro is being executed on all lines except the "foo" line. It works!

Although internally Vim does not actually run the macros in parallel, outwardly, it behaves like such. Vim executes `@a` *independently* on each line from the first line to the last line (`1,$`). Since Vim executes these macros independently, each line does not know that one of the macro executions had failed on the "foo" line.

# Learn Macros the Smart Way

Many things you do in editing are repetitive. To get better at editing, get into the habit of detecting repetitive actions. Use macros (or dot command) so you don't have to perform the same action twice. Almost everything that you can do in Vim can be done with macros.

In the beginning, I find it very awkward to write macros, but don't give up. With enough practice, you will get into the habit of automating everything.

You might find it helpful to use mnemonics to help remember your macros. If you have a macro that creates a function, use the "f" register (`qf`). If you have a macro for numerical operations, the "n" register may be a good fit (`qn`). Name it with the *first named register* that comes to your mind when you think of that operation. I also find that "q" register makes a good default macro register because `qq` does not require much brain power to use. Lastly, I like to increment my macros in alphabetical orders, like `qa`, then `qb`, then `qc`, and so on. Find a method that works best for you.
