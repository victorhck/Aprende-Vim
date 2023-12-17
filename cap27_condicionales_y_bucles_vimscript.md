# Capítulo 27: Condicionales y bucles en Vimscript

Después de aprender cuales son los tipos básicos de datos que existen en Vimscript, el siguiente paso es aprender cómo combinarlos para empezar a escribir un programa básico. Un programa básico consiste en condicionales y bucles.

En este capítulo, aprenderás cómo utilizar los tipos de datos de Vimscript para escribir esos condicionales y bucles.

## Operadores relacionales

Los operadores relacionales de Vimscript son similares a los que que existen en la mayoría de lenguajes de programación:

```text
a == b        igual a
a != b        no igual a
a >  b        mayor que
a >= b        mayor o igual que
a <  b        menor que
a <= b        menor o igual que
```

Por ejemplo:

```text
:echo 5 == 5
:echo 5 != 5
:echo 10 > 5
:echo 10 >= 5
:echo 10 < 5
:echo 5 <= 5
```

Recuerda que las _strings_ o cadenas son forzadas a números en una expresión aritmética. Aquí Vim fuerza las cadenas a números en una expresión de igualdad."5foo" es forzado a 5 \(verdadero\):

```text
:echo 5 == "5foo"
" devuelve true
```

También recuerda que si comienzas una cadena con un carácter no numérico como "foo5", la cadena es convertida al número 0 \(falso\).

```text
echo 5 == "foo5"
" devuelve false
```

### Operadores lógicos para cadenas

Vim tiene más operadores relacionales para comparar cadenas:

```text
a =~ b
a !~ b
```

Por ejemplo:

```text
let str = "abundante desayuno"

echo str =~ "abundante"
" devuelve true

echo str =~ "cena"
" devuelve false

echo str !~ "cena"
" devuelve true
```

El operador `=~` realiza una coincidencia de expresiones regulares contra la cadena dada. En el ejemplo anterior, `str =~ "hearty"` devuelve verdadero porque `str` _contiene_ el patrón "abundante". Siempre puedes utilizar `==` o `!=`, pero al usarlos comparará la expresión contra la cadena entera. `=~` o `!~` son unas elecciones más flexibles.

```text
echo str == "abundante"
" devuelve false

echo str == "abundante desayuno"
" devuelve true
```

Vamos a probar esta otra. Ten en cuenta la letra mayúscula "A":

```text
echo str =~ "Abundante"
" true
```

Devuelve verdadero incluso aunque "Abundante" comience con mayúscula. Interesante... Resulta que mi ajuste de Vim está establecido para ignorar las mayúsculas \(`set ignorecase`\), así que cuando Vim comprueba la igualdad, utiliza mis ajustes de Vim e ignora esa letra en mayúscula. Si inhabilitara esa opción de ignorar mayúsculas \(`set noignorecase`\), la comparación ahora devolvería un falso.

```text
set noignorecase
echo str =~ "Abundante"
" devuelve false porque tiene en cuenta las mayúsculas

set ignorecase
echo str =~ "Abundante"
" devuelve true porque no tiene en cuenta las mayúsculas
```

Si estás escribiendo un complemento para otras personas, esto puede ser una situación engorrosa. ¿Utiliza esa persona `ignorecase` o `noignorecase`? Realmente _no_ quieres forzar a nadie a cambiar sus opciones de ignorar o no las mayúsculas. ¿Qué puedes hacer?

Afortunadamente, Vim tiene un par de operadores que _siempre_ puede ignorar o tener en cuenta las mayúsculas y minúsculas. Para siempre tener en cuenta las mayúsculas, añade un `#` al final.

```text
set ignorecase
echo str =~# "abundante"
" devuelve true

echo str =~# "AbundaNTe"
" devuelve false

set noignorecase
echo str =~# "abundante"
" true

echo str =~# "AbundaNTe"
" false

echo str !~# "AbundaNTe"
" true
```

Para siempre ignorar las mayúsculas y minúsculas al comparar, añade `?`:

```text
set ignorecase
echo str =~? "abundante"
" true

echo str =~? "AbundaNTe"
" true

set noignorecase
echo str =~? "abundante"
" true

echo str =~? "AbundaNTe"
" true

echo str !~? "AbundaNTe"
" false
```

Yo prefiero utilizar `#` para siempre tener en cuenta las mayúsculas y minúsculas y siempre ir sobre seguro.

## If

Ahora que ya has visto las expresiones de igualdad de Vim, vamos a tratar un operador condicional fundamental, la sentencia `if`.

Como mínimo, la sintaxis es:

```text
if {cláusula}
  {alguna expresión}
endif
```

Puedes extender el análisis del caso con `elseif` y `else`.

```text
if {predicado1}
  {expresión1}
elseif {predicado2}
  {expresión2}
elseif {predicado3}
  {expresión3}
else
  {expresión4}
endif
```

