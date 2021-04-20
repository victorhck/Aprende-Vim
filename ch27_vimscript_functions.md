# Capítulo 27. Funciones en Vimscript

Las funciones son medios de abtracción, el tercer elemento en el aprendizaje de un nuevo lenguajes de programación.

En capítulos previos, has visto en acción las funciones nativas de Vimscript (`len()`, `filter()`, `map()`, etc.) y las funciones personalizadas. en este capítulo, vas a profundizar en aprender cómo funcionan las funciones.

## Reglas de sintaxis de las funciones

En esencia, una función de Vimscript tiene la siguiente sintaxis:

```
function {NombreFuncion}()
  {algunas_tareas}
endfunction
```

Una definición de una función debe comenzar con una letra mayúscula. Comienza con la palabra clave `function` y termina con `endfunction`. A continuación encontrarás una función válida:

```
function! Sabrosa()
  echo "Sabrosa"
endfunction
```

La siguiente no es una función válida porque no comienza con una letra mayúscula.

```
function sabrosa()
  echo "Sabrosa"
endfunction
```

Si antepones una función con el prefijo que la marca como una variable de script (`s:`), puedes declararla con una letra minúscula . `function s:sabrosa()` es un nombre válido. La razón de porque Vim necesita que se utilice un nombre con la letra inicial en mayúscula es para prevenir una confusión con las funciones propias de Vim (que son todas en minúsculas).

Un nombre de función no puede comenzar con un número. `1Sabrosa()` no es un nombre de función válido, pero `Sabrosa1()` si lo es. Una función tampoco puede contener caracteres no alfanuméricos menos `_`. `Sabrosa-comida()`, `Sabrosa&comida()` o `Sabrosa.comida()` todos estos son ejemplos de nombres no válidos para nombres de funciones. `Sabrosa_comida()` *si lo es*.

Si defines dos funciones con el mismo nombre, Vim mostrará un error quejándose de que la función `Sabrosa` ya existe. Para sobrescribit la función previa con el mismo nombre, añade un símbolo `!` después de la palabra clave `function`.

```
function! Sabrosa()
  echo "Sabrosa"
endfunction
```

## Mostrar un listado con las funciones disponibles

Para ver todas las funciones propias y personalizadas en Vim, puedes ejecutar el comando `:function`. Para ver el contenido de la función `Sabrosa`, puedes ejecutar `:function Sabrosa`.

También puedes buscar funciones con un patrón mediante `:function /patrón`, de manera similar a la navegación de búsqueda con Vim (`/patrón`). Para buscar todas las funciones que contengan la palabra "map", ejecuta `:function /map`. Si utilizas complementos externos, Vim mostrará las funciones definidas en esos complementos.

Si quieres ver dónde se origina una función, puedes utilizar el comando `:verbose` junto con el comando `:function`. Para buscar dónde se originan todas las funciones que contienen la palabra "map", ejecuta:

```
:verbose function /map
```

Cuando lo ejecuto, obtengo un número de resultados. Esto me dice que la función `fzf#vim#maps` una función de autoload (vuelve a echar un vitazo al capítulo 23) está escrita dentro del archivo `~/.vim/plugged/fzf.vim/autoload/fzf/vim.vim`, en la línea 1263. Esto es muy útil a la hora de depurar errores.

```
function fzf#vim#maps(mode, ...)
        Last set from ~/.vim/plugged/fzf.vim/autoload/fzf/vim.vim line 1263
```

## Eliminar una función

Para eliminar una función existente, utiliza `:delfunction {Nombre_de_funcion}`. Para eliminar `Sabrosa`, ejecuta `:delfunction Sabrosa`.

## Valor de retorno de una función

Para que una función devuelva un valor, necesitas pasarle de manera explícita un valor `return`. De otra manera, Vim automáticamente devuelve de manera implícita un valor 0.

```
function! Sabrosa()
  echo "Sabrosa"
endfunction
```

Un valor `return` vacío es equivalente a un valor 0.

```
function! Sabrosa()
  echo "Sabrosa"
  return
endfunction
```

Si ejecutas `:echo Sabrosa()` utilizando la función anterior, después de que Vim muestre la palabra "Sabrosa", devolverá un 0, el valor implícito de retorno. Para hacer que la función `Sabrosa()` devuelva el valor "Sabrosa", puedes hacer lo siguiente:

```
function! Sabrosa()
  return "Sabrosa"
endfunction
```

Ahora cuando ejecutas `:echo Sabrosa()`, esta función devuelve la cadena "Sabrosa".

