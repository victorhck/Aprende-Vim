# Capítulo 29: Funciones en Vimscript

Las funciones son medios de abstracción, el tercer elemento en el aprendizaje de un nuevo lenguajes de programación.

En capítulos previos, has visto en acción las funciones nativas de Vimscript \(`len()`, `filter()`, `map()`, etc.\) y las funciones personalizadas. En este capítulo, vas a profundizar en aprender cómo funcionan las funciones.

## Reglas de sintaxis de las funciones

En esencia, una función de Vimscript tiene la siguiente sintaxis:

```text
function {NombreFuncion}()
  {algunas_tareas}
endfunction
```

Una definición de una función debe comenzar con una letra mayúscula. Comienza con la palabra clave `function` y termina con `endfunction`. A continuación veremos un ejemplo de una función válida:

```text
function! Sabrosa()
  echo "Sabrosa"
endfunction
```

La siguiente no es una función válida porque no comienza con una letra mayúscula.

```text
function sabrosa()
  echo "Sabrosa"
endfunction
```

Si antepones una función con el prefijo que la marca como una variable de script \(`s:`\), puedes declararla con una letra minúscula . `function s:sabrosa()` es un nombre válido. La razón de porque Vim necesita que se utilice un nombre con la letra inicial en mayúscula es para prevenir una confusión con las funciones propias de Vim \(que son todas en minúsculas\).

Un nombre de función no puede comenzar con un número. `1Sabrosa()` no es un nombre de función válido, pero `Sabrosa1()` si lo es. Una función tampoco puede contener caracteres no alfanuméricos menos `_`. `Sabrosa-comida()`, `Sabrosa&comida()` o `Sabrosa.comida()` todos estos son ejemplos de nombres no válidos para nombres de funciones. `Sabrosa_comida()` _si lo es_.

Si defines dos funciones con el mismo nombre, Vim mostrará un error quejándose de que la función `Sabrosa` ya existe. Para sobrescribir la función previa con el mismo nombre, añade un símbolo `!` después de la palabra clave `function`.

```text
function! Sabrosa()
  echo "Sabrosa"
endfunction
```

## Mostrar un listado con las funciones disponibles

Para ver todas las funciones propias y personalizadas en Vim, puedes ejecutar el comando `:function`. Para ver el contenido de la función `Sabrosa`, puedes ejecutar `:function Sabrosa`.

También puedes buscar funciones con un patrón mediante `:function /patrón`, de manera similar a la navegación de búsqueda con Vim \(`/patrón`\). Para buscar todas las funciones que contengan la palabra "map", ejecuta `:function /map`. Si utilizas complementos externos, Vim mostrará las funciones definidas en esos complementos.

Si quieres ver dónde se origina una función, puedes utilizar el comando `:verbose` junto con el comando `:function`. Para buscar dónde se originan todas las funciones que contienen la palabra "map", ejecuta:

```text
:verbose function /map
```

Cuando lo ejecuto, obtengo un número de resultados. Esto me dice que la función `fzf#vim#maps` una función de autoload \(vuelve a echar un vistazo al capítulo 23\) está escrita dentro del archivo `~/.vim/plugged/fzf.vim/autoload/fzf/vim.vim`, en la línea 1263. Esto es muy útil a la hora de depurar errores.

```text
function fzf#vim#maps(mode, ...)
        Last set from ~/.vim/plugged/fzf.vim/autoload/fzf/vim.vim line 1263
```

## Eliminar una función

Para eliminar una función existente, utiliza `:delfunction {Nombre_de_funcion}`. Para eliminar `Sabrosa`, ejecuta `:delfunction Sabrosa`.

## Valor de retorno de una función

Para que una función devuelva un valor, necesitas pasarle de manera explícita un valor `return`. De otra manera, Vim automáticamente devuelve de manera implícita un valor 0.

```text
function! Sabrosa()
  echo "Sabrosa"
endfunction
```

Un valor `return` vacío es equivalente a un valor 0.

```text
function! Sabrosa()
  echo "Sabrosa"
  return
endfunction
```

Si ejecutas `:echo Sabrosa()` utilizando la función anterior, después de que Vim muestre la palabra "Sabrosa", devolverá un 0, el valor implícito de retorno. Para hacer que la función `Sabrosa()` devuelva el valor "Sabrosa", puedes hacer lo siguiente:

```text
function! Sabrosa()
  return "Sabrosa"
endfunction
```

Ahora cuando ejecutas `:echo Sabrosa()`, esta función devuelve la cadena "Sabrosa".

Puedes utilizar una función dentro de una expresión. Vim utilizará el valor de retorno de esa función. La expresión `:echo Sabrosa() . " comida."` mostrará "Sabrosa comida."

## Argumentos formales

Para pasarle el argumento formal `comida` a tu función `Sabrosa`, puedes hacerlo de la siguiente manera:

```text
function! Sabrosa(comida)
  return "Sabrosa " . a:comida
endfunction

echo Tasty("paella")
" devuelve "Sabrosa paella"
```

`a:` es uno de los ámbitos de las variables mencionados en el capítulo anterior. Es el de variable de parámetro formal. Es la manera de Vim para obtener un parámetro formal en una función. Sin este, Vim mostrará un error:

```text
function! Sabrosa(comida)
  return "Sabrosa " . comida
endfunction

echo Tasty("paella")
" devuelve "undefined variable name" error
```

## Variable local de función

Vamos a ver el otro tipo de variable que no aprendimos en el capítulo anterior: la variable local de función \(`l:`\).

Cuando estás escribiendo una función, puedes definir una variable dentro de la función:

```text
function! Delicioso()
  let sitio = "panza"
  return "Delicioso en mi " . sitio
endfunction

echo Delicioso()
" devuelve "Delicioso en mi panza"
```

En este contexto, la variable `sitio` es lo mismo que `l:sitio`. Cuando defines una variable en una función, es variable es _local_ a esa función. Cuando un usuario ve `sitio`, podría fácilmente caer en el error de que se trata de una variable global. Por eso prefiero no escatimar en información y siempre añadir `l:` para indicar que es una variable propia de la función.

Otra razón para utilizar el método `l:count` al declarar una variable propia de una función es que Vim tiene unas variables especiales con alias que son similares a una variable normal. `v:count` es un ejemplo. Esta variable tiene un alias `count`. En Vim, llamar a `count` es lo mismo que que llamar a `v:count`. Es muy sencillo llamar de manera accidental a una de esas variables especiales.

```text
function! Calorias()
  let count = "count"
  return "I do not " . count . " my calories"
endfunction

echo Calorias()
" muestra un error
```

La ejecución anterior muestra un error, porque `let count = "count"` de manera implícita trata de redefinir una variable especial de Vim `v:count`. Recuerda que las variables especiales \(`v:`\) son de solo lectura. No las puedes cambiar. Para solucionar esto, utiliza `l:count`:

```text
function! Calorias()
  let l:count = "count"
  return "I do not " . l:count . " my calories"
endfunction

echo Calories()
" devuelve "I do not count my calories"
```

## Llamar a una función

Vim tiene un comando `:call` para llamar a una función.

```text
function! Sabrosa(comida)
  return "Sabrosa " . a:comida
endfunction

call Sabrosa("salsa")
```

El comando `call` no muestra el valor de retorno. Vamos a llamar a la función combinando los comandos `call` y `echo`.

```text
echo call Sabrosa("salsa")
```

¡Vaya! Obtuviste un error. El comando `call` anterior es un comando de la línea de comandos \(`:call`\). El comando `echo` anterior es también un comando para la línea de comandos \(`:echo`\). No puedes llamar a un comando para la línea de comandos con otro comando para la línea de comandos. Vamos a utilizar otra opción con el comando `call`:

```text
echo call("Sabrosa", ["salsa"])
" devuelve "Sabrosa salsa"
```

Para aclarar cualquier confusión, acabas de utilizar dos comandos `call` diferentes: el comando `:call` para la línea de comandos y la función `call()`. La función `call()` acepta como su primer argumento el nombre de la función \(una cadena\) y su segundo argumento los parámetros formales \(una lista\).

Para aprender más sobre `:call` y `call()`, echa un vistazo `:h call()` y `:h :call`.

## Argumento predeterminado

Puedes crear una función con un valor predeterminado con `=`. Si llamas a la función `Desayuno` con solo un argumento, el argumento `bebida` usará el valor predeterminado "leche".

