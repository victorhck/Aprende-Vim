# Capítulo 12: Buscar y sustituir

Este capítulo trata sobre dos conceptos separados pero relacionados: buscar y sustituir. Muchas veces, los textos que estás buscando no son simples y debes buscar un patrón común. Aprender cómo utilizar patrones significativos en las búsquedas y sustituciones en vez de cadenas de texto literales, hará que seas capaz de localizar cualquier texto rápidamente.

Como nota complementaria, en este capítulo, utilizará principalmente `/` cuando me refiera a la búsqueda. Todo lo que puedes realizar con el comando `/` puede también ser realizado con `?`.

## Sensibilidad inteligente a mayúsculas y minúsculas

Puede ser complicado hacer coincidir las mayúsculas y minúsculas en el término buscado. Si estás buscando el texto "Aprende Vim", te puedes confundir fácilmente al escribir una letra y obtener unos resultados de la búsqueda falsos. ¿No sería más sencillo y fácil si pudieras hacer coincidir tanto si está en mayúsculas o minúsculas? Aquí es donde la opción `ignorecase` es más útil. Simplemente añade `set ignorecase` en tu archivo de configuración vimrc y todos tus términos de búsqueda no tendrán en cuenta las mayúsculas o minúsculas. Ahora ya no será necesario que escribas `/Learn Vim` nunca mas. `/learn vim` será más que necesario para realizar la misma búsqueda.

Sin embargo, hay ocasiones en las que necesitas buscar una frase específica con diferenciación entre mayúsculas o minúsculas. Una forma de hacer eso es inhabilitando la opción `ignorecase` mediante `set noignorecase`, pero es muy laborioso el inhabilitar y volver a habilitar la opción cada vez que necesites una u otra forma de realizar búsquedas.

¿Existe un ajuste que te permita realizar búsquedas que no tengan en cuenta las mayúsculas la mayor parte de las veces, pero también sepa cuando una búsqueda requiere buscar las mayúsculas cuando lo necesites? Resulta que hay una manera.

Vim tiene una opción llamada `smartcase` para anular `ignorecase` si el patrón de búsqueda *contiene al menos una letra en mayúscula*. Puedes combinar tanto `ignorecase` y `smartcase` para realizar búsquedas que no tengan en cuenta las mayúsculas cuando introduzcas caracteres en minúsculas y tenga en cuenta las mayúsculas y minúsculas cuando introduzcas una o más letras en mayúsculas.

Dentro de tu archivo vimrc, añade:

```
set ignorecase smartcase
```

Si tienes estos textos:
```
hola
HOLA
Hola
```

Puedes controlar si la búsqueda debe encontrar uno u otro término de la manera en la que escribas el el término de búsqueda con o sin mayúsculas:
- `/hola` encontrará los términos "hola", "HOLA" y "Hola".
- `/HOLA` encontrará solo "HOLA".
- `/Hola` encontrará solo "Hola".

Solo hay una pega. ¿Qué pasa cuando necesitas solo encontrar la cadena de texto en minúscula? Cuando buscas `/hola`, Vim siempre encontrará sus variantes con mayúsculas. ¿Qué pasa si no quieres que se muestren en la búsqueda? Puedes utilizar el patrón `\c` anteponiéndolo a tu búsqueda para decir que en el término de búsqueda siguiente tenga en cuenta las minúsculas. Ahora si ejecutas `/\chola`, restringirá las coincidencias únicamente a "hola" y no aparecerán ni "HOLA" ni "Hola".

## El primer y último caracter en una línea

Puedes utilizar `^` para encontrar el primer caracter en una línea y `$` para encontrar el última caracter en una línea.

Si tienes este texto:

```
hola hola
```

Puedes llegar al primer "hola" con `/^hello`. El caracter que siga a `^` deberá ser el primer caracter de la línea. Para buscar el último "hola", ejecuta `/hello$`. El caracter anterior a `$` deberá se el último caracter de la línea. 

Si tienes el siguiente texto:

```
hola hola amigo
```

Al ejecutar `/hello$` no encontrará nada porque "amigo" es el último término en esa línea, no "hola".

## Repetir la búsqueda

Puedes repetir la búsqueda con `//`. Si has buscado `/hola`, al ejecutar `//` será equivalente a ejecutar `/hola`. Este atajo de teclado te puede ahorrar varias pulsaciones de teclas, especialmente si anteriormente hiciste una búsqueda de un término bastante largo. También recordar que puedes utilizar `n` y `N` para repetir la última búsqueda en la misma dirección o en la dirección contraria, respectivamente.

