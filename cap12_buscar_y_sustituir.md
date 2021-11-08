# Capítulo 12: Buscar y sustituir

Este capítulo trata sobre dos conceptos separados pero relacionados: buscar y sustituir. A menudo cuando estás editando con Vim, necesitas buscar múltiples textos basando esas búsquedas en un patrón que sea su mínimo denominador común. Aprender cómo utilizar expresiones regulares en la búsqueda y sustitución en vez de búsquedas de cadenas literales, hará que seas capaz de localizar cualquier texto rápidamente.

Como nota complementaria, en este capítulo, utilizaré principalmente `/` cuando me refiera a la búsqueda. Todo lo que puedes realizar con el comando `/` puede también ser realizado con `?`. Con `/` realiza la búsqueda desde la posición del cursor hacia adelante en el texto, mientras que `?` realiza la búsqueda desde la posición del cursor hacia atrás.

## Sensibilidad inteligente a mayúsculas y minúsculas

Puede ser complicado hacer coincidir las mayúsculas y minúsculas en el término buscado. Si estás buscando el texto "Aprende Vim", te puedes confundir fácilmente al escribir una letra y obtener unos resultados de la búsqueda falsos. ¿No sería más sencillo y fácil si pudieras hacer coincidir tanto si está en mayúsculas o minúsculas? Aquí es donde la opción `ignorecase` es más útil. Simplemente añade `set ignorecase` en tu archivo de configuración vimrc y todos tus términos de búsqueda no tendrán en cuenta las mayúsculas o minúsculas. Ahora ya no será necesario que escribas `/Learn Vim` nunca mas. `/learn vim` será más que necesario para realizar la misma búsqueda.

Sin embargo, hay ocasiones en las que necesitas buscar una frase específica con diferenciación entre mayúsculas o minúsculas. Una forma de hacer eso es inhabilitando la opción `ignorecase` mediante `set noignorecase`, pero es muy laborioso el inhabilitar y volver a habilitar la opción cada vez que necesites una u otra forma de realizar búsquedas.

Para evitar estar alternando la opción `ignorecase`, Vim tiene la opción `smartcase` para buscar cadenas de texto sin importar las mayúsculas o minúsculas si el patrón de búsqueda _contiene al menos una carácter en mayúscula_. Puedes combinar tanto `ignorecase` y `smartcase` para realizar búsquedas que no tengan en cuenta las mayúsculas cuando introduzcas caracteres en minúsculas y tenga en cuenta las mayúsculas y minúsculas cuando introduzcas una o más letras en mayúsculas.

Dentro de tu archivo vimrc, añade:

```text
set ignorecase smartcase
```

Si tienes estos textos:

```text
hola
HOLA
Hola
```

* `/hola` encontrará los términos "hola", "HOLA" y "Hola".
* `/HOLA` encontrará solo "HOLA".
* `/Hola` encontrará solo "Hola".

Solo hay una pega. ¿Qué pasa cuando necesitas solo encontrar la cadena de texto en minúscula? Cuando buscas `/hola`, Vim siempre encontrará sus variantes con mayúsculas. Puedes utilizar el patrón `\C` en cualquier parte en tu término de búsqueda para decirle a Vim que en el término de búsqueda tenga en cuenta las minúsculas. Ahora si ejecutas `/\Chola`, restringirá las coincidencias únicamente a "hola" y no aparecerán ni "HOLA" ni "Hola".

## El primer y último carácter en una línea

Puedes utilizar `^` para encontrar el primer carácter en una línea y `$` para encontrar el último carácter en una línea.

Si tienes este texto:

```text
hola hola
```

Puedes llegar al primer "hola" con `/^hola`. El carácter que siga a `^` deberá ser el primer carácter de la línea. Para buscar el último "hola", ejecuta `/hola$`. El carácter anterior a `$` deberá se el último carácter de la línea.

Si tienes el siguiente texto:

```text
hola hola amigo
```

Al ejecutar `/hola$` no encontrará nada porque "amigo" es la última palabra en esa línea, no "hola".

## Repetir la búsqueda

Puedes repetir la búsqueda con `//`. Si has buscado `/hola`, al ejecutar `//` será equivalente a ejecutar `/hola`. Este atajo de teclado te puede ahorrar varias pulsaciones de teclas, especialmente si anteriormente hiciste una búsqueda de un término bastante largo. También recordar que puedes utilizar `n` y `N` para repetir la última búsqueda en la misma dirección o en la dirección contraria, respectivamente.

