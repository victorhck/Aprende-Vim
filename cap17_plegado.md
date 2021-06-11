# Capítulo 17: Plegado de texto \(Fold\)

Cuando lees un archivo, a menudo hay mucho texto irrelevante que te impide entender lo que realiza ese archivo. Para ocultar ese _ruido_ innecesario, utiliza el plegado de texto que ofrece Vim \(Vim Fold\).

En este capítulo, aprenderás las diferentes formas de plegar y desplegar partes de un archivo.

## Plegado manual

Imagina que estás doblando una hoja de papel para cubrir una parte de texto. El texto actual no ha desaparecido, todavía sigue ahí. El plegado de Vim funciona de una manera similar. Este pliega una parte del texto, evitando mostrarlo pero sin eliminarlo del archivo.

El operador para el plegado es `z` \(cuando pliegas un papel, este tiene forma de la letra z\).

Supón que tienes el siguiente texto:

```text
Fold me
Hold me
```

Con el cursor situado en la primera línea, escribe `zfj`. Vim plegará las dos líneas en una sola. Deberías ver algo similar a esto:

```text
+-- 2 lines: Fold me -----
```

Vamos a ver en detalle el comando:

* `zf` es el operador del operador del plegado.
* `j` es el indicador de movimiento para el operador del plegado.

Puedes abrir un texto plegado con `zo`. Para cerrarlo utiliza `zc`.

El comando para plegar texto en Vim es un operador, por lo que cumple las reglas gramaticales de Vim \(`verbo + sustantivo`\). Puedes pasarle al operador de plegado un movimiento o un objeto de texto. Para plegar el párrafo en el que se encuentra el cursor, ejecuta `zfip`. Para plegar desde la posición actual hasta el final del archivo, ejecuta `zfG`. Para plegar los textos entre `{` y `}`, ejecuta `zfa{`.

Puedes plegar texto desde el modo visual. Selecciona el área que quieres plegar \(puedes hacerlo con `v`, `V` o `Ctrl-v`\), y después ejecuta `zf`.

También puedes ejecutar un plegado de texto desde la línea de comandos con el comando `:fold`. Para plegar la línea actual y la línea posterior, ejecuta:

```text
:,+1fold
```

`,+1` es el rango. Si no le pasas parámetros al rango, el comando de manera predeterminada lo aplica a la línea actual. `+1` es el indicador de rango para la línea siguiente. Para plegar las líneas de la 5 a la 10, ejecuta `:5,10fold`. Para plegar desde la posición actual hasta el final de la línea, ejecuta `:,$fold`.

Hay muchos otros comandos para gestionar el plegado de texto. Creo que son muchos para recordar cuando estás comenzando. Los más útiles son:

* `zR` para abrir todos los plegados.
* `zM` para cerrar todos los plegados.
* `za` para alternar el estado en el que se encuentra un plegado.

Puedes ejecutar `zR` y `zM` estando en cualquier línea, pero `za` solo funciona cuando estás en una línea que pertenece a un plegado de texto. Para aprender más sobre los comandos de los plegados de texto, echa un vistazo a `:h fold-commands`.

## Diferentes métodos de plegado de texto

La sección anterior trata sobre el plegado de texto manual de Vim. Hay seis métodos de plegado de texto en Vim:

1. Manual \(Manual\)
2. Sangría \(Indent\)
3. Expresión \(Expression\) 
4. Sintaxis \(Syntax\) 
5. Diferencia \(Diff\) 
6. Marcador \(Marker\)

Para ver el método de plegado de texto que estás utilizando actualmente, ejecuta `:set foldmethod?`. De manera predeterminada, Vim utiliza el método `manual`.

En el resto del capítulo, aprenderás los otros cinco métodos. Vamos a empezar con el plegado por sangría \(o en inglés _indent_\).

## Plegado por sangría \(_Indent Fold_\)

Para utilizar el plegado de texto por sangría de margen, cambia el método de plegado `'foldmethod'` a _indent_. Ejecuta:

```text
:set foldmethod=indent
```

Supongamos que tenemos este texto:

```text
Uno
  Dos
  Dos de nuevo
```

Si ejecutas `:set foldmethod=indent`, verás algo así:

```text
Uno
+-- 2 lines: Dos -----
```

Con este método de plegado de texto, Vim mira cuantos espacios tiene cada línea al comienzo y lo compara con la opción `'shiftwidth'` para determinar cómo lo puede plegar. `'shiftwidth'` devuelve el número de espacios requeridos para cada paso del sangrado de línea. Si ejecutas:

```text
:set shiftwidth?
```

El valor predeterminado de Vim de `'shiftwidth'` es 2. En el texto del ejemplo anterior, hay dos espacios al comienzo de la línea y los textos "Dos" y "Dos de nuevo". Cuando Vim comprueba el número de espacios al inicio de la línea y el valor 2 de `'shiftwidth'`, Vim considera que las líneas tienen que ser plegadas en un primer nivel.

Supón esta vez que solo tienes un espacio entre el comienzo de la línea y el texto:

```text
Uno
 Dos
 Dos de nuevo
```

Ahora mismo si ejecutas `:set foldmethod=indent`, Vim no plegará el texto sangrado debido a que no tiene el margen indicado en cada línea. Un solo espacio no es considerado dentro de sangrado de margen. Sin embargo, si cambias el valor de la variable `'shiftwidth'` a 1:

```text
:set shiftwidth=1
```

El texto ahora sí se podrá plegar, ya que entra dentro de los valores de sangrado de márgenes.

Devuelve de nuevo el valor de `shiftwidth` a 2 y los espacios de las líneas de nuevo a dos. Y además añade otras dos líneas de texto, como en el ejemplo:

```text
Uno
  Dos
  Dos de nuevo
    Tres
    Tres de nuevo
```

Ejecuta el plegado \(`zM`\), y verás:

```text
Uno
+-- 4 lines: Dos -----
```

Despliega las líneas plegadas \(`zR`\), y después sitúa el cursor sobre la palabra "Tres" y alterna el estado del plegado del texto \(`za`\):

```text
Uno
  Dos
  Dos de nuevo
+-- 2 lines: Tres -----
```

¿Qué es esto? ¿Un plegado dentro de otro plegado de texto?

Los plegados anidados son válidos. El texto "Dos" y "Dos de nuevo" están plegados en un primer nivel. El texto "Tres" y "Tres de nuevo" están plegados en un segundo nivel. Si tienes un texto plegado con un nivel de plegado mayor con texto plegado dentro, tienes múltiples capas de plegados.

## Plegado por expresión \(_Expression Fold_\)

El plegado por expresión te permite definir una expresión que encaje en un plegado de texto. Después de definir las expresiones que se pueden plegar, Vim busca cada línea el valor de `'foldexpr'`. Esta es la variable que tienes que configurar para devolver el valor adecuado. Si `'foldexpr'` devuelve un valor 0, entonces la línea no puede meterse en un plegado. Si devuelve un valor 1, entonces esa línea tiene un nivel de plegado de 1. Si devuelve un 2, entonces esa línea tiene un nivel de plegado de 2. Hay más valores que números, pero no voy a repasarlos todos. Si tienes curiosidad, echa un vistazo a `:h fold-expr`.

Primero, vamos a cambiar el método de plegado:

```text
:set foldmethod=expr
```

Supongamos que tienes una lista de alimentos para desayunar y quieres plegar todos los elementos de desayuno que comiencen con "c":

```text
donut
churros
croissant
chocolate
salmon
huevos revueltos
```

Ahora, cambia `foldexpr` para capturar todas las expresiones que comiencen con "c":

```text
:set foldexpr=getline(v:lnum)[0]==\\"c\\"
```

La expresión anterior parece un poco complicada. Vamos a dividirla y explicarla por partes:

* `:set foldexpr` establece la opción `'foldexpr'` para que acepte expresiones personalizadas.
* `getline()` es una función de Vimscript que devuelve el contenido de cualquier línea dada. Si ejecutas `:echo getline(5)`, esto devolverá el contenido de la línea 5.
* `v:lnum` es una variable especial de Vim para la expresión `'foldexpr'`. Vim busca cada línea y en cada momento almacena cada número de línea en la variable `v:lnum`. En la línea 5, `v:lnum` tiene el valor 5. En la línea 10, `v:lnum` tiene el valor 10.
* `[0]` en el contexto de `getline(v:lnum)[0]` es el primer carácter de cada línea. Cuando Vim rastrea una línea, `getline(v:lnum)` devuelve el contenido de cada línea. `getline(v:lnum)[0]` devuelve el primer carácter de cada línea. En la primera línea de nuestra lista, "donut", `getline(v:lnum)[0]` devuelve "d". En la segunda línea de nuestra lista, "churros", `getline(v:lnum)[0]` devuelve "c".
* `==\\"c\\"` es la segunda mitad de la expresión de igualdad. Esta comprueba si la expresión que acaba de evaluar es igual a "c". Si esto es cierto, esto devuelve 1. Si esto es falso, esto devuelve 0. En Vim, 1 es verdadero y 0 es falso. Así que las líneas que comiencen con "c" devuelven un 1. Recuerda que si `'foldexpr'` tiene un valor de 1, entonces tiene un nivel de plegado de 1.

Después de ejecutar esta expresión, deberías ver:

```text
donut
+-- 3 lines: churros -----
salmon
huevos revueltos
```

## Plegado por sintaxis \(_Syntax Fold_\)

