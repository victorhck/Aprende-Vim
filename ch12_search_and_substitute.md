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

Si no quieres escribir `\|` cada vez, puedes utilizar la sintáxis `magic` (`\v`) al comienzo de la búsqueda: `/\vhello|hola`. No se va a tratar `magic` en este capítulo, pero con `\v`, ya no necesitará escapar caracteres especiales nunca más. Para aprender más sobre `\v`, echa un vistazo a la ayuda de Vim mediante `:h \v`.

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

## Searching for Repeating Characters

If you need to search for double digits in this text:

```
1aa
11a
111
```

You can use `/[0-9][0-9]` to match a two-digit character, but this method is unscalable. What if you need to match twenty digits? Typing `[0-9]` twenty times is not a fun experience. That's why you need a `count` argument.

You can pass `count` to your search. It has the following syntax:

```
{n,m}
```

By the way, these `count` braces need to be escaped when you use them in Vim. The `count` operator is placed after a single character you want to increment.

Here are the four different variations of the `count` syntax:
- `{n}` is an exact match. `/[0-9]\{2\}`  matches the two digit numbers: "11" and the "11" in "111".
- `{n,m}` is a range match. `/[0-9]\{2,3\}` matches between 2 and 3 digit numbers: "11"  and "111".
- `{,m}` is an up-to match. `/[0-9]\{,3\}` matches up to 3 digit numbers: "1", "11", and "111".
- `{n,}` is an at-least match. `/[0-9]\{2,\}` matches at least a 2 or more digit numbers: "11" and "111".

The count arguments `\{0,\}` (zero or more) and `\{1,\}` (one or more) are common search patterns and Vim has special operators for them: `*` and `+` (`+` needs to be escaped while `*` works fine without the escape). If you do `/[0-9]*`, it is the same as `/[0-9]\{0,\}`. It searches for zero or more digits. It will match "", "1", "123". By the way, it will also match non-digits like "a", because there is technically zero digit in the letter "a". Think carefully before using `*`. If you do `/[0-9]\+`, it is the same as `/[0-9]\{1,\}`. It searches for one or more digits. It will match "1" and "12".

## Predefined Ranges

Vim has predefined ranges for common characters like digits and alphas. I will not go through every single one here, but you can find the full list inside `:h /character-classes`. Here are the useful ones:

```
\d    Digit [0-9]
\D    Non-digit [^0-9]
\s    Whitespace character (space and tab)
\S    Non-whitespace character (everything except space and tab)
\w    Word character [0-9A-Za-z_]
\l    Lowercase alphas [a-z]
\u    Uppercase character [A-Z]
```

You can use them like you would use character ranges. To search for any single digit, instead of using `/[0-9]`, you can use `/\d` for a more concise syntax.

## More Search Examples
### Capturing a Text Between a Pair of Similar Characters

If you want to search for a phrase surrounded by a pair of double quotes:

```
"Vim is awesome!"
```

Run this:


`/"[^"]\+"`


Let's break it down:
- `"` is a literal double quote. It matches the first double quote.
- `[^"]` means any character except for a double quote. It matches any alphanumeric and whitespace character as long as it is not a double quote. 
- `\+` means one or more. Since it is preceded by `[^"]`, Vim looks for one or more character that is not a double quote.
- `"` is a literal double quote. IT matches the closing double quote.

When sees the first `"`, it begins the pattern capture. The moment Vim sees the second double quote in a line, it matches the second `"` pattern and stops the pattern capture. Meanwhile, all non-`"` characters between the two `"` are captured by the `[^"]\+` pattern, in this case, the phrase `Vim is awesome!`. This is a common pattern to capture a phrase surrounded by a pair of similar delimiters: to capture a phrase surrounded by a single quote, you can use `/'[^']\+'`. 

### Capturing a Phone Number

If you want to match a US phone number separated by a hyphen (`-`), like `123-456-7890`, you can use:
```
/\v\d\{3\}-\d\{3\}-\d\{4\}
```

US Phone number consists of a set of three digit number, followed by another three digits, and finally by four digits. Let's break it down:
- `\d\{3\}` matches a digit repeated exactly three times
- `-` is a literal hyphen

This pattern is also useful to capture any repeating digits, such as IP addresses and zip codes. 

That covers the search part of this chapter. Now let's move to substitution.

## Basic Substitution

Vim's substitute command is a useful command to quickly find and replace any pattern. The substitution syntax is:

```
:s/old-pattern/new-pattern/
```

Let's start with a basic usage. If you have this text:

```
vim is good
```