¿Qué pasa si quieres volver a ejecutar rápidamente la búsqueda anterior _n_? Puedes navegar rápidamente por el historial de búsquedas pulsando primero la tecla `/`, y después las teclas `arriba`/`abajo` \(o `Ctrl-N`/`Ctrl-P`\) hasta que encuentres el término de búsqueda que necesitas. Para ver todo el historial de búsquedas, puedes ejecutar `:history /`.

Cuando llegues al final de un archivo mientras estás buscando, Vim te mostrará un error: `"Search hit the BOTTOM without match for: <your-search>"`. A veces esto puede ser una buena protección para no hacer una búsqueda sin fin, pero otras veces puedes necesitar que el ciclo de búsqueda vuelva a empezar desde la parte superior de nuevo. Puedes utilizar la opción `set wrapscan` para hacer que Vim vuelva a buscar desde el inicio de un archivo cuando llega al final de un archivo. Para inhabilitar esta opción simplemente ejecuta `set nowrapscan`.

## Buscando palabras alternativas

Es común buscar múltiples palabras a la vez. Si necesitas buscar _tanto_ "hello vim" u "hola vim", pero no "salve vim" o "bonjour vim", puedes utilizar el símbolo `|`.

Dado este texto:

```text
hello vim
hola vim
salve vim
bonjour vim
```