El plegado por sintaxis está determinado por el resaltado de lenguaje de sintaxis. Si utilizas un complemento de sintaxis de lenguaje como [vim-polyglot](https://github.com/sheerun/vim-polyglot), el plegado por sintaxis funcionará sin necesidad de configurar nada. Simplemente cambia el método de plegado a sintaxis:

```text
:set foldmethod=syntax
```

Vamos a asumir que estás editando un archivo en lenguaje JavaScript y tienes el complemento vim-polyglot instalado. Si tienes un array como el siguiente:

```text
const nums = [
  uno,
  dos,
  tres,
  cuatro
]
```

Esto será plegado con un plegado por sintaxis. Cuando defines un resaltado de sintaxis para un lenguaje en particular \(normalmente dentro del directorio `syntax/`\), puedes añadir un atributo `fold` para hacer que puede ser plegado. A continuación tienes una porción de código del archivo de sintaxis de vim-polyglot para JavaScript. Ten en cuenta la palabra clave `fold` al final de la línea.

```text
syntax region  jsBracket                      matchgroup=jsBrackets            start=/\[/ end=/\]/ contains=@jsExpression,jsSpreadExpression extend fold
```

Esta guía no tratará de manera detallada la funcionalidad de `syntax`. Si tienes más curiosidad por esto, echa un vistazo a la ayuda de Vim `:h syntax.txt`.

## Plegado por diferencia \(_Diff Fold_\)

Vim puede hacer un procedimiento de búsqueda de diferencias para comparar dos o más archivos.

Si tienes el archivo `archivo1.txt`:

```text
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
```

Y el archivo `archivo2.txt`:

```text
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
emacs está bien
```

Ejecuta `vimdiff archivo1.txt archivo2.txt`:

```text
+-- 3 lines: vim es genial -----
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
vim es genial
[vim es genial] / [emacs está bien]
```

Vim de manera automática pliega algunas líneas idénticas. Cuando ejecutas el comando `vimdiff`, Vim automáticamente utiliza `foldmethod=diff`. Si ejecutas ahora `:set foldmethod?`, verás que devuelve `diff` como método de plegado utilizado.

## Plegado por marcador \(_Marker Fold_\)

Para utilizar el plegado por marcador, ejecuta:

```text
:set foldmethod=marker
```

Supongamos que tenemos el siguiente texto:

```text
Hello

{{{
world
vim
}}}
```

Al ejecutar `zM`, verás:

```text
Hello

+-- 4 lines: -----
```

Vim ve `{{{` y `}}}` como indicadores de plegado y pliega el texto que hay entre ellos. Con el plegado por marcador, Vim busca los marcadores especiales, definidos por la opción `'foldmarker'`, para marcar las áreas de plegado. Para ver qué marcadores utiliza Vim, ejecuta:

```text
:set foldmarker?
```

De manera predeterminada, Vim utiliza `{{{` y `}}}` como indicadores. Si quieres cambiar el indicador a otros textos como "coffee1" y "coffee2":

```text
:set foldmarker=coffee1,coffee2
```

Si tienes el texto:

```text
hello

coffee1
world
vim
coffee2
```

Ahora Vim utiliza `coffee1` y `coffee2` como los nuevos marcadores de plegado de texto. Como nota complementaria, un indicador debe ser una cadena literal y no puede ser una expresión regular.

## Plegado persistente \(*Persisting Fold*\)

Al cerrar Vim se pierde toda la información de plegado. Si tienes este archivo, `cuenta.txt`:

```text
uno
dos
tres
cuatro
cinco
```

Ahora vamos a hacer un plegado de texto desde la línea "tres" hacia abajo \(`:3,$fold`\):

```text
uno
dos
+-- 3 lines: tres ---
```

Cuando sales de Vim y vuelves a abrir el archivo `cuenta.txt`, el plegado de texto ¡ha desaparecido!

Para preservar los plegados te texto que hagamos en un archivo, después de ejecutar el plegado, ejecuta:

```text
:mkview
```

Después cuando vuelvas a abrir el archivo `cuenta.txt`, ejecuta:

```text
:loadview
```

Los plegados de texto que haya quedarán restaurados. Sin embargo, tienes que ejecutar `mkview` y `loadview` de manera manual. Estoy seguro que un día u otro olvidarás ejecutar `mkview` antes de cerrar el archivo y perderás todos esos plegados de texto. ¿Cómo podemos automatizar este proceso?

Para ejecutar automáticamente `mkview` cuando cierras un archivo de tipo `.txt` y ejecutar `loadview` cuando abras un archivo `.txt`, añade esto en tu archivo de configuración vimrc:

```text
autocmd BufWinLeave *.txt mkview
autocmd BufWinEnter *.txt silent loadview
```

Recuerda que `autocmd` es utilizado para ejecutar un comando ante la aparición de un evento. Los dos eventos aquí son:

* `BufWinLeave` para cuando eliminas un _buffer_ de una ventana.
* `BufWinEnter` para cuando cargas un _buffer_ en una ventana.

Ahora cuando hayas creado un plegado de texto dentro de un archivo `.txt` y salgas de Vim, la próxima vez que abras un archivo, la próxima vez que abras ese archivo la información de los plegados que hayas creado se restaurará automáticamente.

De manera predeterminada, Vim guarda la información del plegado cuando ejecutas `mkview` dentro de `~/.vim/view` en sistemas operativos basados en Unix. Para más información, echa un vistazo a `:h 'viewdir'`.

## Aprende el plegado de texto de la manera más inteligente

Cuando comencé a utilizar Vim, me negué a aprender sobre el plegado o _fold_ porque no creí que fuera útil. Sin embargo, cuanto más programaba, más encontraba lo útil que era. Ubicar los plegados de texto de manera estratégica te puede dar una mejor visión de la estructura del texto, como el índice de contenidos de un libro.

Cuando comiences con el plegado de texto o _fold_, comienza con el plegado manual, porque eso puede utilizarse sobre la marcha. Después de manera gradual aprende diferentes trucos para plegar texto con otros métodos como el de sangría y marcadores. Finalmente, aprende cómo hacer plegados en base a la sintaxis y expresiones. Puedes utilizar estos dos métodos para escribir tus propios complementos de Vim.