Let's substitute "good" with "awesome" because Vim is awesome. Run `:s/good/awesome/.` You should see:

```
vim is awesome
```

## Repeating the Last Substitution

You can repeat the last substitute command with either the normal command `&` or by running `:s`. If you have just run `:s/good/awesome/`, running either `&` or `:s` will repeat it. 

Also, earlier in this chapter I mentioned that you can use `//` to repeat the previous search pattern.  This trick works with the substitution command. If `/good` was done recently and you leave the first substitute pattern argument blank, like in `:s//awesome/`, it is the same as running `:s/good/awesome/`.

## Substitution Range

Just like many Ex commands, you can pass a range argument into the substitute command. The syntax is:

```
:[range]s/old/new/
```

If you have these expressions:

```
let one = 1;
let two = 2;
let three = 3;
let four = 4;
let five = 5;
```

To substitute the "let" into "const" on lines three to five, you can do:

```
:3,5s/let/const/
```

The substitute command's range syntax is similar to the count syntax in search (`{n,m}`), with minor differences. Here are some variations to pass the range:

- `:,3/let/const/` - if nothing is given before the comma, it represents the current line. Substitute from current line to line 3.
- `:1,s/let/const/` - if nothing is given after the comma, it also represents the current line. Substitute from line 1 to current line.
- `:3s/let/const/` - if only one value is given as range (no comma), it does substitution on that line only.

In Vim, `%` usually means the entire file. If you run `:%s/let/const/`, it will do substitution on all lines.

## Pattern Matching

The next few sections will cover basic regular expressions. A strong pattern knowledge is essential to master the substitute command.

If you have the following expressions:

```
let one = 1;
let two = 2;
let three = 3;
let four = 4;
let five = 5;
```

To add a pair of double quotes around the digits:

```
:%s/\d/"\0"/
```

The result:
```
let one = "1";
let two = "2";
let three = "3";
let four = "4";
let five = "5";
```

Let's break down the command:
- `:%s` targets the entire file to perform substitution.
- `\d` is Vim's predefined range for digits (`[0-9]`). 
- `"\0"` the double quotes are literal double quotes. `\0` is a special character representing "the whole matched pattern". The matched pattern here is a single digit number, `\d`. On line one, `\0` has the value of "1". On line two, value of "2". On line three, value of "3", and so on.

Alternatively, `&` also represents "the whole matched pattern" like `\0`. `:s/\d/"&"/` would have also worked.

Let's consider another example. Given these expressions:
```
one let = "1";
two let = "2";
three let = "3";
four let = "4";
five let = "5";
```
You need to swap all the "let" with the variable names. To do that, run:

```
:%s/\(\w\+\) \(\w\+\)/\2 \1/
```

The command above contains too many backslashes and is hard to read. It is more convenient to use the `\v` operator:

```
:%s/\v(\w+) (\w+)/\2 \1/
```

The result:
```
let one = "1";
let two = "2";
let three = "3";
let four = "4";
let five = "5";
```

Great! Let's break down that command:
- `:%s` targets all the lines in the file
- `(\w+) (\w+) `groups the patterns. `\w` is one of Vim's predefined ranges for a word character (`[0-9A-Za-z_]`). The `( )` surrounding it captures a word character match in a group. Notice the space between the two groupings. `(\w+) (\w+)` captures in two groups. On the first line, the first group captures "one" and the second group captures "two".
- `\2 \1` returns the captured group in a reversed order. `\2` contains the captured string "let" and `\1` the string "one". Having `\2 \1` returns the string "let one".

Recall that `\0` represents the entire matched pattern. You can break the matched string into smaller groups with `( )`. Each group is represented by `\1`, `\2`, `\3`, etc.

Let's do one more example to solidify this matched group concept. If you have these numbers:

```
123
456
789
```

To reverse the order, run:
```
:%s/\v(\d)(\d)(\d)/\3\2\1/
```

The result is:

```
321
654
987
```

Each `(\d)` matches and groups each digit. On the first line, the first `(\d)` has a value of "1", the second `(\d)` "2", and the third `(\d)` "3". They are stored in the variables `\1`, `\2`, and `\3`. In the second half of your substitution, the new pattern `\3\2\1` results in the "321" value on line one.

If you had run this instead:
```
:%s/\v(\d\d)(\d)/\2\1/
```
You would have gotten a different result:
```
312
645
978
```

This is because you now only have two groups. The first group,captured by `(\d\d)`, is stored within `\1` and has the value of "12". The second group, captured by `(\d)`, is stored inside `\2` and has the value of "3". `\2\1` then, returns "312".

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
