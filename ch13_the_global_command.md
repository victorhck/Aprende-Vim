# Capítulo 13: El comando global

Hasta ahora has aprendido cómo repetir el último cambio con el comando del punto (`.`), repetir acciones con macros (`q`) y almacenar textos en los registros (`"`). 

En este capítulo, aprenderás cómo repetir un comando ejecutado en la línea de comandos de Vim con el comando global. Ejecútalo una vez, aplícalo en todas partes (dentro de un *buffer*).

## Un vistazo al comando global

El comando global de Vim es utilizado para ejecutar un comando de la línea de comandos en múltiples líneas de manera simultánea. 

Por cierto, puede que hayas leído anteriormente el témino "comandos Ex". En este libro, yo me refiero a ellos como comandos de la línea de comandos, pero ambos tanto los comandos Ex como los comandos de la línea de comandos son lo mismo. Hay comandos que comienzan con el símbolo de dos puntos (`:`). En el capítulo anterior aprendiste sobre los comandos de sustitución. Fue un ejemplo de los comandos Ex. Son llamados Ex porque originalmente provenían del editor de texto Ex. Me seguiré refiriendo a ellos en este libro como comandos de la línea de comandos. Para obtener una lista completa de los comandos Ex, echa un vistazo en Vim a `:h ex-cmd-index`.

El comando global tiene la siguiente sintaxis:

```
:g/patrón/comando
```

El `patrón` busca todas las líneas que contengan ese patrón, de manera similar al patrón en el comando de sustitución. El `comando` puede ser cualquier comando de la línea de comandos. El comando global funciona al ejecutar `comando` en cada línea que coincida con el `patrón`.

Si tienes las siguiente expresiones:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Para eliminar todas las líneas que contengan la palabra "console", puedes ejecutar:

```
:g/console/d
```

Este sería el resultado:

```
const one = 1;

const two = 2;

const three = 3;
```

El comando global ejecuta el comando eliminar (`d`) en todas las líneas que coincidan con el patrón "console".

Al ejecutar el comando `g`, Vim realiza dos escaneos del archivo. En el primero, escanea cada línea y marca las líneas que coinciden con el patrón `/console/`. Una vez que todas las líneas que coinciden están marcadas, vuelve a hacer otra pasada por todo el archivo, donde ejecuta el comando `d` en las líneas marcadas.

Si en vez de eso, quieres eliminar todas las líneas que contienen "const", ejecuta:

```
:g/const/d
```

Este sería el resultado:

```
console.log("one: ", one);

console.log("two: ", two);

console.log("three: ", three);
```

## Invertir las coincidencias

Para ejecutar el comando global en las líneas que no cumplan la coincidencia con el patrón, puedes ejecutar:

```
:g!/{patrón}/{comando}
```

o

```
:v/{patrón}/{comando}
```

Si ejecutas `:v/console/d`, esto eliminará todas las líneas que *no* contengan la palabra "console".

## Patrón

El comando global utiliza el mismo sistema de patrones que el comando de sustitución, así que esta sección servirá como repaso. Si lo deseas, sientete libre de saltar a la siguiente sección o sigue leyendo.

Si tienes las siguientes expresiones:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Para eliminar las líneas que contienen tanto "one" o "two", ejecuta:

```
:g/one\|two/d
```

Para eliminar las líneas que contienen algún número de una cifra, ejecuta una de estas dos opciones:

```
:g/[0-9]/d
```

o

```
:g/\d/d
```

Si tienes las siguientes expresiones:

```
const oneMillion = 1000000;
const oneThousand = 1000;
const one = 1;
```

Para encontrar las líneas que contienen entre tres a seis ceros, ejecuta:

```
:g/0\{3,6\}/d
```

## Pasando un rango

Puedes pasar un rango antes del comando `g`. Aquí tienes algunas maneras de cómo lo puedes hacer:

- `:1,5/g/console/d`  encuentra la cadena "console" entre las líneas 1 y 5 y las elimina.
- `:,5/g/console/d` si no se especifica un número delante de la coma, entonces esto indica que comience el comando desde la línea actual. Busca la cadena "console" entre la línea actual y la línea 5 y las elimina.
- `:3,/g/console/d` si no se especifica un número después de la coma, entonces indica que el comando finaliza en la línea actual. Busca la cadena "console" entre la línea 3 y la línea actual y las elimina.
- `:3g/console/d` si solo se le pasa un número sin el símbolo de la coma, esto ejecuta el comando solo en la línea 3. Busca en la línea 3 y la elimina si existe la cadena "console".

Además de los números, también se pueden utilizar esto símbolos como rango:
- `.` significa la línea actual. Un rango de `.,3` significa entre la línea actual y la línea 3.
- `$` significa la última línea del archivo. `3,$` es un rango entre la línea 3 y la última línea del archivo.
- `+n` significa *n* líneas después de la línea actual. Lo puedes utilizar con `.` o sin ese símbolo. `3,+1` o `3,.+1` ambas significa, entre la línea 3 y la línea después de la línea actual.

Si no pasas ningún rango, de manera predeterminada se entiende que el comando se aplica al archivo completo. Esto realmente no es la norma general. La mayoría de los comandos para la línea de comandos de Vim se ejecutan solo en la línea actual si no se le pasa ningún rango. Las dos excepciones más notables son los comandos globales (`:g`) y el comando de guardar (`:w`). 

## El comando normal

Puedes ejecutar un comando normal dentro del comando global con el comando `:normal` de la línea de comandos.

Si tienes el siguiente texto:
```
const one = 1
console.log("one: ", one)

const two = 2
console.log("two: ", two)

const three = 3
console.log("three: ", three)

```
Para añadir un símbolo ";" al final de cada línea, ejecuta:
```
:g/./normal A;
```

Diseccionesmos el comando:
- `:g` es el comando global.
- `/./` es el patrón para "todas las líneas que no estén vacías". Recuerda que el punto (`.`) es un registro que representa *cualquier caracter*. Lo que hace es buscar las líneas con al menos un caracter, así que en este caso marcará las líneas con las palabras "const" y "console". Ignorando las líneas vacías.
- `normal A;` ejecuta el comando de la línea de comando `:normal`. `A;` es el comando normal para insertar un ";" al final de la línea.

## Ejecutar una macro

También puedes ejecutar una macro con el comando global. Una macroes simplemente un modo normal de operación, así que es posible ejecutarlo con `:normal`. Si tienes las siguiente expresiones:

```
const one = 1
console.log("one: ", one);

const two = 2
console.log("two: ", two);

const three = 3
console.log("three: ", three);
```

Ten en cuenta que las líneas con "const" no tienen un símbolo de punto y coma al final de la línea. Vamos a crear una macro, que almacenaremos en el registro "a", que añada un símbolo de punto y coma al final de esas líneas:

```
qa0A;<esc>q
```

Si necesitas refrescar conocimientos, echa un vistazo al capítulo que trata sobre las macros. Ahora ejecuta:

```
:g/const/normal @a
```

Ahora todas las líneas con "const" tendrán un ";" al final de la línea.

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

## Comando recursivo global

El comando global pro sí mismo es un tipo de comando de la línea de comandos, así que técnicamente puedes ejecutar el comando global dentro de un comando global.

Dadas las siguientes expresiones:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Si ejecutas:

```
:g/console/g/two/d
```

Primero, `g` buscará las líneas que contengan el patrón "console" y encontrará 3 coincidencias. El segundo `g` buscará la línea que contenga el patrón "two" para esas tres coincidencias encontradas anteriormente. Finalmente, eliminará esas coincidencias.

Puedes combinar `g` con `v` para encontrar patrones positivos o negativos. Por ejemplo:

```
:g/console/v/two/d
```

En vez de buscar las líneas que contienen "two", buscará las líneas que *no* contienen el patrón "two".

## Cambiando el símbolo del delimitador del comando global