¿qué pasa si quieres volver a ejecutar rápidamente la búsqueda anterior *n*? Puedes navegar rápidamente por el historial de búsquedas pulsando primero la tecla `/`, y después las teclas  `arriba`/`abajo` (o `Ctrl-N`/`Ctrl-P`) hasta que encuentres el término de búsqueda que necesitas. Para ver todo el historial de búsquedas, puedes ejecutar `:history /`.

Cuando llegues al final de un archivo mientras estás buscando, Vim te mostrará un error: `"Search hit the BOTTOM without match for: <your-search>"`. A veces esto puede ser una buena protección para no hacer una búsqueda sin fin, pero otras veces puedes necesitar que el ciclo de búsqueda vuelva a empezar desde la parte superior de nuevo. Puedes utilizar la opción `set wrapscan` para hacer que Vim vuelva a buscar desde el inicio de un archivo cuando llega al final de un archivo. Para inhabilitar esta opción simplemente ejecuta `set nowrapscan`.

## Buscando palabras alternativas

Es común buscar múltiples palabras a la vez. Si necesitas buscar *tanto* "hello vim" u "hola vim", pero no "salve vim" o "bonjour vim", puedes utilizar la tubería `|`.

Dado este texto:

```
hello vim
hola vim
salve vim
bonjour vim
```