Por ejemplo, el complemento [vim-signify](https://github.com/mhinz/vim-signify) utiliza un método diferente de instalación dependiendo de tus ajustes de Vim. Debajo está la instrucción de instalación copiada desde su `readme`, utilizando la instrucción `if`:

```text
if has('nvim') || has('patch-8.0.902')
  Plug 'mhinz/vim-signify'
else
  Plug 'mhinz/vim-signify', { 'branch': 'legacy' }
endif
```

## Expresiones ternarias

Vim tiene expresiones ternarias para analizar en una sola línea:

```text
{predicado} ? expresión verdadera : expresión falsa
```

Por ejemplo:

```text
echo 1 ? "Soy verdadero" : "Soy falso"
```

Como 1 es tomado como verdadero, Vim mostrará el mensaje "Soy verdadero". Supongamos que quieres establecer una condición para configurar `background` a oscuro si estás usando Vim después de cierta hora. Añade esto a tu vimrc:

```text
let &background = strftime("%H") < 18 ? "light" : "dark"
```

`&background` es la opción de `'background'` en Vim. `strftime("%H")` devuelve la hora actual. Si todavía no son las 6 PM, utiliza un fondo claro. De lo contrario, utilizará un fondo oscuro.

## Or \(O\)

El "or" lógico \(`||`\) funciona como en la mayoría de lenguajes de programación.

```text
{Expresión falsa}  || {Expresión falsa}   false
{Expresión falsa}  || {Expresión verdadera}  true
{Expresión verdadera} || {Expresión falsa}   true
{Expresión verdadera} || {Expresión verdadera}  true
```

Vim evalúa la expresión y devuelve un 1 \(verdadero\) o 0 \(falso\).

```text
echo 5 || 0
" devuelve 1

echo 5 || 5
" devuelve 1

echo 0 || 0
" devuelve 0

echo "foo5" || "foo5"
" devuelve 0

echo "5foo" || "foo5"
" devuelve 1
```

Dentro del `or` la primera expresión se evalúa y si es verdadera, la expresión siguiente no será evaluada.

```text
let una_docena = 12

echo una_docena || dos_docenas
" devuelve 1

echo dos_docenas || una_docena
" devuelve error
```

Ten en cuenta que `dos_docena` no se ha definido nunca. La expresión `una_docena || dos_docenas` no muestra ningún error porque `una_docena` es evaluada primero y encuentra que es verdadera, por lo que Vim ya no evalúa `dos_docenas`.

## And \(Y\)

El "and" lógico \(`&&`\) es el complemento del "o" lógico.

```text
{Expresión falsa}  && {Expresión falsa}   false
{Expresión falsa}  && {Expresión verdadera}  false
{Expresión verdadera} && {Expresión falsa}   false
{Expresión verdadera} && {Expresión verdadera}  true
```

Por ejemplo:

```text
echo 0 && 0
" devuelve 0

echo 0 && 10
" devuelve 0
```

`&&` evalúa una expresión hasta que ve la primera expresión falsa. Por ejemplo, si tienes `true && true`, evaluará ambas y devolverá `true`. Si tienes `true && false && true`, evaluará el primer `true` y parará en el primer `false`. No evaluará el tercer `true`.

```text
let una_docena = 12
echo una_docena && 10
" devuelve 1

echo una_docena && v:false
" devuelve 0

echo una_docena && dos_docenas
" devuelve error

echo exists("una_docena") && una_docena == 12
" devuelve 1
```

## For

El bucle `for` es comúnmente utilizado con el tipo de datos listas.

```text
let desayunos = ["tortitas", "gofres", "huevos"]

for comida in desayunos
  echo comida
endfor
```

También funciona con listas anidadas:

```text
let meals = [["desayuno", "tortitas"], ["almuerzo", "pescado"], ["cena", "pasta"]]

for [tipo_comida, comida] in meals
  echo "Estoy tomando " . comida . " para " . tipo_comida
endfor
```

Técnicamente puedes utilizar el bucle `for` con un diccionario utilizando el método `keys()`.

```text
let bebidas = #{desayuno: "leche", almuerzo: "zumo de naranja", cena: "agua"}
for tipo_de_bebida in keys(bebidas)
  echo "Estoy bebiendo " . bebidas[tipo_de_bebida] . " para " . tipo_de_bebida
endfor
```

## While

Otro bucle común es el bucle `while`.

```text
let contador = 1
while contador < 5
  echo "El valor del contador es: " . contador
  let contador += 1
endwhile
```

Otro ejemplo, para obtener el contenido desde la línea actual hasta la última línea:

```text
let linea_actual = line(".")
let ultima_linea = line("$")

while linea_actual <= ultima_linea
  echo getline(linea_actual)
  let linea_actual += 1
endwhile
```

## Gestión del error

A menudo tu programa no funciona en la manera que esperas. Como resultado, el programa te lleva a un bucle \(valga el juego de palabras\). Lo que necesitas es una gestión del error adecuada.

### Break

Cuando utilizas `break` dentro de un bucle `while` o `for`, esto detiene el bucle.

Veamos un ejemplo, modificando un poco el anterior. Para obtener los textos desde el inicio del archivo hasta la línea actual, pero parar el bucle cuando encuentre la palabra "donut":

```text
let linea = 0
let ultima linea = line("$")
let total_palabras = ""

while linea <= ultima_linea
  let linea += 1
  let texto_linea = getline(linea)
  if texto_linea =~# "donut"
    break
  endif
  echo texto_linea
  let total_palabras .= texto_linea . " "
endwhile

echo total_palabras
```

Si tienes el siguiente texto:

```text
uno
dos
tres
donut
cuatro
cinco
```

Al ejecutar el bucle `while` anterior, este mostrará "uno dos tres" y no mostrará el resto de texto, ya que el bucle se detiene por el comando `break` cuando encuentra en esa lista la palabra "donut".

### Continue

El método `continue` es similar a `break`, cuando es invocado en un bucle. La diferencia está en que en vez de detener el bucle, simplemente omite la evaluación actual.

Supongamos que tenemos el mismo texto que antes, pero en vez de `break`, utilizamos `continue`:

```text
let linea = 0
let ultima_linea = line("$")
let total_palabras = ""

while linea <= ultima_linea
  let linea += 1
  let texto_linea = getline(linea)
  if texto_linea =~# "donut"
    continue
  endif
  echo texto_linea
  let total_palabras .= linea_texto . " "
endwhile

echo total_palabras
```

Esta vez mostrará `uno dos tres cuatro cinco`. Ahora salta la línea que contiene la palabra "donut", pero la ejecución del bucle continua.

### Try, Finally y Catch

En Vim existe `try`, `finally` y `catch` para la gestión de errores. Para simular un error, puedes utilizar el comando `throw`.

```text
try
  echo "Try"
  throw "Nope"
endtry
```

Ejecuta esto. Vim mostrará un error `"Exception not caught: Nope`.

Ahora añade un bloque `catch`:

```text
try
  echo "Try"
  throw "Nope"
catch
  echo "Pillado"
endtry
```

Ahora ya no habrá un error. Deberías ver "Try" y se mostrará "Pillado".

Vamos a eliminar `catch` y añadir `finally`:

```text
try
  echo "Try"
  throw "Nope"
  echo "No me verás"
finally
  echo "Finalmente"
endtry
```

Ejecuta esto. Ahora Vim muestra el error y el texto "Finalmente".

Vamos a poner todo junto:

```text
try
  echo "Try"
  throw "Nope"
catch
  echo "Pillado"
finally
  echo "Finalmente"
endtry
```

Esta vez Vim muestra tanto "Pillado" y "Finalmente". No se muestra el error por que Vim lo ha "pillado". No error is displayed because Vim caught it.

Los errores provienen de diferentes lugares. Otra fuente de error es una llamada a una función que no existe, como `Nada()` que veremos a continuación:

```text
try
  echo "Try"
  call Nada()
catch
  echo "Pillado"
finally
  echo "Finalmente"
endtry
```

La diferencia entre `catch` y `finally` es que `finally` siempre se ejecuta, haya error o no. Mientras que `catch` solo se ejecuta cuando tu código tiene algún error.

Puedes detectar un error específico con `:catch`. De acuerdo a `:h :catch`:

```text
catch /^Vim:Interrupt$/.             " detecta interrupciones (CTRL-C)
catch /^Vim\\%((\\a\\+)\\)\\=:E/.    " detecta todos los errores de Vim
catch /^Vim\\%((\\a\\+)\\)\\=:/.     " detecta errores e interrupciones
catch /^Vim(write):/.                " detecta todos los errores en :write
catch /^Vim\\%((\\a\\+)\\)\\=:E123:/ " detecta el error E123
catch /my-exception/.                " detecta excepciones de usuario
catch /.*/                           " detecta todo
catch.                               " similar a /.*/
```

Dentro de un bloque `try`, una interrupción es considerada un error que se puede detectar.

```text
try
  catch /^Vim:Interrupt$/
  sleep 100
endtry
```

En tu vimrc, si utilizas un esquema de color personalizado, como [gruvbox](https://github.com/morhetz/gruvbox), y de manera accidental eliminas el esquema de color del directorio, pero todavía tienes la línea `colorscheme gruvbox` en tu vimrc, Vim mostrará un error al ejecutar `source` para volver a tomar en cuenta las modificaciones del vimrc. Para solucionar esto, he añadido lo siguiente a mi vimrc:

```text
try
  colorscheme gruvbox
catch
  colorscheme default
endtry
```

Ahora al ejecutar `source` sin el directorio `gruvbox`, Vim utilizará `colorscheme default` el esquema de color predeterminado.

## Aprende condicionales de la manera más inteligente

En los capítulos previos, aprendiste sobre los tipos de datos básicos de Vim. En este capítulo, has aprendido cómo combinarlos para escribir programas básicos utilizando condicionales y bucles. Estos están construidos con bloques de programación.

A continuación, vamos a aprender sobre el alcance de las variables.