Puedes utilizar una función dentro de una expresión. Vim utilizará el valor de retorno de esa función. La expresión `:echo Sabrosa() . " comida."` mostrará "Sabrosa comida."

## Argumentos formales

Para pasarle el argumento formal `comida` a tu función `Sabrosa`, puedes hacerlo de la siguiente manera:

```
function! Sabrosa(comida)
  return "Sabrosa " . a:comida
endfunction

echo Tasty("paella")
" returns "Sabrosa paella"
```

`a:` es uno de los ámbitos de las variables mencionados en el capítulo anterior. Es el de variable de parámtero formal. Es la manera de Vim para obtener un parámetro formal en una función. Sin este, Vim mostrará un errror:

```
function! Sabrosa(comida)
  return "Sabrosa " . comida
endfunction

echo Tasty("paella")
" devuelve "undefined variable name" error
```

## Variable local de función

Vamos a ver el otro tipo de variable que no aprendimos en el capítulo anterior: la variable local de función (`l:`).

Cuando estás escribiendo una función, puedes definir una variable dentro de la función:

```
function! Delicioso()
  let sitio = "panza"
  return "Delicioso en mi " . sitio
endfunction

echo Delicioso()
" devuelve "Delicioso en mi panza"
```

En este contexto, la variable `sitio` es lo mismo que `l:sitio`. Cuando defines una variable en una función, es variable es *local* a esa función. Cuando un usuario ve `sitio`, podría fácilmente caer en el error de que se trata de una variable global. Por eso prefiero no escatimar en información y siempre añadir `l:` para indicar que es una variable propia de la función.

Otra razón para utilizar el método `l:count` al declarar una variable propia de una función es que Vim tiene unas variables especiales con alias que son similares a una variable normal. `v:count` es un ejemplo. Esta variable tien un alias `count`. En Vim, llamar a `count` es lo mismo que que llamar a `v:count`. Es muy sencillo llamar de manera accidental a una de esas variables especiales.

```
function! Calorias()
  let count = "count"
  return "I do not " . count . " my calories"
endfunction

echo Calorias()
" muestra un error
```

La ejecución anterior muestra un error, porque `let count = "count"` de manera implícita trata de redefinir una variable especial de Vim `v:count`. Recuerda que las variables especiales (`v:`) son de solo lectura. No las puedes cambiar. Para solucionar esto, utiliza `l:count`:

```
function! Calorias()
  let l:count = "count"
  return "I do not " . l:count . " my calories"
endfunction

echo Calories()
" returns "I do not count my calories"
```

## Llamar a una función

Vim tiene un comando `:call` para llamar a una función.

```
function! Sabrosa(comida)
  return "Sabrosa " . a:comida
endfunction

call Sabrosa("salsa")
```

El comando `call` no muestra el valor de retorno. Vamos a llamar a la función combinando los comandos `call` y `echo`.

```
echo call Sabrosa("salsa")
```

¡Vaya! obtuviste un error. El comando `call` anterior es un comando de la línea de comandos (`:call`). El comando `echo` anterior es también un comando para la línea de comandos (`:echo`). No puedes llamar a un comando para la línea de comandos con otro comando para la línea de comandos. Vamos a utilizar otra opción con el comando `call`:

```
echo call("Sabrosa", ["salsa"])
" devuelve "Sabrosa salsa"
```

Para aclarar cualquier confusión, acabas de utilizar dos comandos `call` diferentes: el comando `:call` para la línea de comandos y la función `call()`. La función `call()` acepta como su primer argumento el nombre de la función (una cadena) y su segundo argumento los parámetros formales (una lista). 

Para aprender más sobre `:call` y `call()`, echa un vistazo `:h call()` y `:h :call`.

## Argumento predeterminado

Puedes crear una función con un valor predeterminado con `=`. Si llamas a la función `Desayuno` con solo un argumento, el argumento `bebida` usará el valor predeterminado "leche".

```
function! Desayuno(comida, bebida = "Leche")
  return "Tomé " . a:comida . " y " . a:bebida . " para desayunar"
endfunction

echo Desayuno("Gofres")
" devuelve Gofres y Leche

echo Desayuno("Cereales", "Zumo de naranja")
" devuelve Cereales y Zumo de naranja
```

## Variable Arguments

You can pass a variable argument with three-dots (`...`). Variable argument is useful when you don't know how many variables a user will give.