Para encontrar tanto "hello" como "hola", puedes ejecutar `/hello\|hola`. Es necesario *escapar* (`\`) el operador de la tubería (`|`), de lo contrario Vim literalmente buscará la cadena "|". 

Si no quieres escribir `\|` cada vez, puedes utilizar la sintaxis `magic` (`\v`) al comienzo de la búsqueda: `/\vhello|hola`. No se va a tratar `magic` en este capítulo, pero con `\v`, ya no necesitará escapar caracteres especiales nunca más. Para aprender más sobre `\v`, echa un vistazo a la ayuda de Vim mediante `:h \v`.

## Estableciendo el inicio y el final de una búsqueda

Quizás necesites buscar un texto que es parte de una palabra compuesta. Si tienes estos textos:

```
11vim22
vim22
11vim
vim
```

Si necesitas seleccionar "vim" pero solo cuando esta comienza con "11" y termina con "22", puedes utilizar los comandos `\zs` (coincidencia de comienzo) y `\ze` (coincidencia final). Ejecuta:

```
/11\zsvim\ze22
```

Vim buscará el patrón entero "11vim22", pero solo resaltará el patrón que está entre los comandos `\zs` y `\ze`. Otro ejemplo:

```
foobar
foobaz
```

Si necesitar buscar "foo" en "foobaz" pero no en "foobar", ejecuta:

```
/foo\zebaz
```

## Buscando un rango de caracteres

Todos tus términos de búsqueda hasta este punto han sido una búsqueda literal de una palabra. En la vida real, puede que tengas que utilizar un patrón general para encontrar tu texto. El patrón básico es el rango de caracteres, `[ ]`.

si necesitas buscar cualquier dígito, lo más probable es que no quieras escribir `/0\|1\|2\|3\|4\|5\|6\|7\|8\|9\|0` cada vez que lo necesites. En vez de eso, utiliza `/[0-9]` para encontrar cualquier dígito entre ese rango. La expresión `0-9` representa un rangos de números entre 0-9 que Vim intentará encontrar, así que si estás buscando dígitos entre 1 y 5, en ese caso utilizaríamos `/[1-5]`. 

Los dígitos no son el único tipo de datos que Vim puede buscar. También puedes buscar entre `/[a-z]` para buscar letras en minúsculas y `/[A-Z]` para letras en mayúsculas. 

También puedes combinar estos rangos juntos. Si necesitas buscar dígitos entre 0-9 y a la vez letras en mayúsculas y minúsculas de la "a" a la "f" (una cifra en hexadecimal), puedes ejecutar `/[0-9a-fA-F]`. 

Para realizar una búsqueda inversa, puedes añadir `^` dentro de los corchetes dentro del rango. Para buscar algo que no sea un dígito, ejecuta `/[^0-9]`. Vim encontrará cualquier caracter mientras que no sea un dígito. Ten en cuenta que el caracter (`^`) dentro de los corchetes del rango es diferente del caracter al principio de una línea (ejemplo: `/^hello`). Si el caracter está fuera del par de corchetes y es el primer caracter en el término de búsqueda, esto significa "el primer caracter en una línea". Si el caracter está dentro de un par de corchetes y es el primer caracter dentro de los corchetes, esto significa un operador de búsqueda negativo. `/^abc` encontrará el primer "abc" en una línea y `/[^abc]` encontrará cualquier caracter excepto una "a", "b" o "c".

## Buscando caracteres repetidos

Si necesitas buscar dígitos dobles en este texto:

```
1aa
11a
111
```

Puedes utilizar `/[0-9][0-9]` para encontrar una pareja de dos dígitos, pero este método no es escalable. ¿Qué pasa si necesitas encontrar veinte dígitos? Escribir `[0-9]` veinte veces no es una solución muy elegante. Por eso necesitas el argumento `count`.

Puedes pasarle el argumento `count` a tu búsqueda. Tiene la siguiente sintaxis:

```
{n,m}
```

Por cierto, estos llaves de `count` necesitan ser *escapadas* cuando las utilizas en Vim. El operador `count` es ubicado después del caracter que quieres incrementar.

Aquí tienes cuatro variaciones diferente de la sintaxis de `count`:
- `{n}` es una búsqueda exacta. `/[0-9]\{2\}`  encontrará números de dos dígitos: "11" y el "11" en "111".
- `{n,m}` es un rango de búsqueda. `/[0-9]\{2,3\}` encontrará números con 2 y 3 dígitos: "11" y "111".
- `{,m}` es un límite superior debúsqueda. `/[0-9]\{,3\}` encontrará números hasta de 3 dígitos: "1", "11" y "111".
- `{n,}` es una búsqueda con límite al menos *n* elementos. `/[0-9]\{2,\}` encontrará números con al menos 2 o más dígitos: "11" y "111".

Los argumentos de contaje `\{0,\}` (cero o más) y `\{1,\}` (uno o más) son patrones de búsqueda comunes y Vim tiene operadores especiales para estos casos: `*` y `+` (`+` necesita ser *escapado* mientras que `*` funciona bien sin necesidad de añadir símbolos de escape). Si ejecutas `/[0-9]*`, esto es lo mismo que ejecutar `/[0-9]\{0,\}`. Buscará cero o más dígitos. Esto encontrará "", "1", "123". Por cierto, también encontrará caracteres que no sean dígitos como "a", porque técnicamente hay un dígito cero en la letra "a". Piénsalo detenidamente antes de utilizar `*`. Si ejecutas `/[0-9]\+`, es lo mismo que ejecutar `/[0-9]\{1,\}`. Esto buscará uno o más dígitos. Encontrará "1" y "12".

## Rangos predefinidos

Vim tiene unos rangos predefinidos para los caracteres más comunies como dígitos o letras. No vamos a entrar en detalle de todos, pero si lo necesitas puedes encontrar la lista ejecutando: `:h /character-classes`. Aquí tienes los más útiles:

```
\d    Dígitos [0-9]
\D    No digitos [^0-9]
\s    Caracter de espacio en blanco (espacio y tabulador)
\S    Caracter que no sea espacio en blanco (todo excepto un espacio o tabulador)
\w    Caracter de palabra [0-9A-Za-z_]
\l    Letras minúsculas [a-z]
\u    Letras mayúsculas [A-Z]
```

Los puedes utilizar como utilizarías un rango de caracteres. Para buscar un único dígito, en vez de utilizar `/[0-9]`, puedes utilizar `/\d` para una sintaxis más concisa.

## Mas ejemplos de búsqueda
### Encontrar un texto entre un par de caracteres similares

Si quieres buscar una frase rodeada por un par de comillas dobles:

```
"¡Vim es asombroso!"
```

Ejecuta esto:

`/"[^"]\+"`

Vamos a diseccionar el comando:
- `"` es una doble comilla literal. Encontrará la primera comilla doble.
- `[^"]` significa cualquier caracter excepto una comilla doble. Encontrará cualquier caracter alfanumérico y espacios en blanco mientras que no sea una comilla doble.
- `\+` significa uno o más. Como está precedido por `[^"]`, Vim busca uno o más caracteres que no sean una comilla doble.
- `"` es una doble comilla literal. Encontrará las comillas dobles de cierre.

