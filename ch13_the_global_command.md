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

## Passing a Range

You can pass a range before the `g` command. Here are some ways you can do it:

- `:1,5/g/console/d`  matches the string "console" between lines 1 and 5 and deletes them.
- `:,5/g/console/d` if there is no address before the comma, then it starts from the current line. It looks for the string "console" between the current line and line 5 and deletes them.
- `:3,/g/console/d` if there is no address after the comma, then it ends at the current line. It looks for the string "console" between line 3 and the current line and deletes them.
- `:3g/console/d` if you only pass one address without a comma, it executes the command only on line 3. It looks on line 3 and deletes it if has the string "console".

In addition to numbers, you can also use these symbols as range:
- `.` means the current line. A range of `.,3` means between the current line and line 3.
- `$` means the last line in the file. `3,$` range means between line 3 and the last line.
- `+n` means n lines after the current line. You can use it with `.` or without. `3,+1` or `3,.+1` means between line 3 and the line after the current line.

If you don't give it any range, by default it affects the entire file. This is actually not the norm. Most of Vim's command-line commands run on only the current line if you don't pass it any range. The two notable exceptions are the global (`:g`) and the save (`:w`) commands. 

## Normal Command

You can run a normal command with the global command with `:normal` command-line command.

If you have this text:
```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);

```
To add a ";" to the end of each line, run:
```
:g/./normal A;
```

Let's break it down:
- `:g` is the global command.
- `/./` is a pattern for "non-empty lines". Recall that the dot (`.`) in regex represents *any character*. It matches the lines with at least one character, so it matches the lines with "const" and "console". It does not match empty lines.
- `normal A;` runs the `:normal` command-line command. `A;` is the normal mode command to insert a ";" at the end of the line.

## Executing a Macro

You can also execute a macro with the global command. A macro is just a normal mode operation, so it is possible to execute a macro with `:normal`. If you have the expressions:

```
const one = 1
console.log("one: ", one);

const two = 2
console.log("two: ", two);

const three = 3
console.log("three: ", three);
```

Notice that the lines with "const" do not have semi-colons. Let's create a macro to add a comma to the end of those lines in the register "a":

```
qa0A;<esc>q
```

If you need a refresher, check out the chapter on macro. Now run:

```
:g/const/normal @a
```

Now all lines with "const" will have a ";" at the end.

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

## Recursive Global Command

The global command itself is a type of a command-line command, so you can technically run the global command inside a global command.

Given the expressions:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

If you run:

```
:g/console/g/two/d
```

First, `g` will look for the lines containing the pattern "console" and find 3 matches. Then the second `g` will look for the line containing the pattern "two" from those three matches. Finally, it will delete that match.

You can also combine `g` with `v` to find positive and negative patterns. For example:

```
:g/console/v/two/d
```

Instead of looking for the line containing the pattern "two", it will look for the lines *not* containing the pattern "two".

## Changing the Delimiter

You can change the global command's delimiter like the substitute command. The rules are the same: you can use any single byte character except for alphabets, numbers, `"`, `|`, and `\`.

To delete the lines containing "console":

```
:g@console@d
```

If you are using the substitute command with the global command, you can have two different delimiters:

```
g@one@s+const+let+g
```

Here the global command will look for all lines containing "one". The substitute command will substitute, from those matches, the string "const" with "let".

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