Puedes cambiar el delimitador del comando global de igual manera que se podía hacer en el comando de sustitución. Las reglas son las mismas: puedes utilizar cualquier caracter de un único byte excepto las letras del alfabeto, números, `"`, `|`, y `\`.

Para eliminar las líneas que contienen la palabra "console":

```
:g@console@d
```

Si estás utilizando el comando de sustitución con el comando global, puedes utilizar dos delimitadores diferentes:

```
g@one@s+const+let+g
```

Aquí el comando global buscará todas laslíneas que contienen "one". El comando de sustitución, sustituirá de esas líneas encontradas, la cadena "const" con "let".

## The Default Command

What happens if you don't specify any command-line command in the global command? 

The global command will use the print (`:p`) command to print the current line's text. If you run:

```
:g/console
```

It will print at the bottom of the screen all the lines containing "console".

By the way, here is one interesting fact. Because the default command used by the global command is `p`, this makes the `g` syntax to be:

```
:g/re/p
```

- `g` = the global command
- `re` = the regex pattern
- `p` = the print command

It spells *"grep"*, the same `grep` from the command line. This is **not** a coincidence. The `g/re/p` command originally came from the Ed Editor, one of the first line text editors. The `grep` command got its name from Ed. 

Your computer probably still has the Ed editor. Run `ed` from the terminal (hint: to quit, type `q`).

## More examples

### Reverse the Entire Buffer

To reverse the entire file, run:

```
:g/^/m 0 
```

`^` is a pattern for the "beginning of a line". Use `^` to match all lines, including empty lines.

If you need to reverse only a few lines, pass it a range. To reverse the lines between line five to line ten, run:

```
:5,10g/^/m 0
```

To learn more about the move command, check out `:h :move`.

### Aggregating All TODOs

When I code, sometimes I think of a random brilliant ideas. Not wanting to lose concentration, I usually write them down in the file I am editing, for example:

```
const one = 1;
console.log("one: ", one);
// TODO: feed the puppy

const two = 2;
// TODO: feed the puppy automatically
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
// TODO: create a startup selling an automatic puppy feeder
```

It can be hard to keep track of all the created TODOs. Vim has a `:t` (copy) method to copy all matches to an address. To learn more about the copy method, check out `:h :copy`.

To copy all TODOs to the end of the file for easier introspection, run:

```
:g/TODO/t $
```

Result:

```
const one = 1;
console.log("one: ", one);
// TODO: feed the puppy

const two = 2;
// TODO: feed the puppy automatically
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
// TODO: create a startup selling an automatic puppy feeder

// TODO: feed the puppy
// TODO: feed the puppy automatically
// TODO: create a startup selling an automatic puppy feeder
```

Now I can review all the TODOs I created, find a time to do them or delegate them to someone else, and continue to work on my next task.

Another alternative is to use `m`:

```
:g/TODO/m $
```

Result:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);

// TODO: feed the puppy
// TODO: feed the puppy automatically
// TODO: create a startup selling an automatic puppy feeder
```

I can just delete the list once I decided what to do with it.

### Black Hole Delete

Recall from the register chapter that deleted texts are stored inside the numbered registers (granted they are sufficiently large ). Whenever you run `:g/console/d`, Vim stores the deleted lines in the numbered registers. If you delete many lines, you can quickly fill up all the numbered registers. To avoid this, you can always use the black hole register (`"_`) to *not* store your deleted lines into the registers. Run:

```
:g/console/d _
```

By passing `_` after `d`, Vim won't save the deleted lines into any registers.

### Reduce Multiple Empty Lines to One Empty Line

If you have a file with multiple empty lines like the following:

```
const one = 1;
console.log("one: ", one);


const two = 2;
console.log("two: ", two);





const three = 3;
console.log("three: ", three);
```

You can quickly reduce each the long empty lines to one empty line. Run:

```
:g/^$/,/./-1j
```