Para encontrar tanto "hello" como "hola", puedes ejecutar `/hello\|hola`. Es necesario _escapar_, es decir, añadir una barra invertida \(`\`\) antes del operador de la tubería \(`|`\), de lo contrario Vim literalmente buscará la cadena "\|".

Si no quieres escribir `\|` cada vez, puedes utilizar la sintaxis `magic` \(`\v`\) al comienzo de la búsqueda: `/\vhello|hola`. No se va a tratar `magic` en esta guía, pero con `\v`, ya no necesitará escapar caracteres especiales nunca más. Para aprender más sobre `\v`, echa un vistazo a la ayuda de Vim mediante `:h \v`.

## Estableciendo el inicio y el final de una búsqueda

Quizás necesites buscar un texto que es parte de una palabra compuesta. Si tienes estos textos:

```text
11vim22
vim22
11vim
vim
```

Si necesitas seleccionar "vim" pero solo cuando esta comienza con "11" y termina con "22", puedes utilizar los comandos `\zs` \(coincidencia de comienzo\) y `\ze` \(coincidencia final\). Ejecuta:

```text
/11\zsvim\ze22
```

Vim buscará el patrón entero "11vim22", pero solo resaltará el patrón que está entre los comandos `\zs` y `\ze`. Otro ejemplo:

```text
foobar
foobaz
```

Si necesitar buscar "foo" en "foobaz" pero no en "foobar", ejecuta:

```text
/foo\zebaz
```

## Buscando un rango de caracteres

Todos tus términos de búsqueda hasta este punto han sido una búsqueda literal de una palabra. En la vida real, puede que tengas que utilizar un patrón general para encontrar tu texto. El patrón básico es el rango de caracteres, `[ ]`.

Si necesitas buscar cualquier dígito, lo más probable es que no quieras escribir `/0\|1\|2\|3\|4\|5\|6\|7\|8\|9\|0` cada vez que lo necesites. En vez de eso, utiliza `/[0-9]` para encontrar cualquier dígito entre ese rango. La expresión `0-9` representa un rangos de números entre 0-9 que Vim intentará encontrar, así que si estás buscando dígitos entre 1 y 5, en ese caso utilizaríamos `/[1-5]`.

Los dígitos no son el único tipo de datos que Vim puede buscar. También puedes buscar entre `/[a-z]` para buscar letras en minúsculas y `/[A-Z]` para letras en mayúsculas.

También puedes combinar estos rangos juntos. Si necesitas buscar dígitos entre 0-9 y a la vez letras en mayúsculas y minúsculas de la "a" a la "f" \(una cifra en hexadecimal\), puedes ejecutar `/[0-9a-fA-F]`.

Para realizar una búsqueda inversa, puedes añadir `^` dentro de los corchetes dentro del rango. Para buscar algo que no sea un dígito, ejecuta `/[^0-9]`. Vim encontrará cualquier carácter mientras que no sea un dígito. Ten en cuenta que el carácter \(`^`\) dentro de los corchetes del rango es diferente del carácter al principio de una línea \(ejemplo: `/^hello`\). Si el carácter está fuera del par de corchetes y es el primer carácter en el término de búsqueda, esto significa "el primer carácter en una línea". Si el carácter está dentro de un par de corchetes y es el primer carácter dentro de los corchetes, esto significa un operador de búsqueda negativo. `/^abc` encontrará el primer "abc" en una línea y `/[^abc]` encontrará cualquier carácter excepto una "a", "b" o "c".

## Buscando caracteres repetidos

Si necesitas buscar dígitos dobles en este texto:

```text
1aa
11a
111
```

Puedes utilizar `/[0-9][0-9]` para encontrar una pareja de dos dígitos, pero este método no es escalable. ¿Qué pasa si necesitas encontrar veinte dígitos? Escribir `[0-9]` veinte veces no es una solución muy elegante. Por eso necesitas el argumento `count`.

Puedes pasarle el argumento `count` a tu búsqueda. Tiene la siguiente sintaxis:

```text
{n,m}
```

Por cierto, estas llaves de `count` necesitan ser _escapadas_ cuando las utilizas en Vim. El operador `count` es ubicado después del carácter que quieres incrementar.

Aquí tienes cuatro variaciones diferente de la sintaxis de `count`:

* `{n}` es una búsqueda exacta. `/[0-9]\{2\}`  encontrará números de dos dígitos: "11" y el "11" en "111".
* `{n,m}` es un rango de búsqueda. `/[0-9]\{2,3\}` encontrará números con 2 y 3 dígitos: "11" y "111".
* `{,m}` es un límite superior de búsqueda. `/[0-9]\{,3\}` encontrará números hasta de 3 dígitos: "1", "11" y "111".
* `{n,}` es una búsqueda con límite al menos _n_ elementos. `/[0-9]\{2,\}` encontrará números con al menos 2 o más dígitos: "11" y "111".

Los argumentos de contaje `\{0,\}` \(cero o más\) y `\{1,\}` \(uno o más\) son patrones de búsqueda comunes y Vim tiene operadores especiales para estos casos: `*` y `+` \(`+` necesita ser _escapado_ mientras que `*` funciona bien sin necesidad de añadir símbolos de escape\). Si ejecutas `/[0-9]*`, esto es lo mismo que ejecutar `/[0-9]\{0,\}`. Buscará cero o más dígitos. Esto encontrará "", "1", "123". Por cierto, también encontrará caracteres que no sean dígitos como "a", porque técnicamente hay un dígito cero en la letra "a". Piénsalo detenidamente antes de utilizar `*`. Si ejecutas `/[0-9]\+`, es lo mismo que ejecutar `/[0-9]\{1,\}`. Esto buscará uno o más dígitos. Encontrará "1" y "12".

## Rangos predefinidos

Vim tiene unos rangos predefinidos para los caracteres más comunes como dígitos o letras. No vamos a entrar en detalle de todos, pero si lo necesitas puedes encontrar la lista ejecutando: `:h /character-classes`. Aquí tienes los más útiles:

```text
\d    Dígitos [0-9]
\D    No digitos [^0-9]
\s    Carácter de espacio en blanco (espacio y tabulador)
\S    Carácter que no sea espacio en blanco (todo excepto un espacio o tabulador)
\w    Carácter de palabra [0-9A-Za-z_]
\l    Letras minúsculas [a-z]
\u    Letras mayúsculas [A-Z]
```

Los puedes utilizar como utilizarías un rango de caracteres. Para buscar un único dígito, en vez de utilizar `/[0-9]`, puedes utilizar `/\d` para una sintaxis más concisa.

## Ejemplo de búsqueda: Encontrar un texto entre un par de caracteres similares

Si quieres buscar una frase rodeada por un par de comillas dobles:

```text
"¡Vim es asombroso!"
```

Ejecuta esto:

```text
/"[^"]\+"
```

Vamos a diseccionar el comando:

* `"` es una doble comilla literal. Encontrará la primera comilla doble.
* `[^"]` significa cualquier carácter excepto una comilla doble. Encontrará cualquier carácter alfanumérico y espacios en blanco mientras que no sea una comilla doble.
* `\+` significa uno o más. Como está precedido por `[^"]`, Vim busca uno o más caracteres que no sean una comilla doble.
* `"` es una doble comilla literal. Encontrará las comillas dobles de cierre.

