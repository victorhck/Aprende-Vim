# Capítulo 28: Variables de Vimscript y su ámbito

Antes de adentrarnos en las funciones de Vimscript, vamos a aprender las diferentes fuentes y ámbitos en los que operan las variables en Vim.

## Variables mutables e inmutables

Puedes asignar un valor a una variable en Vim con `let`:

```text
let tortitas = "tortitas"
```

Después puedes llamar a la variable en cualquier momento.

```text
echo tortitas
" devuelve "tortitas"
```

`let` es mutable, esto significa que puedes cambiar el valor de la variable en cualquier momento en el futuro.

```text
let tortitas = "tortitas"
let tortitas = "no gofres"

echo tortitas
" devuelve "no gofres"
```

Ten en cuenta que cuando quieres cambiar el valor de una variable, necesitas utilizar `let`.

```text
let bebida = "leche"

bebida = "zumo de naranja"
" lanza un error
```

Puedes definir variables inmutables con `const`. Al ser inmutables, una vez que a la variable se le asigna un valor, ya no puedes reasignarle un valor diferente más adelante.

```text
const gofres = "gofres"
const gofres = "tortitas"
" lanza un error
```

## Fuentes de variable

Hay tres fuentes de las que obtener los valores de variables: variables de entorno, variables de opciones y variables de registro.

### Variables de entorno

Vim puede acceder a las variables de entorno de tu terminal. Por ejemplo, si tienes la variable `SHELL` de entorno en tu terminal, puedes acceder a ella desde Vim mediante:

```text
echo $SHELL
" devuelve el valor de $SHELL. E mi caso devuelve /bin/bash
```

### Variables de opciones

Puedes acceder a las opciones de Vim con `&` \(estos son ajustes a los que accedes con `set`\).

Por ejemplo, para ver qué fondo utiliza Vim, puedes ejecutar:

```text
echo &background
" devuelve un valor que puede ser "light" o "dark" (claro u oscuro)
```

De manera alternativa, siempre puedes ejecutar `set background?` para ver el valor de la opción `background`.

### Variables de registro

Puedes acceder a los registros de Vim \(ver el capítulo 08\) mediante `@`.

Supongamos que el valor "chocolate" está ya guardado en el registro a. Para acceder a el, puedes utilizar `@a`. También puedes actualizarlo con `let`.

```text
echo @a
" devuelve chocolate

let @a .= " donut"

echo @a
" devuelve "chocolate donut"
```

Ahora cuando pegues el contenido del registro a \(`"ap`\), este mostrará el contenido que es "chocolate donut". El operador `.=` une dos cadenas. La expresión `let @a .= " donut"` es lo mismo que `let @a = @a . " donut"`

## Ámbito de las variables

Hay 9 diferentes ámbitos en los que pueden operar las variables en Vim. Puedes reconocerlos porque les antecede una letra:

```text
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

```text
let tortita = "tortita"
```

`tortita` es de hecho una variable global. Cuando defines una variable global, puedes llamarla desde cualquier lugar.

Al anteponer `g:` a la declaración de una variable, también estás creando una variable global:

```text
let g:gofre = "gofre"
```

En este caso tanto `pancake` como `g:waffle` tienen el mismo ámbito de trabajo, son globales. Puedes llamar a cada una de ellas con o sin `g:`.

```text
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

Una variable precedida de `b:` es una variable de _buffer_. Una variable de _buffer_ es una variable que es local al _buffer_ actual \(ver capítulo 02\). Si tienes múltiples _buffers_ abiertos, cada _buffer_ tendrá su propia lista separada de variables de _buffer_.

En el _buffer_ 1:

```text
const b:donut = "chocolate donut"
```

En el _buffer_ 2:

```text
const b:donut = "frambuesa donut"
```

Si ejecutas `echo b:donut` en el _buffer_ 1, mostrará "chocolate donut". Si ejecutas lo mismo pero ahora en el _buffer_ 2, en este caso se mostrará "frambuesa donut".

Como nota complementaria, Vim tiene una variable de _buffer_ "especial" `b:changedtick` que guarda el número acumulado de todos los cambios realizados en el _buffer_ actual.

