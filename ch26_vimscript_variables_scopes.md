# Capítulo 26. Variables de Vimscript y su ámbito

Antes de adentrarnos en las funciones de Vimscript, vamos a aprender las diferentes fuentes y ámbitos en los que operan las variables en Vim.

## Variables mutables e inmutables

Puedes asignar un valor a una variable en Vim con `let`:

```
let tortitas = "tortitas"
```

Después puedes llamar a la variable en cualquier momento.

```
echo tortitas
" devuelve "tortitas"
```

`let` es mutable, esto significa que puedes cambiar el valor de la variable en cualquier momento en el futuro.

```
let tortitas = "tortitas"
let tortitas = "no gofres"

echo tortitas
" devuelve "no gofres"
```

Ten en cuenta que cuando quieres cambiar el valor de una variable, necesitas utilizar `let`.

```
let bebida = "leche"

bebida = "zumo de naranja"
" lanza un error
```

Puedes definir variables inmutables con `const`. Al ser inmutables, una vez que a la variable se le asigna un valor, ya no puedes reasignarle un valor diferente más adelante.

```
const gofres = "gofres"
const gofres = "tortitas"
" lanza un error
```

## Fuentes de variable

Hay tres fuentes de las que obtener los valores de variables: variables de entorno, variables de opciones y variables de registro.

### Variables de entorno

Vim puede acceder a las variables de entorno de tu terminal. Por ejemplo, si tienes la variable `SHELL` de entorno en tu terminal, puedes acceder a ella desde Vim mediante:

```
echo $SHELL
" devuelve el valor de $SHELL. E mi caso devuelve /bin/bash
```

### Variables de opciones

Puedes acceder a las opciones de Vim con `&` (estos son ajustes a los que accedes con `set`).

Por ejemplo, para ver qué fondo utiliza Vim, puedes ejecutar:

```
echo &background
" devuelve un valor que puede ser "light" o "dark" (claro u oscuro)
```

De manera alternativa, siempre puedes ejecutar `set background?` para ver el valor de la opción `background`.

### Variables de registro

Puedes acceder a los registros de Vim (ver el capítulo 08) mediante `@`.

Supongamos que el valor "chocolate" está ya guardado en el registro a. Para acceder a el, puedes utilizar `@a`. También puedes actualizarlo con `let`.

```
echo @a
" devuelve chocolate

let @a .= " donut"

echo @a
" devuelve "chocolate donut"
```

Ahora cuando pegues el contenido del registro a (`"ap`), este mostrará el contenido que es "chocolate donut". El operador `.=` une dos cadenas. La expresión `let @a .= " donut"` es lo mismo que `let @a = @a . " donut"`

## Ámbito de las variables 

Hay 9 diferentes ámbitos en los que pueden operar las variables en Vim. Puedes reconocerlos porque les antecede una letra:

```
g:           Variable global 
{nothing}    Variable global 
b:           Variable de buffer local
w:           Variable de ventana local
t:           Variable de pestaña local
s:           Variable procedente de Vimscript 
l:           Variable de función local
a:           Variable de parámetro de función formal
v:           Variable propia de Vim
```

### Variable global 

Cuando estás declarando una variable "normal":

```
let tortita = "tortita"
```

`tortita` es de hecho una variable global. Cuando defines una variable global, puedes llamarla desde cualquier lugar.

Al anteponer `g:` a la declaración de una variable, también estás creando una variable global:

```
let g:gofre = "gofre"
```

En este caso tanto `pancake` como `g:waffle` tienen el mismo ámbito de trabajo, son globales. Puedes llamar a cada una de ellas con o sin `g:`.

```
echo tortita
" devuelve "tortita"

echo g:tortita
" devuelve "tortita"

echo gofre
" devuelve "gofre"

echo g:gofre
" devuelve "gofre"
```

### Variable de buffer 

Una variable precedida de `b:` es una variable de *buffer*. Una variable de *buffer* es una variable que es local al *buffer* actual (ver capítulo 02). Si tienes múltiples *buffers* abiertos, cada *buffer* tendrá su propia lista separada de variables de *buffer*.