Cuando ve el primer `"`, comenzará el patrón de captura. En el momento que Vim ve las segundas comillas dobles en una línea, encuentra el segundo patrón de `"` y detiene el patrón de captura. Mientras, todos los caracteres que no sean `"` entre las dos comillas `"` son capturados por el patrón `[^"]\+`, en este caso, la frase `¡Vim es asombroso!`. Este es un patrón común a la hora de capturar una frase encerrada entre un par de delimitadores similares.

* Para capturar una frase entre dos comillas simples, puedes utilizar  `/'[^']\+'`. 
* Para capturar una frase entre ceros, puedes utilizar `/0[^0]\+0`. 

## Ejemplo de búsqueda: Encontrando un número de teléfono

Si quieres encontrar un número de teléfono de EE.UU. separados por guiones \(`-`\), como `123-456-7890`, puedes utilizar:

```text
/\d\{3\}-\d\{3\}-\d\{4\}
```

Los números de teléfono de EE.UU. están formados por un conjunto de tres números, seguidos por otros tres números y finalmente cuatro dígitos. Vamos a ver en detalle el comando:

* `\d\{3\}` encontrará un dígito repetido exactamente tres veces
* `-` es literalmente un guión

Puedes evitar tener que escribir los signos de _escapado_ de caracteres especiales utilizando `\v`:

```text
/\v\d{3}-\d{3}-\d{4}
```

Este patrón es también útil para capturar dígitos repetidos, como direcciones IP o códigos postales.

Con esto finaliza la parte de búsquedas de este capítulo. Ahora veamos las sustituciones.

## Sustitución básica

El comando de sustitución de Vim es útil para rápidamente encontrar y reemplazar cualquier patrón. La sintaxis de una sustitución básica es:

```text
:s/{patrón_antiguo}/{patrón_nuevo}/
```

Vamos a empezar con un uso básico. Si tenemos el siguiente texto:

```text
vim es bueno
```

Vamos a sustituir "bueno" con "increíble" porque Vim es realmente increíble. Ejecuta `:s/bueno/increíble/.` Con lo que quedaría de esta forma:

```text
vim es increíble
```

## Repetir la última sustitución

Puedes repetir el último comando de sustitución ya sea con el comando normal `&` o ejecutando `:s`. Si acabas de ejecutar `:s/bueno/increíble/`, tanto ejecutando `&` o `:s` repetirá la misma sustitución.

También, previamente en este mismo capítulo mencioné que puedes utilizar `//` para repetir el patrón de búsqueda previo. Este truco funciona con el comando de sustitución. Si `/bueno` fue ejecutado recientemente y dejas el primer argumento del patrón de sustitución en blanco, de esta manera `:s//increíble/`, es lo mismo que ejecutar lo siguiente `:s/bueno/increíble/`.

## Rango de sustituciones

Igual que en muchos comandos Ex, puedes pasarle un rango como argumento al comando de sustitución. La sintaxis es:

```text
:[rango]s/antiguo/nuevo/
```

Si tienes estas expresiones:

```text
let uno = 1;
let dos = 2;
let tres = 3;
let cuatro = 4;
let cinco = 5;
```

Para sustituir "let" por "const" de la línea tres a la cinco, deberás ejecutar:

```text
:3,5s/let/const/
```

Aquí tienes algunas variaciones para pasar el rango:

* `:,3/let/const/` - si no se da ningún parámetro antes de la coma, representa la línea actual. Sustituye desde la línea actual hasta la línea 3.
* `:1,s/let/const/` - si no se da ningún dato después de la coma, también representa la línea actual. Sustituye desde la línea 1 hasta la línea actual.
* `:3s/let/const/` - si solo se da un valor como rango \(sin coma\), realiza la sustitución únicamente en esa línea.

En Vim, `%` normalmente se refiere al archivo entero. Si ejecutas `:%s/let/const/`, esto realizará la sustitución en todas las líneas del archivo. Ten en mente la sintaxis de este rango. Muchos comandos para la línea de comandos que aprenderás en capítulos siguientes seguirán esta misma forma.

