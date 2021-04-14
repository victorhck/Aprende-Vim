# Capítulo 25. Condicionales y bucles en Vimscript

Después de aprender cuales son los tipos básicos de datos que existen en Vimscript, el siguiente paso es aprender cómo combinarlos para empezar a escribir un programa básico. Un programa básico consiste en condicionales y bucles.

En este capítulo, aprenderás cómo utilizar los tipos de datos de Vimscript para escribir esos condicionales y bucles.

## Operadores relacionales

Los operadores relacionales de Vimscript son similares a los que que existen en la mayoría de lenguajes de programación:

```
a == b		igual a
a != b		no igual a
a >  b		mayor quegreater than
a >= b		mayor o igual que
a <  b		menor que
a <= b		menor o igual que
```

Por ejemplo:

```
:echo 5 == 5
:echo 5 != 5
:echo 10 > 5
:echo 10 >= 5
:echo 10 < 5
:echo 5 <= 5
```

Recuerda que las *strings* o cadenas son forzadas a números en una expresión aritmética. Aquí Vim fuerza las cadenas a números en una expresión de igualdad."5foo" es forzado a 5 (verdadero):

```
:echo 5 == "5foo"
" devuelve true
```

También recuerda que si comienzas una cadena con un caracter no numérico como "foo5", la cadena es convertida al número 0 (falso).

```
echo 5 == "foo5"
" devuelve false
```

### Operadores lógicos para cadenas

Vim tiene más operadores relacionales para comparar cadenas:

```
a =~ b
a !~ b
```

Por ejemplo:

```
let str = "abundante desayuno"

echo str =~ "abundante"
" devuelve true

echo str =~ "cena"
" devuelve false

echo str !~ "cena"
" devuelve true
```

El operador `=~` realiza una coincidencia de expresiones regulares contra la cadena dada. En el ejemplo anterior, `str =~ "hearty"` devuelve verdadero porque `str` *contiene* el patrón "abundante". Siempre puedes utilizar `==` o `!=`, pero al usarlos comparará la expresión contra la cadena entera. `=~` o `!~` son unas elecciones más flexibles.

```
echo str == "abundante"
" devuelve false

echo str == "abundante desayuno"
" devuelve true
```

Vamos a probar esta otra. Ten en cuenta la letra mayúscula "A":

```
echo str =~ "Abundante"
" true
```

Devuelve verdadero incluso aunque "Abundante" comience con mayúscula. Interesante... Resulta que mi ajuste de Vim está establecido para ignorar las mayúsculas (`set ignorecase`), así que cuando Vim comprueba la igualdad, utiliza mis ajustes de Vim e ignora esa letra en mayúscula. Si inhabilitara esa opción de ignorar mayúsculas (`set noignorecase`), la comparación ahora devolvería un falso.

```
set noignorecase
echo str =~ "Abundante"
" devuelve false porque tiene en cuenta las mayúsculas

set ignorecase
echo str =~ "Abundante"
" devuelve true porque no tiene en cuenta las mayúsculas
```

Si estás escribiendo un complemento para otras personas, esto puede ser una situación engorrosa. ¿Utiliza esa persona `ignorecase` o `noignorecase`? Realmente *no* quieres forzar a nadie a cambiar sus opciones de ignorar o no las mayúsculas. ¿Qué puedes hacer?

Afortunadamente, Vim tiene un par de operadores que *siempre* puede ignorar o tener en cuenta las mayúsculas y minúsculas. Para siempre tener en cuenta las mayúsculas, añade un `#` al final.

```
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

```
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

```
if {cláusula}
  {alguna expresión}
endif
```

Puedes extender el análisis del caso con `elseif` y `else`.

```
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

```
if has('nvim') || has('patch-8.0.902')
  Plug 'mhinz/vim-signify'
else
  Plug 'mhinz/vim-signify', { 'branch': 'legacy' }
endif
```

## Expresiones ternarias

Vim tiene expresiones ternarias para analizar en una sola línea:

```
{predicado} ? expresión verdadera : expresión falsa
```

Por ejemplo:
```
echo 1 ? "Soy verdadero" : "Soy falso"
```

Como 1 es tomado como verdadero, Vim mostrará el mensaje "Soy verdadero". Supongamos que quieres establecer una condición para configurar `background` a oscuro si estás usando Vim después de cierta hora. Añade esto a tu vimrc:

```
let &background = strftime("%H") < 18 ? "light" : "dark"
```

`&background` es la opción de `'background'` en Vim. `strftime("%H")` devuelve la hora actual. Si todavía no son las 6 PM, utiliza un fondo claro. De lo contrario, utilizará un fondo oscuro.

## Or (O)

El "or" lógico (`||`) funciona como en la mayoría de lenguajes de programación.

```
{Expresión falsa}  || {Expresión falsa}   false
{Expresión falsa}  || {Expresión verdadera}  true
{Expresión verdadera} || {Expresión falsa}   true
{Expresión verdadera} || {Expresión verdadera}  true
```

Vim evalua la expresión y devuelve un 1 (verdadero) o 0 (falso).

```
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

```
let una_docena = 12

echo una_docena || dos_docenas
" devuelve 1

echo dos_docenas || una_docena
" devuelve error
```

Ten en cuenta que `dos_docena` no se ha definido nunca. La expresión `una_docena || dos_docenas` no muestra ningún error porque `una_docena` es evaluada primero y encuentra que es verdadera, por lo que Vim ya no evalua `dos_docenas`.

## And (Y)

El "and" lógico (`&&`) es el complemento del "o" lógico.

```
{Expresión falsa}  && {Expresión falsa}   false
{Expresión falsa}  && {Expresión verdadera}  false
{Expresión verdadera} && {Expresión falsa}   false
{Expresión verdadera} && {Expresión verdadera}  true
```