Result:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Let's break it down:
- `:g` is the global command
- `/^$/` is the pattern for an empty line. Recall that `^` means the beginning of the line and `$` the end of the line. `^$` matches an empty line (a line with zero characters long).
- `,/./-1` is the range for the `j` command. Since you don't pass a value for the starting range, it starts from the current line. You just learned earlier that `/./` is a pattern for a non-empty line. `,/./` is a range from the current line to the next non-empty line. The global command's range, `/^$/`, takes you to the first match on the line below `console.log("one: ", one);`. This is the current line. `/./` matches the first non-empty line, the line `const two = 2;`. Finally, `-1` offsets that by one line. The effective range for the first match is the empty line below the `console.log("one: ", one);` and the empty line above the `const two = 2;`.
- `j` is the join command `:j`. You can join all the lines given as its range. For example, `:1,5j` joins lines one through five.

Notice that you are passing a range (`,/./-1`) before the `j` command. Just because you are using a command-line command with the global command, does not mean you cannot give it a range. In this code, you are passing to the `j` command its own range to execute on. You can pass a range to any command while executing the global command.

By the way, if you want to reduce multiple empty lines into no lines, instead of using `,/./-1` as the range for `j` command, just use `,/./` as the range instead:

```
:g/^$/,/./j
```

Or simpler:

```
:g/^$/-j
```

Your text is now reduced to:

```
const one = 1;
console.log("one: ", one);
const two = 2;
console.log("two: ", two);
const three = 3;
console.log("three: ", three);
```

### Advanced sort

Vim has a `:sort` command to sort the lines within a range. For example:

```
d
b
a
e
c
```

You can sort them by running `:sort`. If you give it a range, it will sort only the lines within that range. For example, `:3,5sort` sorts only between lines three and five. 

If you have the following expressions:

```
const arrayB = [
  "i",
  "g",
  "h",
  "b",
  "f",
  "d",
  "e",
  "c",
  "a",
]

const arrayA = [
  "h",
  "b",
  "f",
  "d",
  "e",
  "a",
  "c",
]
```

If you need to sort the elements inside the arrays, but not the arrays themselves, you can run this:

```
:g/\[/+1,/\]/-1sort
```

Result:
```
const arrayB = [
  "a",
  "b",
  "c",
  "d",
  "e",
  "f",
  "g",
  "h",
  "i",
]

const arrayA = [
  "a"
  "b",
  "c",
  "d",
  "e",
  "f",
  "h",
]
```

This is great! But the command looks complicated. Let's break it down. The command consists of three main parts: the global command pattern, the sort command range, and the sort command.

`:g/\[/` is the global command pattern. 
- `:g` is the global command.
- `/\[/` is the pattern used by the global command. `\[` looks for a literal "[" string.

`+1,/\]/-1` is the range for the sort command.
- A range can have a starting and an ending addresses. In this case, `+1` is the starting address and `/\]/-1` is the ending address.
- `+1` represents the line after the current line, which is the line that matches the pattern "[" from the global command. `+1` offsets the current line by one line. So in the first match, the range actually starts one line *after* the `const arrayB = [` text.
- `/\]/-1` is the ending address. `\]` represents a literal closing square bracket "]". `-1` offsets it by one line. The ending address is the line above the "]".

`sort` is the sort command-line command. It sorts everything within the given range. Everything after the "[" to the line above "]" is getting sorted.

If you are still confused by the command, do not worry. It took me a long time to grasp it. Take a break, leave the screen, and come back again with a fresh mind.

## Learn the Global Command the Smart Way

The global command executes the command-line command against all matching lines. With it, you only need to run a command once and Vim will do the rest for you. To become proficient at the global command, two things are required: a good vocabulary of command-line commands and a knowledge of regular expressions. As you spend more time using Vim, you will naturally learn more command-line commands. A regular expression knowledge will require a more active approach. But once you become comfortable with regular expressions, you will be ahead of many. 

Some of the examples here are complicated. Do not be intimidated. Really take your time to understand them. Learn to read the patterns. Make sure you know what each letter in each command represent. Do not give up.

Whenever you need to apply a command in several locations, pause and see if you can use the `g` command. Look for the best command for the job and write a pattern to target as many things at once. Then repeat it until you can do it without thinking. The next time, see if there is even a faster and more efficient way to do it.

Now that you know how powerful the global command is, let's learn how to use the external commands to increase your tool arsenals.