```text
function! Desayuno(comida, bebida = "Leche")
  return "Tomé " . a:comida . " y " . a:bebida . " para desayunar"
endfunction

echo Desayuno("Gofres")
" devuelve Gofres y Leche

echo Desayuno("Cereales", "Zumo de naranja")
" devuelve Cereales y Zumo de naranja
```

## Argumentos de variables

Puedes pasarle una variable a un argumento mediante tres puntos \(`...`\). Un argumento de variable es útil cuando no sabes cuantas variables dará un usuario.

Supongamos que estás creando un _buffet_ en el que puedas comer todo lo que desees \(nunca sabes cuanta comida comerán tus comensales\):

```text
function! Buffet(...)
  return a:1
endfunction
```

Si ejecutas `echo Buffet("Fideos")`, entonces mostrará "Fideos". Vim utiliza `a:1` para mostrar el _primer_ argumento que se le pasa a la función hasta `...`, más de 20 \(`a:1` es el primer argumento, `a:2` es el segundo argumento, etc\). Si ejecutas `echo Buffet("Fideos", "Sushi")`, mostrará solo "Fideos", vamos a actualizar la función:

```text
function! Buffet(...)
  return a:1 . " " . a:2
endfunction

echo Buffet("Fideos", "Sushi")
" devuelve "Fideos Sushi"
```

El problema con este método es que si ejecutas `echo Buffet("Fideos")` \(con solo una variables\), Vim se quejará de que tiene una variable no definida en `a:2`. ¿Cómo podemos hacerlo lo suficientemente flexible para mostrar exactamente lo que el usuario le de a la función?

Afortunadamente, Vim tiene una variable especial `a:0` para mostrar la _longitud_ del argumento pasado en `...`.

```text
function! Buffet(...)
  return a:0
endfunction

echo Buffet("Fideos")
" devuelve 1

echo Buffet("Fideos", "Sushi")
" devuelve 2

echo Buffet("Fideos", "Sushi", "Helado", "Tofu", "Mochi")
" devuelve 5
```

Con esto, puedes consultar la longitud del argumento.

```text
function! Buffet(...)
  let l:contador_comida = 1
  let l:alimentos = ""
  while l:contador_comida <= a:0
    let l:alimentos .= a:{l:contador_comida} . " "
    let l:contador_comida += 1
  endwhile
  return l:alimentos
endfunction
```

Las llaves `a:{l:contador_comida}` es una interpolación de una cadena, utiliza el valor del contador `contador_comida` para llamar a los argumentos del parámetro formal `a:1`, `a:2`, `a:3`, etc.

```text
echo Buffet("Fideos")
" devuelve "Fideos"

echo Buffet("Fideos", "Sushi", "Helado", "Tofu", "Mochi")
" devuelve todo lo que le has pasado: "Fideos Sushi Helado Tofu Mochi"
```

El argumento de variable tiene una variable especial más `a:000`. Tiene el valor de todos los argumentos de variable en un formato de lista.

```text
function! Buffet(...)
  return a:000
endfunction

echo Buffet("Fideos")
" devuelve ["Fideos"]

echo Buffet("Fideos", "Sushi", "Helado", "Tofu", "Mochi")
" devuelve ["Fideos", "Sushi", "Helado", "Tofu", "Mochi"]
```

Vamos a volver a escribir la función para utilizar un bucle `for`:

```text
function! Buffet(...)
  let l:comidas = ""
  for elemento_comida in a:000
    let l:comidas .= elemento_comida . " "
  endfor
  return l:comidas
endfunction

echo Buffet("Fideos", "Sushi", "Helado", "Tofu", "Mochi")
" devuelve Fideos Sushi Helado Tofu Mochi
```

## Rango

Puedes definir un _rango_ para una función de Vimscript añadiendo la palabra clave `range` al final de la definición de la función. Un rango de función tiene disponibles dos variables especiales `a:firstline` y `a:lastline`.

```text
function! Desayuno() range
  echo a:firstline
  echo a:lastline
endfunction
```