Cuando ve el primer `"`, comenzará el patrón de captura. En el momento que Vim ve las segundas comillas dobles en una línea, encuentra el segunto patrón de `"` y detiene el patrón de captura. Mientras, todos los caracteres que no sean `"` entre las dos comillas `"` son capturados por el patrón `[^"]\+`, en este caso, la frase `¡Vim es asombroso!`. Este es un patrón común a la hora de capturar una frase encerrada entre un par de delimitadores similares: para capturar una frase entre dos comillas simples, puedes utilizar  `/'[^']\+'`. 

### Encontrando un número de teléfono

Si quieres encontrar un número de teléfono de EE.UU. separados por guiones (`-`), como `123-456-7890`, puedes utilizar:

```
/\v\d\{3\}-\d\{3\}-\d\{4\}
```

Los números de teléfono de EE.UU. están formados por un conjunto de tres números, seguidos por otros tres números y finalmente cuatro dígitos. Vamos a ver en detalle el comando:

- `\d\{3\}` contrará un dígito repetido exactamente tres veces
- `-` es literalmente un guión

Este patrón es también útil para capturar dígitos repetidos, como direcciones IP o códigos postales.

Con esto finaliza la parte de búsquedas de este capítulo. Ahora veamos las sustituciones.

## Sustitución básica

El comando de sustitución de Vim es útil para rápidamente encontrar y reemplazar cualquier patrón. La sintaxis de una sustitución básica es:

```
:s/patrón_antiguo/patrón_nuevo/
```

Vamos a empezar con un uso básico. Si tenemos el siguiente texto:

```
vim es bueno
```

Vamos a sustituir "bueno" con "increíble" porque Vim es realmente increíble. Ejecuta `:s/bueno/increíble/.` Con lo que quedaría de esta forma:

```
vim es increíble
```

## Repetir la última sustitución

Puedes repetir el último comando de sustitución ya sea con el comando normal `&` o ejecutando `:s`. Si acabas de ejecutar `:s/good/awesome/`, tanto ejecutando `&` o `:s` repetirá la misma sustitución. 

También, previamente en este mismo capítulo mencioné que puedes utilizar `//` para repetir el patrón de búsqueda previo. Este truco funciona con el comando de sustitución. Si `/bueno` fue ejecutado recientemente y dejas el primer argumento del patrón de sustitución en blanco, de esta manera `:s//increíble/`, es lo mismo que ejecutar lo siguiente `:s/bueno/increíble/`.

## Rango de sustituciones

Igual que en muchos comandos Ex, puedes pasarle un rango como argumento al comando de sustitución. La sintaxis es:

```
:[rango]s/antiguo/nuevo/
```

Si tienes estas expresiones:

```
let uno = 1;
let dos = 2;
let tres = 3;
let cuatro = 4;
let cinco = 5;
```

Para sustituir "let" por "const" de la línea tres a la cinco, deberás ejecutar:

```
:3,5s/let/const/
```

La sintaxis del rango del comando de sustitución es similar a la sintaxis del contador en el comando de la búsqueda (`{n,m}`), con algunas pequeñas diferencias. Aquí tienes algunas variaciones para pasar el rango:

- `:,3/let/const/` - si no se da ningún parámetro antes de la coma, representa la línea actual. Sustituye desde la línea actual hasta la línea 3.
- `:1,s/let/const/` - si no se da ningún dato después de la coma, también representa la línea actual. Sustituye desde la línea 1 hasta la línea actual.
- `:3s/let/const/` - si solo se da un valor como rango (sin coma), realiza la sustitución únicamente en esa línea.

En Vim, `%` normalmente se refiere al archivo entero. Si ejecutas `:%s/let/const/`, esto realizará la sustitución en todas las líneas del archvo.

## Encontrando un patrón

En las siguientes secciones vamos a tratar de ver algunas de las expresiones regualres básicas. El conocimiento de un patrón robusto es esencial para dominar el comando de sustitución.

Imaginemos que tenemos las siguientes expresiones:

```
let uno = 1;
let dos = 2;
let tres = 3;
let cuatro = 4;
let cinco = 5;
```

Y queremos añadir un par de comillas dobles al comienzo y final de cada número:

```
:%s/\d/"\0"/
```

Este es el resultado:
```
let uno = "1";
let dos = "2";
let tres = "3";
let cuatro = "4";
let cinco = "5";
```

Vamos a ver el comando en detalle:

- `:%s` esto busca en el archivo entero para realizar una acción de sustitución.
- `\d` es el rango predefinido en Vim para los dígito, similar a (`[0-9]`). 
- `"\0"` las comillas dobles son literalmente comillas dobles. `\0` es un caracter especial que representa "el patrón de encontrado completo". El patrón de búsqueda aquí es un número de un solo dígito, `\d`. En la línea uno, `\0` tienes el valor "1". En la línea dos, tiene el valor "2". En la línea tres, el valor "3", y así sucesivamente.

De igual manera, el símbolo `&` también representa "el patrón encontrado completo" igual que `\0`. Por lo que `:s/\d/"&"/` también hubiera funcionado

Vamos a considerar otro ejemplo, Dadas estas expresiones:
```
uno let = "1";
dos let = "2";
tres let = "3";
cuatro let = "4";
cinco let = "5";
```
Necesitas intercambiar todos los "let" con los nobres de las variable. Para hacer eso, ejecuta:

```
:%s/\(\w\+\) \(\w\+\)/\2 \1/
```

El comando anterior contine demasiadas barras invertidas y es difícil de leer. Es más conveniente utilizar el operador `\v`:

```
:%s/\v(\w+) (\w+)/\2 \1/
```

Este es el resultado:

```
let uno = "1";
let dos = "2";
let tres = "3";
let cuatro = "4";
let cinco = "5";
```

¡Genial! Veamos el comando en detalle:

- `:%s` busca en todas las líneas del archivo.
- `(\w+) (\w+)` agrupa los patrones. `\w` es uno de los rangos predefinidos de Vim para un caracter d euna palabra (`[0-9A-Za-z_]`). Los paréntesis `( )` rodeando captura un caracter de palabra coincidente en un frupo. Ten en cuenta el espacio entre los dos agrupamientos. `(\w+) (\w+)` captura en dos grupos. en la primera línea, el primer grupo captura el "uno" y el segundo grupo captura el "dos".
- `\2 \1` devuelve el grupo capturado en orden inverso. `\2` contiene la cadena "let" y `\1` la cadena "uno". Al tener `\2 \1` esto devuelve la cadena "let uno".

Recordemos que `\0` representa el patrón encontrado al completo. Puedes romper la cadena encontrada en grupos más pequeños con `( )`. Cada grupo es representado por `\1`, `\2`, `\3`, etc.

Vamos a ver un ejemplo más para tratar de clarificar más el concepto de grupo encontrado. Si tienes los siguiente números:

```
123
456
789
```

Para invertir el orden, ejecuta:
```
:%s/\v(\d)(\d)(\d)/\3\2\1/
```

El resultado es:

```
321
654
987
```

Cada `(\d)` encuentra y agrupa cada dígito. En la primera línea, el primer `(\d)` tiene el valor "1", el segundo `(\d)` "2" y el trecer `(\d)` "3". Son almacenados en las variables `\1`, `\2` y `\3`. En la segunda mitad de tu sustitución, el nuevo patrón `\3\2\1` resultará en el valor "321" en la primera línea.

Si hubieras ejecutado este comando:
```
:%s/\v(\d\d)(\d)/\2\1/
```
Hubieras obtenido un resultado diferente:
```
312
645
978
```

Esto es debido a que ahora solo tienes dos grupos. El primer grupo, capturado por `(\d\d)`, es almacenado en `\1` y tiene el valor "12". El segundo grupo, capturado por `(\d)`, es almacenado dentro de `\2` y tiene el valor "3". `\2\1` entonces, esto devolverá "312".

## Substitution Flags

If you have the sentence:

```
chocolate pancake, strawberry pancake, blueberry pancake
```

To substitute all the pancakes into donuts, you cannot just run:

```
:s/pancake/donut
```

The command above will only substitute the first match, giving you:

```
chocolate donut, strawberry pancake, blueberry pancake
```

There are two ways to solve this. First, you can run the substitute command twice more. Second, you can pass it a global (`g`) flag to substitute all of the matches in a line. 

Let's talk about the global flag. Run:

```
:s/pancake/donut/g
```

Vim substitutes all pancakes with donuts in one swift command. The global command is one of the several  flags the substitute command accepts. You pass flags at the end of the substitute command. Here is a list of useful flags:

```
&    Reuse the flags from the previous substitute command. Must be passed as the first flag.
g    Replace all matches in the line.
c    Ask for substitution confirmation.
e    Prevent error message from displaying when substitution fails.
i    Perform case insensitive substitution
I    Perform case sensitive substitution
```

There are more flags that I do not list above. To read about all the flags, check out `:h s_flags`.

By the way, the repeat-substitution commands (`&` and `:s`) do not retain the flags. Running `&` will only repeat `:s/pancake/donut/` without `g`. To quickly repeat the last substitute command with all the flags, run `:&&`.

## Changing the Delimiter

If you need to replace a URL with a long path:

```
https://mysite.com/a/b/c/d/e
```

To substitute it with the word "hello", run:
```
:s/https:\/\/mysite.com\/a\/b\/c\/d\/e/hello/
```

However, it is hard to tell which forward slashes (`/`) are part of the substitution pattern and which ones are the delimiters. You can change the delimiter with any single-byte characters (except for alphabets, numbers, or `"`, `|`, and `\`). Let's replace them with `+`. The substitution command above then can be rewritten as:

```
:s+https:\/\/mysite.com\/a\/b\/c\/d\/e+hello+
```

It is now easier to see where the delimiters are.

## Special Replace

You can also modify the case of the text you are substituting. Given the following expressions:

```
let one = "1";
let two = "2";
let three = "3";
let four = "4";
let five = "5";

```
To uppercase the variables "one", "two", "three", etc., run:

```
%s/\v(\w+) (\w+)/\1 \U\2/

```
You will get:

```
let ONE = "1";
let TWO = "2";
let THREE = "3";
let FOUR = "4";
let FIVE = "5";
```

Here is the breakdown of that command:
- `(\w+) (\w+)` captures the first two matched groups, such as "let" and "one".
- `\1` returns the value of the first group, "let"
- `\U\2` uppercases (`\U`) the second group (`\2`).

The trick of this command is the expression `\U\2`. `\U` instructs the following character to be uppercased.

Let's do one more example. Suppose you are writing a Vim book and you need to capitalize the first letter of each word in a line.

```
vim is the greatest text editor in the whole galaxy
```

You can run:

```
:s/\<./\u&/g
```

The result:

```
Vim Is The Greatest Text Editor In The Whole Galaxy
```

Here is the breakdowns:
- `:s` substitutes the current line
- `\<.` is comprised of two parts: `\<` to match the start of a word and `.` to match any character. `\<` operator makes the following character to be the first character of a word. Since `.` is the next character, it will match the first character of any word.
- `\u&`  uppercases the subsequent symbol, `&`. Recall that `&` (or `\0`) represents the whole match. It matches the first character of nay word.
- `g` the global flag. Without it, this command only substitutes the first match. You need to substitute every match on this line.

To learn more of substitution's special replace symbols like `\u` and `\U`, check out `:h sub-replace-special`.

## Alternative Patterns

Sometimes you need to match multiple patterns simultaneously. If you have the following greetings:

```
hello vim
hola vim
salve vim
bonjour vim
```

You need to substitute the word "vim" with "friend" but only on the lines containing the word "hello" or "hola". Run:

```
:%s/\v(hello|hola) vim)/\1 friend/g
```

The result:
```
hello friend
hola friend
salve vim
bonjour vim
```

Here is the breakdown:
- `%s` runs the substitute command on each line in a file.
- `(hello|hola)` Matches *either* "hello" or "hola" and consider it as a group.
- `vim` is the literal word "vim".
- `\1` is the first match group, which is either the text "hello" or "hola".
- `friend` is the literal word "friend".

## Substituting the Start and the End of a Pattern

Recall that you can use `\zs` and `\ze` to define the start and the end of a match. This technique works in substitution too. If you have:

```
chocolate pancake
strawberry sweetcake
blueberry hotcake
```

To substitute the "cake" in "hotcake" with "dog" to get a "hotdog":

```
:%s/hot\zscake/dog/g
```

Result:

```
chocolate pancake
strawberry sweetcake
blueberry hotdog
```

You can also substitute the nth match in a line with this trick:

```
One Mississippi, two Mississippi, three Mississippi, four Mississippi, five Mississippi.
```

To substitute the third "Mississippi" with "Arkansas", run:

```
:s/\v(.{-}\zsMississippi){3}/Arkansas/g
```

The breakdown:
- `:s/` the substitute command.
- `\v` is the magic keyword so you don't have to escape special keywords.
- `.` matches any single character
- `{-}` performs non-greedy match of 0 or more of the preceding atom.
- `\zsMississippi` makes "Mississippi" the start of the match.
- `(...){3}` looks for the third match.

You have seen the `{3}` syntax before. It is a type of `{n,m}`. In this case, you have `{3}` which will match exactly the third match. The new trick here is `{-}`. It is a non-greedy match. It finds the shortest match of the given pattern. In this case, `(.{-}Mississippi)` matches the least amount of "Mississippi" preceded by any character. Contrast this with `(.*Mississippi)` where it finds the longest match of the given pattern.

If you use `(.{-}Mississippi)`, you get five matches: "One Mississippi", "Two Mississippi", etc. If you use `(.*Mississippi)`, you get one match: the last "Mississippi". To learn more check out `:h /\{-` and `:h non-greedy`.

Let's do a simpler example. If you have the string:

```
abc1de1
```

You can match "abc1de1" (greedy) with:

```
/a.*1
```

You can match "abc1" (non-greedy) with:

```
/a.\{-}1
```

So if you need to uppercase the longest match (greedy), run:

```
:s/a.*1/\U&/g
```

To get:

```
ABC1DEFG1
```

If you need to uppercase the shortest match (non-greedy), run:

```
:s/a.\{-}1/\U&/g
```

To get:

```
ABC1defg1
```

## Substituting Across Multiple Files

Finally, let's learn how to substitute phrases across multiple files. For this section, assume that you have two files: `food.txt` and `animal.txt`.

Inside `food.txt`:

```
corndog
hotdog
chilidog
```

Inside `animal.txt`:

```
large dog
medium dog
small dog
```

Assume your directory structure looks like this:

```
- food.txt
- animal.txt
```

First, capture both `food.txt` and `animal.txt` inside `:args`. Recall from earlier chapters that `:args` can be used to create a list of file names. There are several ways to do this from inside Vim:

```
:args *.txt                  captures all txt files in current location 
:args food.txt animal.txt    captures only index and server js files
:args **/*.txt               captures every txt files
:args **                     captures everything 
```

You can also run the commands above from outside Vim, passing the files as *arguments* for Vim (hence it is called the "args" command). From the terminal, run 

```
vim food.txt animal.txt
```

When Vim starts, you will find `food.txt` and `animal.txt` inside `:args`.

Either way, when you run `:args`, you should see:

```
[food.txt] animal.txt
```

To go to the next or previous argument on the list, type `:next` or `:previous`. Now that all the relevant files are stored inside the argument list, you can perform a multi-file substitution with the `:argdo` command. Run:

```
:argdo %s/dog/chicken/
```

This performs substitution against the all files inside the  `:args` list. Finally, save the changed files with:

```
:argdo update
```

`:args` and `:argdo` are  useful tools to apply command line commands across multiple files. Try it with other commands!

## Substituting Across Multiple Files with Macros

Alternatively, you can also run the substitute command across multiple files with macros. Let's start by getting the relevant files into the args list. Run:

```
:args animal.txt food.txt
qq
:%s/dog/chicken/g
:wnext
q
99@q
```

Here is the breakdown of the steps:
- `:args animal.txt food.txt` lists the relevant files into the `:args` list.
- `qq` starts the macro in the "q" register.
- `:%s/dog/chicken/g` substitutes "dog" with "chicken on all lines in the current file.
- `:wnext` writes (saves) the file then go to the next file on the `args` list. It's like running `:w` and `:next` at the same time.
- `q` stops the macro recording.
- `99@q` executes the macro ninety-nine times. Vim will stop the macro execution after it encounters the first error, so Vim won't actually execute the macro ninety-nine times.

## Learning Search and Substitution the Smart Way

The ability to do search well is a necessary skill in editing. Mastering the search lets you to utilize the flexibility of regular expressions to search for any pattern in a file. Take your time to learn these. Actually do the searches and substitutions in this chapter yourself. I once read a book about regular expression without actually doing it and I forgot almost everything I read afterwards. Active coding is the best way to master any skill.

A good way to improve your pattern matching skill is whenever you need to search for a pattern (like "hello 123"), instead of querying for the literal search term (`/hello 123`), try to come up with a pattern for it (`/\v(\l+) (\d+)`). Many of these regular expression concepts are also applicable in general programming, not only when using Vim.

Now that you learned about advanced search and substitution in Vim, let's learn one of the most versatile commands, the global command.