1. Ejecuta `echo b:changedtick` y toma nota del número que devuelve..
2. Haz cambios en Vim.
3. Vuelve a ejecutar `echo b:changedtick` y comprueba el número que devuelve ahora.

### Variable de ventana

Una variable precedida con `w:` es una variable cuyo ámbito de trabajo es la ventana. Existe solo en esa ventana en la que se ha declarado.

En la ventana 1:

```text
const w:donut = "chocolate donut"
```

En la ventana 2:

```text
const w:donut = "frambuesa donut"
```

En cada ventana podrás ejecutar `echo w:donut` y verás que en cada una devolverá valores únicos.

### Variable de pestaña

Una variable precedida de `t:` es una variable propia de esa pestaña. Solo existe en esa pestaña.

En la pestaña 1:

```text
const t:donut = "chocolate donut"
```

En la pestaña 2:

```text
const t:donut = "azucarado donut"
```

En cada pestaña, podrás ejecutar `echo t:donut` para obtener valores distintos en cada una de ella.

### Variable de script

Una variable precedida de `s:` es una variable de script. A estas variables solo se puede acceder desde dentro de ese script.

Si tienes un archivo llamado `docena.vim` y dentro del archivo tienes lo siguiente:

```text
let s:docena = 12

function Consume()
  let s:docena -= 1
  echo s:docena " quedan"
endfunction
```

Haz que Vim tenga en cuenta este archivo mediante `:source dozen.vim`. Ahora llama a la función `Consume`:

```text
:call Consume()
" devuelve "11 quedan"

:call Consume()
" devuelve "10 quedan"

:echo s:dozen
" Undefined variable error
```

Cuando llamas a la función `Consume`, ves que se decrementa el valor de la variable `s:dozen` tal como se espera. Cuando tratas de obtener directamente el valor de `s:dozen`, Vim no la encontrará porque la estás invocando fuera de su ámbito. `s:dozen` solo es accesible desde dentro de `dozen.vim`.

Cada vez que vuelvas a hacer que Vim tenga en cuenta el archivo `dozen.vim` con el comando `source`, Vim resetea el contado de `s:dozen`. Si estás en la mitad de la acción de decrementar el valor de `s:dozen` y ejecutas `:source dozen.vim`, el contador volverá a poner su valor a 12. Esto puede ser un problema para los usuarios menos avezados. Para solucionar este problema, vamos a modificar el código:

```text
if !exists("s:docena")
  let s:docena = 12
endif

function Consume()
  let s:docena -= 1
  echo s:docena
endfunction
```

Ahora cuando volvemos a ejecutar el comando `source` con el archivo `dozen.vim` mientras estemos a la mitad del proceso de decremento de la variable, Vim lee `!exists("s:docena")`, encuentra que esto es verdad y no resetea el valor de nuevo a 12 manteniendo el valor que tenía.

### Variables de función local y función formal de parámetro

Tanto la variable de función local \(`l:`\) como la variable de función formal \(`a:`\) serán tratadas en el próximo capítulo.

### Variables propias de Vim

Una variable precedida con `v:` es una variable especial propia de Vim. No puedes definir estas variables, solo utilizarlas. Ya hemos visto algunas de estas.

* `v:version` muestra la versión de Vim que estás utilizando.
* `v:key` contiene el valor del elemento actual cuando interactúas con un diccionario.
* `v:val` contiene el valor del elemento actual cuando ejecutas una operación `map()` o `filter()`.
* `v:true`, `v:false`, `v:null` y `v:none` son tipos de datos especiales.

Hay otras variables. Para consultar una lista de todas las variables propias de Vim, echa un vistazo a `:h vim-variable` o `:h v:`.

## Utilizando los ámbitos de las variables de Vim de la manera más inteligente

Ser capaz de acceder rápidamente a variables de entorno, opción y registros te da una mayor flexibilidad para personalizar tu editor y tu entorno de la terminal. También has aprendido que Vim tiene 9 ámbitos diferentes para las variables, cada una existe bajo ciertas condiciones. Puedes utilizar esto en tu beneficio usando estas variables únicas para usarlas en tu programa.

Has llegado muy lejos. Has aprendido sobre los tipos de datos, el significado de las combinaciones y el ámbito de las variables. Solo queda una cosa: funciones.