Por ejemplo:
```
echo 0 && 0
" devuelve 0

echo 0 && 10
" devuelve 0
```

`&&` evalua una expresión hasta que ve la primera expresión falsa. Por ejemplo, si tienes `true && true`, evaluará ambas y devolverá `true`. Si tienes `true && false && true`, evaluará el primer `true` y parará en el primer `false`. No evaluará el tercer `true`.

```
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

El bucle `for` es comunmente utilizado con el tipo de datos listas.

```
let desayunos = ["tortitas", "gofres", "huevos"]

for comida in desayunos
  echo comida
endfor
```

También funciona con listas anidadas:

```
let meals = [["desayuno", "tortitas"], ["almuerzo", "pescado"], ["cena", "pasta"]]

for [tipo_comida, comida] in meals
  echo "Estoy tomando " . comida . " para " . tipo_comida
endfor
```

Técnicamente puedes utilizar el bucle `for` con un diccionario utilizando el método `keys()`.

```
let bebidas = #{desayuno: "leche", almuerzo: "zumo de naranja", cena: "agua"}
for tipo_de_bebida in keys(bebidas)
  echo "Estoy bebiendo " . bebidas[tipo_de_bebida] . " para " . tipo_de_bebida
endfor
```

## While

Another common loop is the `while` loop.

```
let counter = 1
while counter < 5
  echo "Counter is: " . counter
  let counter += 1
endwhile
```

To get the content of the current line to the last line:

```
let current_line = line(".")
let last_line = line("$")

while current_line <= last_line
  echo getline(current_line)
  let current_line += 1
endwhile
```

## Error Handling

Often your program doesn't run the way you expect it to. As a result, it throws you for a loop (pun intended). What you need is a proper error handling.

### Break

When you use `break` inside a `while` or `for` loop, it stops the loop.

To get the texts from the start of the file to the current line, but stop when you see the word "donut":

```
let line = 0
let last_line = line("$")
let total_word = ""

while line <= last_line
  let line += 1
  let line_text = getline(line)
  if line_text =~# "donut"
    break
  endif
  echo line_text
  let total_word .= line_text . " "
endwhile

echo total_word
```

If you have the text:

```
one
two
three
donut
four
five
```

Running the above `while` loop gives "one two three" and not the rest of the text because the loop breaks once it matches "donut".

### Continue

The `continue` method is similar to `break`, where it is invoked during a loop. The difference is that instead of breaking out of the loop, it just skips that current iteration.

Suppose you have the same text but instead of `break`, you use `continue`:

```
let line = 0
let last_line = line("$")
let total_word = ""

while line <= last_line
  let line += 1
  let line_text = getline(line)
  if line_text =~# "donut"
    continue
  endif
  echo line_text
  let total_word .= line_text . " "
endwhile

echo total_word
```

This time it returns `one two three four five`. It skips the line with the word "donut", but the loop continues.

### Try, Finally, And Catch

Vim has a `try`, `finally`, and `catch` to handle errors. To simulate an error, you can use the `throw` command.

```
try
  echo "Try"
  throw "Nope"
endtry
```

Run this. Vim will complain with `"Exception not caught: Nope` error.

Now add a catch block:

```
try
  echo "Try"
  throw "Nope"
catch
  echo "Caught it"
endtry
```

Now there is no longer any error. You should see "Try" and "Caught it" displayed.

Let's remove the `catch` and add a `finally`:

```
try
  echo "Try"
  throw "Nope"
  echo "You won't see me"
finally
  echo "Finally"
endtry
```

Run this. Now Vim displays the error and "Finally".

Let's put all of them together:

```
try
  echo "Try"
  throw "Nope"
catch
  echo "Caught it"
finally
  echo "Finally"
endtry
```

This time Vim displays both "Caught it" and "Finally". No error is displayed because Vim caught it.

Errors come from different places. Another source of error is calling a nonexistent function, like `Nope()` below:

```
try
  echo "Try"
  call Nope()
catch
  echo "Caught it"
finally
  echo "Finally"
endtry
```

The difference between `catch` and `finally` is that `finally` is always run, error or not,  where a catch is only run when your code gets an error.

You can catch specific error with `:catch`. According to `:h :catch`:

```
catch /^Vim:Interrupt$/.             " catch interrupts (CTRL-C)
catch /^Vim\\%((\\a\\+)\\)\\=:E/.    " catch all Vim errors
catch /^Vim\\%((\\a\\+)\\)\\=:/.     " catch errors and interrupts
catch /^Vim(write):/.                " catch all errors in :write
catch /^Vim\\%((\\a\\+)\\)\\=:E123:/ " catch error E123
catch /my-exception/.                " catch user exception
catch /.*/                           " catch everything
catch.                               " same as /.*/
```

Inside a `try` block, an interrupt is considered a catchable error.

```
try
  catch /^Vim:Interrupt$/
  sleep 100
endtry
```

In your vimrc, if you use a custom colorscheme, like [gruvbox](https://github.com/morhetz/gruvbox), and you accidentally delete the colorscheme directory but still have the line `colorscheme gruvbox` in your vimrc, Vim will throw an error when you `source` it. To fix this, I added this in my vimrc:

```
try
  colorscheme gruvbox
catch
  colorscheme default
endtry
```

Now if you `source` vimrc without `gruvbox` directory, Vim will use the `colorscheme default`.

## Learn conditionals the smart way

In the previous chapter, you learned about Vim basic data types. In this chapter, you learned how to combine them to write basic programs using conditionals and loops. These are the building blocks of programming.

Next, let's learn about variable scopes.