Si estás en la línea 100 y ejecutas `call Desayuno()`, mostrará 100 tanto para `firstline` como para `lastline`. Si visualmente resaltas \(mediante `v`, `V`, o `Ctrl-V`\) las líneas de la 101 a la 105 y ejecutas `call Desayuno()`, `firstline` mostrará 101 y `lastline` mostrará 105. `firstline` y `lastline` muestran el rango mínimo y máximo de la función que es llamada.

También puedes utilizar `:call` y pasarle un rango. Si ejecutas `:11,20call Desayuno()`, mostrará 11 para `firstline` y 20 para `lastline`.

Te podrías preguntar, "Está bien que una función de Vimscript acepte un rango, pero ¿no puedo obtener el número de línea con `line(".")`? ¿No hará eso lo mismo?"

Buena pregunta. Si esto es a lo que te refieres:

```text
function! Desayuno()
  echo line(".")
endfunction
```

Al llamar a la función `:11,20call Desayuno()` ejecuta la función `Desayuno` 10 veces \(una por cada línea del rango\). Compara eso con la diferencia de si hubieras pasado el argumento del rango `range`:

```text
function! Desayuno() range
  echo line(".")
endfunction
```

Al llamar con `11,20call Desayuno()` esto ejecuta la función `Desayuno` _una vez_.

Si le añades la palabra clave `range` y le pasas un rango numérico \(como `11,20`\) en la llamada `call`, Vim solo ejecutará esa función una vez. Si no pasas un rango `range` y le pasas un rango numérico \(como `11,20`\) en la llamada `call`, Vim ejecuta esa función un número N de veces dependiendo del rango \(en el caso del ejemplo, N = 10\).

## Diccionario

Puedes añadir una función como un elemento diccionario añadiendo la palabra clave `dict` cuando definas la función.

Si tienes una función `SegundoDesayuno` que devuelve cualquier elemento `desayuno` que tengas:

```text
function! SegundoDesayuno() dict
  return self.desayuno
endfunction
```

Vamos a añadir esta función al diccionario `comidas`:

```text
let comidas = {"desayuno": "tortitas", "segundo_desayuno": function("SegundoDesayuno"), "almuerzo": "pasta"}

echo comidas.segundo_desayuno()
" devuelve "tortitas"
```

Con la palabra clave `dict`, la variable clave `self` se refiere al diccionario donde la función está almacenada \(en este caso, el diccionario `comidas`\). La expresión `self.desayuno` es equivalente a `comidas.desayuno`.

Una manera alternativa para añadir una función a un objeto de diccionario es utilizar _namespace_. _namespace_ de forma resumida, es una manera de organizar variables o funciones para evitar conflictos de nombres. Como por ejemplo:

* `comida.arroz`
* `comida.sopa`
* `bebida.agua`
* `bebida.cerveza`

```text
function! comidas.segundo_almuerzo()
  return self.almuerzo
endfunction

echo comidas.segundo_almuerzo()
" devuelve "pasta"
```

Con el _namespace_, no tienes que utilizar la palabra clave `dict`.

## _Funcref_

Una _funcref_ es una referencia a una función. Es uno de los tipos de datos básicos de Vimscript mencionados en el capítulo 24.

La expresión `function("SegundoDesayuno")` anterior es un ejemplo de _funcref_. Vim tiene una función propia `function()` que devuelve un _funcref_ cuando le pasas el nombre de una función \(cadena\).

```text
function! Desayuno(item)
  return "Estoy tomando " . a:item . " para desayunar"
endfunction

let Desayunando = Desayuno
" devuelve error

let Desayunando = function("Desayuno")

echo Desayunando("avena")
" devuelve "Estoy tomando avena para desayunar"

echo Desayunando("tortitas")
" devuelve "Estoy tomando tortitas para desayunar"
```

En Vim, si quieres asignar una función a una variable, no puedes ejecutar la asignación simplemente de esta manera `let MiVariable = MiFuncion`. Necesutas utilizar la función `function()`, así `let MiVariable = function("MiFuncion")`.

Puedes utilizar _funcref_ con mapas y filtros. Ten en cuenta que los mapas y los filtros pasarán un índice como primer argumento y repetirán el valor como segundo argumento.

```text
function! Desayuno(index, item)
  return "Estoy tomando " . a:item . " para desayunar"
endfunction

let desayuno_items = ["tortitas", "galletas", "gofres"]
let primeras_comidas = map(desayuno_items, function("Desayuno"))

for comida in primeras_comidas
  echo comida
endfor
```