## Encontrando un patrón

En las siguientes secciones vamos a tratar de ver algunas de las expresiones regulares básicas. El conocimiento de un patrón robusto es esencial para dominar el comando de sustitución.

Imaginemos que tenemos las siguientes expresiones:

```text
let uno = 1;
let dos = 2;
let tres = 3;
let cuatro = 4;
let cinco = 5;
```

Y queremos añadir un par de comillas dobles al comienzo y final de cada número:

```text
:%s/\d/"\0"/
```

Este es el resultado:

```text
let uno = "1";
let dos = "2";
let tres = "3";
let cuatro = "4";
let cinco = "5";
```

Vamos a ver el comando en detalle:

* `:%s` esto busca en el archivo entero para realizar una acción de sustitución.
* `\d` es el rango predefinido en Vim para los dígitos, similar a \(`[0-9]`\). 
* `"\0"` las comillas dobles son literalmente comillas dobles. `\0` es un carácter especial que representa "el patrón de encontrado completo". El patrón de búsqueda aquí es un número de un solo dígito, `\d`.

De igual manera, el símbolo `&` también representa "el patrón encontrado completo" igual que `\0`. Por lo que `:s/\d/"&"/` también hubiera funcionado.

Vamos a considerar otro ejemplo. Dadas estas expresiones, necesitamos intercambiar todos los "let" con los nombres de las variables.

```text
uno let = "1";
dos let = "2";
tres let = "3";
cuatro let = "4";
cinco let = "5";
```

Para hacer eso, ejecuta:

```text
:%s/\(\w\+\) \(\w\+\)/\2 \1/
```

El comando anterior contiene demasiadas barras invertidas y es difícil de leer. Es más conveniente utilizar el operador `\v`:

```text
:%s/\v(\w+) (\w+)/\2 \1/
```

Este es el resultado:

```text
let uno = "1";
let dos = "2";
let tres = "3";
let cuatro = "4";
let cinco = "5";
```

¡Genial! Veamos el comando en detalle:

* `:%s` busca en todas las líneas del archivo para realizar la sustitución.
* `(\w+) (\w+)` agrupa los patrones. `\w` es uno de los rangos predefinidos de Vim para un carácter de una palabra \(`[0-9A-Za-z_]`\). Los paréntesis `( )` rodeando captura un carácter de palabra coincidente en un grupo. Ten en cuenta el espacio entre los dos agrupamientos. `(\w+) (\w+)` captura en dos grupos. En la primera línea, el primer grupo captura el "uno" y el segundo grupo captura el "dos".
* `\2 \1` devuelve el grupo capturado en orden inverso. `\2` contiene la cadena "let" y `\1` la cadena "uno". Al tener `\2 \1` esto devuelve la cadena "let uno".

Recordemos que `\0` representa el patrón encontrado al completo. Puedes romper la cadena encontrada en grupos más pequeños con `( )`. Cada grupo es representado por `\1`, `\2`, `\3`, etc.

Vamos a ver un ejemplo más para tratar de clarificar más el concepto de grupo encontrado. Si tienes los siguiente números:

```text
123
456
789
```

Para invertir el orden, ejecuta:

```text
:%s/\v(\d)(\d)(\d)/\3\2\1/
```

El resultado es:

```text
321
654
987
```

Cada `(\d)` encuentra y agrupa cada dígito. En la primera línea, el primer `(\d)` tiene el valor "1", el segundo `(\d)` "2" y el tercer `(\d)` "3". Son almacenados en las variables `\1`, `\2` y `\3`. En la segunda mitad de tu sustitución, el nuevo patrón `\3\2\1` resultará en el valor "321" en la primera línea.

Si hubieras ejecutado este comando:

```text
:%s/\v(\d\d)(\d)/\2\1/
```

Hubieras obtenido un resultado diferente:

```text
312
645
978
```

Esto es debido a que ahora solo tienes dos grupos. El primer grupo, capturado por `(\d\d)`, es almacenado en `\1` y tiene el valor 12. El segundo grupo, capturado por `(\d)`, es almacenado dentro de `\2` y tiene el valor 3. `\2\1` entonces, esto devolverá 312.

## Las opciones de sustitución

Si tienes la siguiente frase:

```text
tarta de chocolate, tarta de fresa, tarta de frambuesa
```

Para sustituir todas las palabras "tarta" por "donut", no sirve ejecutar:

```text
:s/tarta/donut
```

El comando anterior, solo sustituirá la primera coincidencia que encuentre, por lo que tendrás:

```text
donut de chocolate, tarta de fresa, tarta de frambuesa
```

Hay dos maneras de solucionar esto. Puedes ejecutar el comando de sustitución un par de veces más o puedes ejecutar el comando de sustitución añadiendo la opción de sustitución global \(`g`\) para que realice el comando en todas las coincidencias que encuentre en la línea.

Veamos la opción de global. Ejecuta:

```text
:s/tarta/donut/g
```

Vim sustituirá todas las "tarta" por "donut" en un único comando. El comando global es una de las muchas opciones que admite el comando de sustitución. Le pasas esas opciones el final del comando de sustitución. Aquí tienes una lista de las opciones más útiles:

```text
&    Reutiliza las opciones del comando previo de sustitución. Debe pasarse como primera opción. Must be passed as the first flag.
g    Reemplaza todas las ocurrencias encontradas en la línea actual.
c    Pregunta una confirmación en cada sustitución.
e    Evita que se muestre un mensaje de error cuando falla una sustitución.
i    Realiza una sustitución sin tener en cuenta mayúsculas y minúsculas.
I    Realiza una sustitución teniendo en cuenta mayúsculas y minúsculas.
```

Existen más opciones que las que están mostradas en este listado. Para leer sobre estas opciones, llamadas _flags_ en Vim, echa un vistazo a la ayuda: `:h s_flags`.

Por cierto, el comando de repetir una sustitución \(`&` y `:s`\) no mantiene las opciones ejecutadas anteriormente. Al ejecutar `&` esto solo repetirá `:s/tarta/donut/` sin `g`. Para repetir rápidamente el último comando de sustitución con todas sus opciones, deberás ejecutar `:&&`.

## Cambiando el delimitador

Si necesitas reemplazar una URL con una ruta larga, algo similar a:

```text
https://mysite.com/a/b/c/d/e
```

Para sustituirla por la palabra "hola", ejecuta:

```text
:s/https:\/\/mysite.com\/a\/b\/c\/d\/e/hola/
```