Suppose you are creating an all-you-can-eat buffet (you'll never know how much food your customer will eat):

```
function! Buffet(...)
  return a:1
endfunction
```

If you run `echo Buffet("Noodles")`, it will output "Noodles". Vim uses `a:1` to print the *first* argument passed to `...`, up to 20 (`a:1` is the first argument, `a:2` is the second argument, etc). If you run `echo Buffet("Noodles", "Sushi")`, it will still display just "Noodles", let's update it:

```
function! Buffet(...)
  return a:1 . " " . a:2
endfunction

echo Buffet("Noodles", "Sushi")
" Returns "Noodles Sushi"
```

The problem with this approach is if you now run `echo Buffet("Noodles")` (with only one variable), Vim complains that it has an undefined variable `a:2`. How can you make it flexible enough to display exactly what the user gives?

Luckily, Vim has a special variable `a:0` to display the *length* of the argument passed into `...`.

```
function! Buffet(...)
  return a:0
endfunction

echo Buffet("Noodles")
" returns 1

echo Buffet("Noodles", "Sushi")
" returns 2

echo Buffet("Noodles", "Sushi", "Ice cream", "Tofu", "Mochi")
" returns 5
```

With this, you can iterate using the length of the argument.

```
function! Buffet(...)
  let l:food_counter = 1
  let l:foods = ""
  while l:food_counter <= a:0
    let l:foods .= a:{l:food_counter} . " "
    let l:food_counter += 1
  endwhile
  return l:foods
endfunction
```

The curly braces `a:{l:food_counter}` is a string interpolation, it uses the value of `food_counter` counter to call the formal parameter arguments `a:1`, `a:2`, `a:3`, etc.

```
echo Buffet("Noodles")
" returns "Noodles"

echo Buffet("Noodles", "Sushi", "Ice cream", "Tofu", "Mochi")
" returns everything you passed: "Noodles Sushi Ice cream Tofu Mochi"
```

The variable argument has one more special variable: `a:000`. It has the value of all variable arguments in a list format.

```
function! Buffet(...)
  return a:000
endfunction

echo Buffet("Noodles")
" returns ["Noodles"]

echo Buffet("Noodles", "Sushi", "Ice cream", "Tofu", "Mochi")
" returns ["Noodles", "Sushi", "Ice cream", "Tofu", "Mochi"]
```

Let's refactor the function to use a `for` loop:

```
function! Buffet(...)
  let l:foods = ""
  for food_item in a:000
    let l:foods .= food_item . " "
  endfor
  return l:foods
endfunction

echo Buffet("Noodles", "Sushi", "Ice cream", "Tofu", "Mochi")
" returns Noodles Sushi Ice cream Tofu Mochi
```

## Range

You can define a *ranged* Vimscript function by adding a `range` keyword at the end of the function definition. A ranged function has two special variables available: `a:firstline` and `a:lastline`.

```
function! Breakfast() range
  echo a:firstline
  echo a:lastline
endfunction
```

If you are on line 100 and you run `call Breakfast()`, it will display 100 for both `firstline` and `lastline`. If you visually highlight (`v`, `V`, or `Ctrl-V`) lines 101 to 105 and run `call Breakfast()`, `firstline` displays 101 and `lastline` displays 105. `firstline` and `lastline` displays the minimum and maximum range where the function is called.

You can also use `:call` and passing it a range. If you run `:11,20call Breakfast()`, it will display 11 for `firstline` and 20 for `lastline`.

You might ask, "That's nice that Vimscript function accepts range, but can't I get the line number with `line(".")`? Won't it do the same thing?"

Good question. If this is what you mean:

```
function! Breakfast()
  echo line(".")
endfunction
```

Calling `:11,20call Breakfast()` executes the `Breakfast` function 10 times (one for each line in the range). Compare that if you had passed the `range` argument:

```
function! Breakfast() range
  echo line(".")
endfunction
```

Calling `11,20call Breakfast()` executes the `Breakfast` function *once*.

If you pass a `range` keyword and you pass a numerical range (like `11,20`) on `call`, Vim only executes that function once. If you don't pass a `range` keyword and you pass a numerical range (like `11,20`) on `call`, Vim executes that function N times depending on the range (in this case, N = 10).

## Dictionary

You can add a function as a dictionary item by adding a `dict` keyword when defining a function.

If you have a function `SecondBreakfast` that returns whatever `breakfast` item you have:

```
function! SecondBreakfast() dict
  return self.breakfast
endfunction
```

Let's add this function to the `meals` dictionary:

```
let meals = {"breakfast": "pancakes", "second_breakfast": function("SecondBreakfast"), "lunch": "pasta"}

echo meals.second_breakfast()
" returns "pancakes"
```

With `dict` keyword, the key variable `self` refers to the dictionary where the function is stored (in this case, the `meals` dictionary). The expression `self.breakfast` is equal to `meals.breakfast`.

An alternative way to add a function into a dictionary object to use a namespace.

```
function! meals.second_lunch()
  return self.lunch
endfunction

echo meals.second_lunch()
" returns "pasta"
```

With namespace, you do not have to use the `dict` keyword.

## Funcref

A funcref is a reference to a function. It is one of Vimscript's basic data types mentioned in Ch. 24.

The expression `function("SecondBreakfast")` above is an example of funcref. Vim has a built-in function `function()` that returns a funcref when you pass it a function name (string).

```
function! Breakfast(item)
  return "I am having " . a:item . " for breakfast"
endfunction

let Breakfastify = Breakfast
" returns error

let Breakfastify = function("Breakfast")

echo Breakfastify("oatmeal")
" returns "I am having oatmeal for breakfast"

echo Breakfastify("pancake")
" returns "I am having pancake for breakfast"
```

In Vim, if you want to assign a function to a variable, you can't just run assign it directly like `let MyVar = MyFunc`. You need to use the `function()` function, like `let MyFar = function("MyFunc")`.

You can use funcref with maps and filters. Note that maps and filters will pass an index as the first argument and the iterated value as the second argument.

```
function! Breakfast(index, item)
  return "I am having " . a:item . " for breakfast"
endfunction

let breakfast_items = ["pancakes", "hash browns", "waffles"]
let first_meals = map(breakfast_items, function("Breakfast"))

for meal in first_meals
  echo meal
endfor
```

## Lambda

A better way to use functions in maps and filters is to use lambda expression (sometimes known as unnamed function). For example:

```
let Plus = {x,y -> x + y}
echo Plus(1,2)
" returns 3

let Tasty = { -> 'tasty'}
echo Tasty()
" returns "tasty"
```

You can call a function from insisde a lambda expression:

```
function! Lunch(item)
  return "I am having " . a:item . " for lunch"
endfunction

let lunch_items = ["sushi", "ramen", "sashimi"]

let day_meals = map(lunch_items, {index, item -> Lunch(item)})

for meal in day_meals
  echo meal
endfor
```

If you don't want to call the function from inside lambda, you can refactor it:

```
let day_meals = map(lunch_items, {index, item -> "I am having " . item . " for lunch"})
```

## Method Chaining

You can chain several Vimscript functions and lambda expressions sequentially with `->`. Keep in mind that `->` must be followed by a method name *without space.*

```
Source->Method1()->Method2()->...->MethodN()
```

To convert a float to a number using method chaining:

```
echo 3.14->float2nr()
" returns 3
```

Let's do a more complicated example. Suppose that you need to capitalize the first letter of each item on a list, then sort the list, then join the list to form a string.

```
function! Capitalizer(word)
  return substitute(a:word, "\^\.", "\\u&", "g")
endfunction

function! CapitalizeList(word_list)
  return map(a:word_list, {index, word -> Capitalizer(word)})
endfunction

let dinner_items = ["bruschetta", "antipasto", "calzone"]

echo dinner_items->CapitalizeList()->sort()->join(", ")
" returns "Antipasto, Bruschetta, Calzone"
```

With method chaining, the sequence is more easily read and understood. I can just glance at `dinner_items->CapitalizeList()->sort()->join(", ")` and know exactly what is going on.

## Closure

When you define a variable inside a function, that variable exists within that function boundaries. This is called a lexical scope.

```
function! Lunch()
  let appetizer = "shrimp"

  function! SecondLunch()
    return appetizer
  endfunction

  return funcref("SecondLunch")
endfunction
```

`appetizer` is defined inside the `Lunch` function, which returns `SecondLunch` funcref. Notice that `SecondLunch` uses the `appetizer`, but in Vimscript, it doesn't have access to that variable. If you try to run `echo Lunch()()`, Vim will throw an undefined variable error.

To fix this issue, use the `closure` keyword. Let's refactor:

```
function! Lunch()
  let appetizer = "shrimp"

  function! SecondLunch() closure
    return appetizer
  endfunction

  return funcref("SecondLunch")
endfunction
```

Now if you run `echo Lunch()()`, Vim will return "shrimp".

## Learn Vimscript Functions The Smart Way

In this chapter, you learned the anatomy of Vim function. You learned how to use different special keywords `range`, `dict`, and `closure` to modify function behavior. You also learned how to use lambda and to chain multiple functions together. Functions are important tools for creating complex abstractions.

This concludes this Vim guide. However, your Vim journey doesn't end here. In fact, it actually starts now. You should have sufficient knowledge to go on your own or even create your own plugins.

Happy Vimming, friends!