## Lambda

Una manera más adecuada de utilizar funciones en mapas y filtros es utilizando la expresión _lambda_ \(a veces también conocida como la función sin nombre\). Por ejemplo:

```text
let Plus = {x,y -> x + y}
echo Plus(1,2)
" devuelve 3

let Tasty = { -> 'tasty'}
echo Tasty()
" devuelve "tasty"
```

Puedes llamar una función desde dentro de la expresión _lambda_:

```text
function! Almuerzo(item)
  return "Estoy tomando " . a:item . " para almorzar"
endfunction

let almuerzo_items = ["sushi", "ramen", "sashimi"]

let comidas_diarias = map(almuerzo_items, {index, item -> Almuerzo(item)})

for comida in comidas_diarias
  echo comida
endfor
```

Si no quieres llamar la función desde dentro de _lambda_, puedes refactorizar el código así:

```text
let comidas_diarias = map(almuerzo_items, {index, item -> "Estoy tomando " . item . " para almorzar"})
```

## Encadenamiento de métodos

Puedes encadenar varias funciones de Vimscript y expresiones _lambda_ secuencialmente con `->`. Ten en mente que `->` debe ir seguido por un nombre de método _sin espacios en blanco_.

```text
Source->Metodo1()->Metodo2()->...->MetodoN()
```

Para convertir un número flotante en número utilizando el método de encadenado, podemos hacer:

```text
echo 3.14->float2nr()
" devuelve 3
```

Veamos un ejemplo más complicado. Supongamos que necesitas convertir a mayúsculas la primera letra de cada elemento de una lista, y después ordenar la lista y después unir la lista para formar una cadena.

```text
function! Mayuscula(palabra)
  return substitute(a:palabra, "\^\.", "\\u&", "g")
endfunction

function! ListaMayuscula(lista_palabra)
  return map(a:lista_palabra, {index, palabra -> Mayuscula(palabra)})
endfunction

let cena_items = ["bruschetta", "antipasto", "calzone"]

echo cena_items->ListaMayuscula()->sort()->join(", ")
" devuelve "Antipasto, Bruschetta, Calzone"
```

Con el método de encadenado, la secuencia es más sencilla de leer y entender. Me basta con echar un vistazo a `cena_items->ListaMayuscula()->sort()->join(", ")` y saber exactamente qué va a suceder.

## Cierre

Cuando defines una variable dentro de una función, esa variable existe dentro de los límites de esa función. A esto se le llama alcance léxico \(lexical scope\).

```text
function! Almuerzo()
  let appetizer = "camarón"

  function! SegundoAlmuerzo()
    return appetizer
  endfunction

  return funcref("SegundoAlmuerzo")
endfunction
```

`appetizer` está definido dentro de la función `Almuerzo`, que devuelve la _funcref_ `SegundoAlmuerzo`. Ten en cuenta que `SegundoAlmuerzo` utiliza `appetizer`, pero en Vimscript, esta no tiene acceso a esa variable. Si intentas ejecutar `echo Desayuno()()`, Vim mostrará un error de variable no definida.

Para solucionar este problema, utiliza la palabra clave `closure`. Volvamos a escribir el código:

```text
function! Deayuno()
  let appetizer = "camarón"

  function! SegundoDesayuno() closure
    return appetizer
  endfunction

  return funcref("SegundoDesayuno")
endfunction
```

Ahora si ejecutas `echo Desayuno()()`, Vim devolverá "camarón".

## Aprende las funciones de Vimscript de la manera más inteligente

En este capítulo, has aprendido la anatomía de una función de Vim. Has aprendido cómo utilizar las distintas palabras clave como `range`, `dict` y `closure` para modificar el comportamiento de una función. También has aprendido cómo utilizar _lambda_ y cómo encadenar múltiples funciones. Las funciones son importantes para crear abstracciones complejas.

Con esto concluye esta guía de Vim. Sin embargo, tu viaje no termina aquí. De hecho, no ha hecho más que comenzar. Deberías tener los suficientes conocimientos para valerte por ti mismo y poder crear tus propios complementos.

¡_Happy Vimming_, amigos y amigas!