Sin embargo, es difícil discernir qué barras \(`/`\) son parte del patrón de sustitución y cuales son delimitadores. Puedes cambiar el delimitador por cualquier carácter de un único byte \(excepto por letras, números o `"`, `|`, y `\`\). Vamos a reemplazarlo por `+`. El comando de sustitución anterior puede ser reescrito como:

```text
:s+https:\/\/mysite.com\/a\/b\/c\/d\/e+hola+
```

Ahora es más sencillo ver donde están los delimitadores.

## Reemplazo especial

También puedes modificar las mayúsculas o minúsculas del texto que está sustituyendo. Dadas las siguientes expresiones, tu tare es convertir en mayúsculas las variables "uno", "dos", "tres", etc.

```text
let uno = "1";
let dos = "2";
let tres = "3";
let cuatro = "4";
let cinco = "5";
```

Ejecuta:

```text
%s/\v(\w+) (\w+)/\1 \U\2/
```

Y obtendrás:

```text
let UNO = "1";
let DOS = "2";
let TRES = "3";
let CUATRO = "4";
let CINCO = "5";
```

Veamos el comando en detalle:

* `(\w+) (\w+)` captura el primero de los dos grupos encontrado, que son "let" y "uno".
* `\1` devuelve el valor del primer grupo: "let".
* `\U\2` cambia a mayúsculas \(`\U`\) el segundo grupo \(`\2`\).

El truco de este comando reside en la expresión `\U\2`. `\U` hace que el siguiente carácter sea convertido a mayúsculas.

Vamos a ver un ejemplo más. Supongamos que estás escribiendo una guía sobre Vim y necesitas convertir en mayúsculas la primera letra de cada palabra de una línea.

```text
vim es el mejor editor de texto en toda la galaxia
```

Puedes ejecutar:

```text
:s/\<./\u&/g
```

El resultado:

```text
Vim Es El Mejor Editor De Texto En Toda La Galaxia
```

Veamos el comando en detalle:

* `:s` realiza la sustitución en la línea actual.
* `\<.` está formado por dos partes: `\<` para encontrar el comienzo de cada palabra y `.` para encontrar cualquier carácter. El operador `\<` hace que el siguiente carácter sea el primer carácter de una palabra. Como `.` es el siguiente carácter, esto encontrará el primer carácter de cualquier palabra.
* `\u&`  convierte en mayúsculas el símbolo `&`. Recordemos que `&` \(o `\0`\) representa la coincidencia entera. Encontrará el primer carácter de cualquier palabra.
* `g` la opción o _flag_ global. Sin este, este comando solo sustituirá la primera coincidencia encontrada. Y deberemos sustituir cada coincidencia encontrada en esta línea.

Para aprender más sobre los símbolos especiales de sustitución como `\u` y `\U`, echa un vistazo a la ayuda de Vim `:h sub-replace-special`.

## Patrones alternativos

A veces necesitas encontrar múltiples patrones de manera simultánea. Si tienes los siguientes saludos:

```text
hello vim
hola vim
salve vim
bonjour vim
```

Y necesitas sustituir la palabra "vim" por "amigo" pero solo en las líneas que contengan las palabras "hello" u "hola". Recuerda que en este capítulo vimos que se podía utilizar `|` para encadenar múltiples patrones.

```text
:%s/\v(hello|hola) vim)/\1 amigo/g
```

El resultado:

```text
hello amigo
hola amigo
salve vim
bonjour vim
```

Esta es la explicación del comando:

* `%s` ejecuta el comando de sustitución en cada línea de un archivo.
* `(hello|hola)` encuentra _tanto_ "hello" u "hola" y lo considera como un grupo.
* `vim` es literalmente la palabra "vim".
* `\1` es el primer grupo encontrado, que es tanto "hello" u "hola".
* `amigo` es literalmente la palabra "amigo".

## Sustituir el comienzo y el final de un patrón

Recordemos que puedes utilizar `\zs` y `\ze` para definir el comienzo y el final de una coincidencia. Esta técnica también funciona en la sustitución. Si tienes este texto:

```text
chocolate pancake
strawberry sweetcake
blueberry hotcake
```

Para sustituir el texto "cake" dentro de "hotcake" con "dog" para obtener "hotdog":

```text
:%s/hot\zscake/dog/g
```

Este sería el resultado:

```text
chocolate pancake
strawberry sweetcake
blueberry hotdog
```

También puedes sustituir la coincidencia número _x_ en una línea con este truco:

```text
Uno Mississippi, dos Mississippi, tres Mississippi, cuatro Mississippi, cinco Mississippi.
```

Para sustituir el tercer "Mississippi" con "Arkansas", ejecuta:

```text
:s/\v(.{-}\zsMississippi){3}/Arkansas/g
```

Veamos en detalle el comando:

* `:s/` el comando de sustitución.
* `\v` es la clave mágica así no es necesario escapar palabras clave especiales.
* `.` encuentra cualquier único carácter.
* `{-}` realiza una coincidencia no excesiva de 0 o más del carácter anterior.
* `\zsMississippi` hace que "Mississippi" sea el comienzo de la coincidencia.
* `(...){3}` busca la tercera coincidencia de los parámetros anteriores.

Ya has visto antes la sintaxis de `{3}`. Es del tipo `{n,m}`. En este caso, tienes `{3}` que será exactamente la tercera coincidencia. El único truco nuevo que hemos utilizado aquí es `{-}`. Es una coincidencia no excesiva. Encuentra la coincidencia más corta dada en el patrón. En este caso, `(.{-}Mississippi)` encuentra la mínima cantidad de "Mississippi" precedida por cualquier carácter. Esto contrasta con `(.*Mississippi)` que encuentra la coincidencia más larga del patrón dado.

Si utilizas `(.{-}Mississippi)`, esto encontrará cinco coincidencias: "Uno Mississippi", "dos Mississippi", etc. Si utilizas `(.*Mississippi)`, encontrarás una coincidencia: el último "Mississippi". Para aprender más, echa un vistazo `:h /\{-` y `:h non-greedy`.

Vamos a realizar un ejemplo más simple. Si tienes la siguiente cadena de caracteres:

```text
abc1de1
```

Puedes encontrar "abc1de1" \(coincidencia excesiva o _greedy_\) con:

```text
/a.*1
```

Puedes encontrar "abc1" \(coincidencia no excesiva o _non-greedy_\) con:

```text
/a.\{-}1
```

Así que si necesitas convertir a mayúsculas la coincidencia más larga \(greedy\), ejecuta:

```text
:s/a.*1/\U&/g
```

Para obtener:

```text
ABC1DEFG1
```

Si necesitas convertir a mayúsculas la coincidencia más corta \(non-greedy\), ejecuta:

```text
:s/a.\{-}1/\U&/g
```

Para obtener:

```text
ABC1defg1
```

## Sustituyendo en múltiples archivos

Finalmente veamos cómo sustituir frases a través de múltiples archivos. Para esta sección asumiremos que tenemos dos archivos: `food.txt` y `animal.txt`.

Dentro de `food.txt`:

```text
corndog
hotdog
chilidog
```

Dentro de `animal.txt`:

```text
large dog
medium dog
small dog
```

Asumimos que la estructura del directorio es la siguiente:

```text
- food.txt
- animal.txt
```

Primero, vamos a capturar tanto `food.txt` como `animal.txt` dentro de `:args`. Recordemos de capítulos anteriores que `:args` puede ser utilizado para crear una lista de nombres de archivos. Hay varias formas de realizar esto dentro de Vim, una de ellas es ejecutando esto dentro de Vim:

```text
:args *.txt                  captura todos los archivos txt en la ubicación actual
```

Para comprobarlo, cuando ejecutas `:args`, deberías ver:

```text
[food.txt] animal.txt
```

Ahora que todos los archivos necesarios están almacenados dentro de la lista de argumentos, puedes realizar una sustitución en múltiples archivos con el comando `:argdo`. Ejecuta:

```text
:argdo %s/dog/chicken/
```

Esto realiza una sustitución en todos los archivos que estén dentro de la lista `:args`. Finalmente, guarda los archivos modificados con:

```text
:argdo update
```

`:args` y `:argdo` son herramientas útiles para aplicar comandos de la línea de comandos de Vim a múltiples archivos. ¡Inténtalo con otros comandos!

## Sustituyendo a través de múltiples archivos con macros

De manera alternativa, también puedes ejecutar el comando de sustituir en múltiples archivos con macros. Vamos a empezar obteniendo los archivos relevantes en la lista de argumentos. Ejecuta:

```text
:args *.txt
qq
:%s/dog/chicken/g
:wnext
q
99@q
```

Veamos los pasos seguidos en detalle:

* `:args *.txt` añade todos los archivos de texto a la lista de `:args`.
* `qq` comienza la grabación de la macro en el registro "q".
* `:%s/dog/chicken/g` sustituye "dog" con "chicken" en todas las líneas del archivo actual.
* `:wnext` escribe \(guarda\) el archivo y después va al siguiente archivo de la lista `args`.
* `q` detiene la grabación de la macro.
* `99@q` ejecuta la macro noventa y nueve veces. Vim detendrá la ejecución de la macro después de encontrar el primer error, así que Vim no ejecutará la macro noventa y nueve veces.

## Aprendiendo a buscar y sustituir de la manera más inteligente

La habilidad para realizar búsquedas correctas es una habilidad necesaria cuando se está editando. Dominar las búsquedas te permite utilizar la flexibilidad de las expresiones regulares para buscar cualquier patrón en un archivo. Tómate tu tiempo para aprender estas técnicas. Incluso haz las búsquedas y sustituciones de este capítulo por ti mismo. Hace tiempo leí un libro sobre expresiones regulares sin realmente realizar ninguna y he olvidado casi por completo todo lo que leí. La lectura activa practicando es la mejor manera de dominar cualquier habilidad.

Una buena manera de mejorar tu habilidad con los patrones de búsqueda es, todo lo que necesites buscar con un patrón \(como "hello 123"\), en vez de buscarlo de manera literal el término completo \(`/hello 123`\), intenta realizarlo con un patrón \(`/\v(\l+) (\d+)`\). Muchos de estos conceptos de expresiones regulares también son aplicables a la programación en general, no solo cuando estás utilizando Vim.

Ahora que has aprendido sobre la búsqueda y sustitución avanzada en Vim, vamos a aprender uno de los comandos más versátiles, el comando global.