En el *buffer* 1:

```
const b:donut = "chocolate donut"
```

En el *buffer* 2:

```
const b:donut = "frambuesa donut"
```

Si ejecutas `echo b:donut` en el *buffer* 1, mostrará "chocolate donut". Si ejecutas lo mismo pero ahora en el *buffer* 2, en este caso se mostrará "frambuesa donut".

Como nota complementaria, Vim tiene una variable de *buffer* "especial" `b:changedtick` que guarda el número acumulado de todos los cambios realizados en el *buffer* actual.

1. Ejecuta `echo b:changedtick` y toma nota del número que devuelve..
2. Haz cambios en Vim.
3. Vuelve a ejecutar `echo b:changedtick` y comprueba el número que devuelve ahora.

### Variable de ventana

Una variable precedida con `w:` es una variable cuyo ámbito de trabajo es la ventana. Existe solo en esa ventana en la que se ha declarado.

En la ventana 1:

```
const w:donut = "chocolate donut"
```

En la ventana 2:

```
const w:donut = "frambuesa donut"
```

En cada ventana podrás ejecutar `echo w:donut` y verás que en cada una devolverá valores únicos.

### Variable de pestaña

Una variable precdida de `t:` es una variable propia de esa pestaña. Solo existe en esa pestaña.

En la pestaña 1:

```
const t:donut = "chocolate donut"
```

En la pestaña 2:

```
const t:donut = "azucarado donut"
```

En cada pestaña, podrás ejecutar `echo t:donut` para obtener valores distintos en cada una de ella.

### Script variable

A variable preceded with `s:` is a script variable. These variables can only be accessed from inside that script.

If you have an arbitrary file `dozen.vim` and inside it you have:

```
let s:dozen = 12

function Consume()
  let s:dozen -= 1
  echo s:dozen " is left"
endfunction
```

Source the file with `:source dozen.vim`. Now call the `Consume` function:

```
:call Consume()
" devuelve "11 is left"

:call Consume()
" devuelve "10 is left"

:echo s:dozen
" Undefined variable error
```

When you call `Consume`, you see it decrements the `s:dozen` value as expected. When you try to get `s:dozen` value directly, Vim won't find it because you are out of scope. `s:dozen` is only accessible from inside `dozen.vim`.

Each time you source the `dozen.vim` file, it resets the `s:dozen` counter. If you are in the middle of decrementing `s:dozen` value and you run `:source dozen.vim`, the counter resets back to 12. This can be a problem for unsuspecting users. To fix this issue, refactor the code:

```
if !exists("s:dozen")
  let s:dozen = 12
endif

function Consume()
  let s:dozen -= 1
  echo s:dozen
endfunction
```

Now when you source `dozen.vim` while in the middle of decrementing, Vim reads `!exists("s:dozen")`, finds that it is true, and doesn't reset the value back to 12.

### Function Local And Function Formal Parameter variable

Both the function local variable (`l:`) and the function formal variable (`a:`) will be covered in the next chapter.

### Built-in Vim Variables

A variable prepended with `v:` is a special built-in Vim variable. You cannot define these variables. You have seen some of them already.
- `v:version` tells you what Vim version you are using.
- `v:key` contains the current item value when iterating through a dictionary.
- `v:val` contains the current item value when running a `map()` or `filter()` operation.
- `v:true`, `v:false`, `v:null`, and `v:none` are special data types.

There are other variables. For a list of Vim built-in variables, check out `:h vim-variable` or `:h v:`.

## Using Vim Variable Scopes The Smart Way

Being able to quickly access environment, option, and register variables give you a broad flexibility to customize your editor and terminal environment. You also learned that Vim has 9 different variable scopes, each existing under a certain constraints. You can take advantage of these unique variable types to decouple your program.

You made it this far. You learned about data types, means of combinations, and variable scopes. Only one thing is left: functions.
