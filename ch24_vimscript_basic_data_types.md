# Capítulo 24: Tipos de datos básicos de Vimscript 

En los próximos capítulos, aprenderás sobre Vimscript, el lenguaje de programación propio de Vim.

Cuando aprender un nuevo lenguaje, hay tres elementos básicos para buscar:
- Primitivos
- Medios de combinación
- Medios de abtracción

En este capítulo, aprenderáslos tipos de datos primitivos de Vim.

## Tipos de datos

Vim tiene 10 tipos de datos diferentes:
- Number (Números)
- Float (Flotante)
- String (Cadenas)
- List (Lista)
- Dictionary (Diccionario)
- Specia (Especial)
- Funcref
- Job (Trabajos)
- Channel (Canal)
- Blob

Veremos los seis primeros tipos de datos en este capítulo. En el capítulo 27 aprenderás sobre Funcref. Para aprender más sobre los tipos de datos, echa un vistazo a `:h variables`.

## Continuando con el modo Ex

Vim técnicamente no tiene un [REPL](https://es.wikipedia.org/wiki/REPL) propio, pero tiene un modo, el modo Ex que puede utilizarse de ese modo. Puedes entrar en el modo Ex con `Q` o `gQ`. El modo Ex es como un modo extendido del modo línea de comandos (es como escribir en el modo línea de comandos sin fin) Para salir del modo Ex, escribe `:visual`.

Puedes utilizar tanto `:echo` o `:echom` en este capítulo y los siguientes capítulos sobre Vimscript para continuar escribiendo código. Son similares a `console.log` en JS o `print` en Python. El comando `:echo` muestra las expresiones evaluadas que le das. El comando `:echom` hace lo mismo, pero además, este almacena el resultado en el historial de mensajes.

```viml
:echom "hola mensaje hecho"
```

Puedes ver el historial de mensajes con:

```
:messages
```

Para borrar el historial de mensajes, ejecuta:

```
:messages clear
```

## Number (Números)

Vim tiene 4 tipos diferentes de números: decimales, hexadecimales, binarios y octales. Por cierto cuando menciono tipos de datos de números, a menudo esto significa un dato de tipo entero. En esta guía, usaré los términos números y enteros de manera indistinta.

### Decimal

Deberías estar familiarizado con el sistema decimal. Vim acepta decimales positivos y negativos. 1, -1, 10, etc. En programación con Vimscript, probablemente usarás el tipo decimal la mayor parte del tiempo.

### Hexadecimal

El tipo hexadecimal comienzo con `0x` o `0X`. Puedes asociarlo con He**x**adecimal.

### Binario

El tipo binario comienza con `0b` o `0B`. Puedes asociarlo con : **B**inario.

### Octal

El tipo octal comienza con `0`, `0o` y `0O`. Puedes asociarlo con **O**ctal.

### Mostrando números

Si ejecutas el comando `echo` ya sea con números de tipo hexadecimal, binario, u octal, Vim automáticamente los convierte a decimales.

```viml
:echo 42
" devuelve 42

:echo 052
" devuelve 42

:echo 0b101010
" devuelve 42

:echo 0x2A
" devuelve 42
```

### Verdadero y falso

En Vim, un valor 0 es similar a falso y todos los valores que no son 0 son tomados como verdadero.

El siguiente ejemplo no mostrará nada:

```viml
:if 0
:  echo "Nope"
:endif
```

Sin embargo, esto sí lo hará:

```viml
:if 1
:  echo "Sí"
:endif
```

Cualquier otro valor que no sea 0 es tomado como verdadero, incluyendo los números negativos. 100 es verdadero. -1 es verdadero.

### Números aritméticos

Los números pueden ser utilizados para ejecutar expresiones aritméticas:

```viml
:echo 3 + 1
" devuelve 4

: echo 5 - 3
" devuelve 2

:echo 2 * 2
" devuelve 4

:echo 4 / 2
" devuelve 2
```

Cuando divides un número y la división produce un resto, Vim elimina ese resto.

```viml
:echo 5 / 2
" devuelve 2 en vez de 2.5
```

Para obtener un resultado más acertado, necesitas utilizar un número flotante.

## Float (Flotante)

Los números flotantes son números con decimales finales. Hay dos maneras de representar los números flotantes: utilizar la notación con un punto (como 31.4) o con exponente (3.14e01). De manera similar a los números, puedes tener signo positivo o negativo:

```viml
:echo +123.4
" devuelve 123.4

:echo -1.234e2
" devuelve -123.4

:echo 0.25
" devuelve 0.25

:echo 2.5e-1
" devuelve 0.25
```

Necesitas darle el número entero un punto y los dígitos finales. `25e-2` (sin punto) y `1234.` (tiene un punto, pero no tiene decimales detrás) son ambos números flotantes inválidos.

### Aritmética con números flotantes

Cuando realizas expresiones aritméticas entre un número y un número flotante, Vim fuerza el resultado a un número flotante.

```viml
:echo 5 / 2.0
" devuelve 2.5
```

Una operación aritmética entre dos numeros flotantes te devuelve otro número flotante.

```
:echo 1.0 + 1.0
" devuelve 2.0
```

## String (Cadenas)

Las *strings* o cadenas son caracteres rodeados ya sea con comillas dobles (`""`) o comillas simples (`''`). "Hola", "123" y '123.4' son ejemplos de cadenas.

### Concatenación de cadenas

Para concatenar una cadena o *string* en Vim, utiliza el operador `.`.

```viml
:echo "Hola" . " mundo"
" devuelve "Hola mundo"
```

### Operaciones aritméticas con cadenas

Cuando ejecutas operaciones aritméticas (`+ - * /`) con un número y una cadena, Vim fuerza la cadena a convertirse a número.

```viml
:echo "12 donuts" + 3
" devuelve 15
```

Cuando Vim ve "12 donuts", extrar el 12 de la cadena y lo convierte al número 12. Después realiza la suma, devolviendo 15. Para que funcione este forzado de cadena a número, los caracteres de los números necesitan ser *los primeros caracteres* de la cadena.

Lo siguiente no funcionará porque 12 no está al comienzo de la cadena:

```viml
:echo "donuts 12" + 3
" devuelve 3
```

Esto tampoco funcionará porque hay un espacio en blanco al comienzo de la cadena:

```viml
:echo " 12 donuts" + 3
" devuelve 3
```

Ese forzao funciona incluso con dos cadenas:

```
:echo "12 donuts" + "6 rosquillas"
" devuelve 18
```

Esto funciona con cualquier operador aritmético, no solo `+`:

```viml
:echo "12 donuts" * "5 cajas"
" devuelve 60

:echo "12 donuts" - 5
" devuelve 7

:echo "12 donuts" / "3 personas"
" devuelve 4
```

Un buen truco para forzar una conversión de cadena a número es añadir 0 o multiplicar por 1:

```viml
:echo "12" + 0
" devuelve 12

:echo "12" * 1
" devuelve 12
```

Cuando se realiza una operación aritmética de un flotante con una cadena, Vim lo trata como un entero, no como un número con coma flotante.

```
:echo "12.0 donuts" + 12
" devuelve 24, no 24.0
```

### Concatenación de número con una cadena

Puedes forzar un número en una cadena con el operador del punto (`.`):

```viml
:echo 12 . "donuts"
" devuelve "12donuts"
```

Este forzado solo funciona con tipo de datos de números, no flotantes. Esto no funcionará:

```
:echo 12.0 . "donuts"
" Esto no devolverá "12.0donuts" si no que lanzará un error
```

### Condicionales de cadenas

Recuerda que 0 es tratado como falso y todos los demás números son verdadero. Esto también se cumple cuando estamos utilizando cadenas como condicionales. This is also true when using string as conditionals.

En la siguiente declaración de un comando *if*, Vim fuerza "12donuts" en 12, lo que se considera verdadero:

```viml
:if "12donuts"
:  echo "Yum"
:endif
" devuelve "Yum"
```

Por otra parte, esto es tratado como falso:

```viml
:if "donuts12"
:  echo "Nope"
:endif
" no devuelve nada
```

Vim fuerza "donuts12" a 0, porque el primer caracter no es un número.

### Double vs Single quotes

Double quotes behave differently than single quotes. Single quotes display characters literally while double quotes accept special characters.

What are special characters? Check out the newline and double-quotes display:

```viml
:echo "hello\nworld"
" devuelve
" hello
" world

:echo "hello \"world\""
" devuelve "hello "world""
```

Compare that with single-quotes:

```
:echo 'hello\nworld'
" devuelve 'hello\nworld'

:echo 'hello \"world\"'
" devuelve 'hello \"world\"'
```

Special characters are special string characters that when escaped, behave differently. `\n` acts like a newline. `\"` behaves like a literal `"`. For a list of other special characters, check out `:h expr-quote`.

### String Procedures

Let's look at some built-in string procedures.

You can get the length of a string with `strlen()`.

```
:echo strlen("choco")
" devuelve 5
```

You can convert string to a number with `str2nr()`:

```
:echo str2nr("12donuts")
" devuelve 12

:echo str2nr("donuts12")
" devuelve 0
```

Similar to the string-to-number coercion earlier, if the number is not the first character, Vim won't catch it.

The good news is that Vim has a method that transforms a string to a float, `str2float()`:

```
:echo str2float("12.5donuts")
" devuelve 12.5
```

You can substitute a pattern in a string with the `substitute()` method:

```
:echo substitute("sweet", "e", "o", "g")
" devuelve "swoot"
```

The last parameter, "g", is the global flag. With it, Vim will substitute all matching occurrences. Without it, Vim will only substitute the first match.

```
:echo substitute("sweet", "e", "o", "")
" devuelve "swoet"
```

The substitute command can be combined with `getline()`. Recall that the function `getline()` gets the text on the given line number. Suppose you have the text "chocolate donut" on line 5. You can use the procedure:

```
:echo substitute(getline(5), "chocolate", "glazed", "g")
" devuelve glazed donut
```

There are many other string procedures. Check out `:h string-functions`.

## List

A Vimscript list is like an Array in Javascript or List in Python. It is an *ordered* sequence of items. You can mix-and-match the content with different data types:

```
[1,2,3]
['a', 'b', 'c']
[1,'a', 3.14]
[1,2,[3,4]]
```

### Sublists

Vim list is zero-indexed. You can access a particular item in a list with `[n]`, where n is the index.

```
:echo ["a", "sweet", "dessert"][0]
" devuelve "a"

:echo ["a", "sweet", "dessert"][2]
" devuelve "dessert"
```

If you go over the maximum index number, Vim will throw an error saying that the index is out of range:

```
:echo ["a", "sweet", "dessert"][999]
" devuelve an error
```

When you go below zero, Vim will start the index from the last element. Going past the minimum index number will also throw you an error:

```
:echo ["a", "sweet", "dessert"][-1]
" devuelve "dessert"

:echo ["a", "sweet", "dessert"][-3]
" devuelve "a"

:echo ["a", "sweet", "dessert"][-999]
" devuelve an error
```

You can "slice" several elements from a list with `[n:m]`, where `n` is the starting index and `m` is the ending index.

```
:echo ["chocolate", "glazed", "plain", "strawberry", "lemon", "sugar", "cream"][2:4]
" devuelve ["plain", "strawberry", "lemon"]
```

If you don't pass `m` (`[n:]`), Vim will return the rest of the elements starting from the nth element. If you don't pass `n` (`[:m]`), Vim will return the first element up to the mth element.

```
:echo ["chocolate", "glazed", "plain", "strawberry", "lemon", "sugar", "cream"][2:]
" devuelve ['plain', 'strawberry', 'lemon', 'sugar', 'cream']

:echo ["chocolate", "glazed", "plain", "strawberry", "lemon", "sugar", "cream"][:4]
" devuelve ['chocolate', 'glazed', 'plain', 'strawberry', 'lemon']
```

You can pass an index that exceeds the maximum items when slicing an array.

```viml
:echo ["chocolate", "glazed", "plain", "strawberry", "lemon", "sugar", "cream"][2:999]
" devuelve ['plain', 'strawberry', 'lemon', 'sugar', 'cream']
```

### Slicing String

You can slice and target strings just like lists:

```viml
:echo "choco"[0]
" devuelve "c"

:echo "choco"[1:3]
" devuelve "hoc"

:echo "choco"[:3]
" devuelve choc

:echo "choco"[1:]
" devuelve hoco
```

### List Arithmetic

You can use `+` to concatenate and mutate a list:

```viml
:let sweetList = ["chocolate", "strawberry"]
:let sweetList += ["sugar"]
:echo sweetList
" devuelve ["chocolate", "strawberry", "sugar"]
```

### List Functions

Let's explore Vim's built-in list functions.

To get the length of a list, use `len()`:

```
:echo len(["chocolate", "strawberry"])
" devuelve 2
```

To prepend  an element to a list, you can use `insert()`:

```
:let sweetList = ["chocolate", "strawberry"]
:call insert(sweetList, "glazed")

:echo sweetList
" devuelve ["glazed", "chocolate", "strawberry"]
```

You can also pass `insert()` the index where you want to prepend the element to. If you want to prepend an item before the second element (index 1):

```
:let sweeterList = ["glazed", "chocolate", "strawberry"]
:call insert(sweeterList, "cream", 1)

:echo sweeterList
" devuelve ['glazed', 'cream', 'chocolate', 'strawberry']
```

To remove a list item, use `remove()`. It accepts a list and the element index you want to remove.

```
:let sweeterList = ["glazed", "chocolate", "strawberry"]
:call remove(sweeterList, 1)

:echo sweeterList
" devuelve ['glazed', 'strawberry']
```

You can use `map()` and `filter()` on a list. To filter out element containing the phrase "choco":

```
:let sweeterList = ["glazed", "chocolate", "strawberry"]
:call filter(sweeterList, 'v:val !~ "choco"')
:echo sweeterList
" devuelve ["glazed", "strawberry"]

:let sweetestList = ["chocolate", "glazed", "sugar"]
:call map(sweetestList, 'v:val . " donut"')
:echo sweetestList
" devuelve ['chocolate donut', 'glazed donut', 'sugar donut']
```

The `v:val` variable is a Vim special variable. It is available when iterating a list or a dictionary using `map()` or `filter()`. It represents each iterated item.

For more, check out `:h list-functions`.

### List Unpacking

You can unpack a list and assign variables to the list items:

```
:let favoriteFlavor = ["chocolate", "glazed", "plain"]
:let [flavor1, flavor2, flavor3] = favoriteFlavor

:echo flavor1
" devuelve "chocolate"

:echo flavor2
" devuelve "glazed"
```

To assign the rest of list items, you can use `;` followed with a variable name:

```
:let favoriteFruits = ["apple", "banana", "lemon", "blueberry", "raspberry"]
:let [fruit1, fruit2; restFruits] = favoriteFruits

:echo fruit1
" devuelve "apple"

:echo restFruits
" devuelve ['lemon', 'blueberry', 'raspberry']
```

### Modifying List

You can modify a list item directly:

```
:let favoriteFlavor = ["chocolate", "glazed", "plain"]
:let favoriteFlavor[0] = "sugar"
:echo favoriteFlavor
" devuelve ['sugar', 'glazed', 'plain']
```

You can mutate multiple list items directly:

```
:let favoriteFlavor = ["chocolate", "glazed", "plain"]
:let favoriteFlavor[2:] = ["strawberry", "chocolate"]
:echo favoriteFlavor
devuelve ['chocolate', 'glazed', 'strawberry', 'chocolate']
```

## Dictionary

A Vimscript dictionary is an associative, unordered list. A non-empty dictionary consists of at least a key-value pair.

```
{"breakfast": "waffles", "lunch": "pancakes"}
{"meal": ["breakfast", "second breakfast", "third breakfast"]}
{"dinner": 1, "dessert": 2}
```

A Vim dictionary data object uses string for key. If you try to use a number, Vim will coerce it into a string.

```
:let breakfastNo = {1: "7am", 2: "9am", "11ses": "11am"}

:echo breakfastNo
" devuelve {'1': '7am', '2': '9am', '11ses': '11am'}
```

If you are too lazy to put quotes around each key, you can use the `#{}` notation:

```
:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}

:echo mealPlans
" devuelve {'lunch': 'pancakes', 'breakfast': 'waffles', 'dinner': 'donuts'}
```

The only requirement for using the `#{}` syntax is that each key must be either:

- ASCII character.
- Digit.
- An underscore (`_`).
- A hyphen (`-`).

Just like list, you can use any data type as values.

```
:let mealPlan = {"breakfast": ["pancake", "waffle", "hash brown"], "lunch": WhatsForLunch(), "dinner": {"appetizer": "gruel", "entree": "more gruel"}}
```

### Accessing Dictionary

To access a value from a dictionary, you can call the key with either the square brackets (`['key']`) or the dot notation (`.key`).

```
:let meal = {"breakfast": "gruel omelettes", "lunch": "gruel sandwiches", "dinner": "more gruel"}

:let breakfast = meal['breakfast']
:let lunch = meal.lunch

:echo breakfast
" devuelve "gruel omelettes"

:echo lunch
" devuelve "gruel sandwiches"
```

### Modifying Dictionary

You can modify or even add a dictionary content:

```
:let meal = {"breakfast": "gruel omelettes", "lunch": "gruel sandwiches"}

:let meal.breakfast = "breakfast tacos"
:let meal["lunch"] = "tacos al pastor"
:let meal["dinner"] = "quesadillas"

:echo meal
" devuelve {'lunch': 'tacos al pastor', 'breakfast': 'breakfast tacos', 'dinner': 'quesadillas'}
```

### Dictionary Functions

Let's explore some of Vim's built-in functions to handle Dictionaries.

To check the length of a dictionary, use `len()`.

```
:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}

:echo len(meaPlans)
" devuelve 3
```

To see if a dictionary contains a specific key, use `has_key()`

```
:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}

:echo has_key(mealPlans, "breakfast")
" devuelve 1

:echo has_key(mealPlans, "dessert")
" devuelve 0
```

To see if a dictionary has any item, use `empty()`. The `empty()` procedure works with all data types: list, dictionary, string, number, float, etc.

```
:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}
:let noMealPlan = {}

:echo empty(noMealPlan)
" devuelve 1

:echo empty(mealPlans)
" devuelve 0
```

To remove an entry from a dictionary, use `remove()`.

```
:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}

:echo "removing breakfast: " . remove(mealPlans, "breakfast")
" devuelve "removing breakfast: 'waffles'""

:echo mealPlans
" devuelve {'lunch': 'pancakes', 'dinner': 'donuts'}
```

To convert a dictionary into a list of lists, use `items()`:

```
:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}

:echo items(mealPlans)
" devuelve [['lunch', 'pancakes'], ['breakfast', 'waffles'], ['dinner', 'donuts']]
```

`filter()` and `map()` are also available.

```
:let breakfastNo = {1: "7am", 2: "9am", "11ses": "11am"}
:call filter(breakfastNo, 'v:key > 1')

:echo breakfastNo
" devuelve {'2': '9am', '11ses': '11am'}

:let mealPlans = #{breakfast: "waffles", lunch: "pancakes", dinner: "donuts"}
:call map(mealPlans, 'v:key . " and milk"')

:echo mealPlans
" devuelve {'lunch': 'lunch and milk', 'breakfast': 'breakfast and milk', 'dinner': 'dinner and milk'}
```

The `v:key` is Vim's special variable, much like `v:val`. When iterating through a dictionary, `v:key` will hold the value of the current iterated key.

To see more dictionary functions, check out `:h dict-functions`.

## Special Primitives

Vim has special primitives:

- `v:false`
- `v:true`
- `v:none`
- `v:null`

By the way, `v:` is Vim's built-in variable. They will be covered more in a later chapter.

In my experience, you won't use these special primitives often. If you need a truthy / falsy value, you can just use 0 (falsy) and non-0 (truthy). If you need an empty string, just use `""`. But it is still good to know, so let's quickly go over them.

### True

This is equivalent to `true`. It is equivalent to a number with value of non-0 . When decoding json with `json_encode()`, it is interpreted as "true".

```
:echo json_encode({"test": v:true})
" devuelve {"test": true}
```

### False

This is equivalent to `false`. It is equivalent to a number with value of 0. When decoding json with `json_encode()`, it is interpreted as "false".

```
:echo json_encode({"test": v:false})
" devuelve {"test": false}
```

### None

It is equivalent to an empty string. When decoding json with `json_encode()`, it is interpreted as an empty item (`null`).

```
:echo json_encode({"test": v:none})
" devuelve {"test": null}
```

### Null

Similar to `v:none`.

```
:echo json_encode({"test": v:null})
" devuelve {"test": null}
```

## Learn Data Types The Smart Way

In this chapter, you learned about Vimscript's basic data types: number, float, string, list, dictionary, and special. Learning these is the first step to start Vimscript programming.

In the next chapter, you will learn how to combine them for writing expressions like equalities, conditionals, and loops.
