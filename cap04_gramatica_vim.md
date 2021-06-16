# Capítulo 4: La gramática de Vim

Es sencillo sentirse intimidado por la complejidad de muchos de los comandos de Vim. Si ves a una persona que usa Vim habitualmente ejecutando `gUfV` o `1GdG`, puede que no sepas inmediatamente qué es lo que realizan esos comandos. En este capítulo, voy a romper la estructura general de los comandos de Vim en reglas simples de gramática.

Este es el capítulo más importante de todo el libro. Una vez que entiendas la estructura similar a la gramática de los comandos de Vim, serás capaz de "hablarle" a Vim. Por cierto, cuando me refiero a _lenguaje de Vim_ en este capítulo, no estoy refiriéndome a Vimscript \(el lenguaje de programación propio para adaptar y crear complementos de Vim\). Aquí se refiere al patrón general de los comandos del modo normal de Vim.

## Cómo aprender un lenguaje

El inglés no es mi lengua nativa. Aprendí inglés cuando tenía 13 años. Tuve que hacer tres cosas para aumentar mis habilidades linguísticas:

1. Aprender las reglas gramáticas
2. Incrementar mi vocabulario
3. Practicar, practicar y practicar

De igual manera, para hablar el lenguaje de Vim, necesitas aprender las reglas gramáticas, incrementar tu vocabulario y practicar hasta que puedas ejecutar los comandos sin necesidad de pensarlos.

## Reglas gramáticas

Solo necesitas saber una regla gramática para hablar el lenguaje de Vim:

```text
verbo + sustantivo
```

¡Eso es todo!

Esto es equivalente a decir en inglés estas frases: \(en español aunque se entienden y se construyan de manera similar, quedan un poco forzadas y poco coloquiales, pero son aplicables las mismas reglas\):

* _"Comer \(verbo\) un donut \(sustantivo\)"_
* _"Patear \(verbo\) un balón \(sustantivo\)"_
* _"Aprender \(verbo\) el editor Vim \(sustantivo\)"_

Ahora necesitas construir tu vocabulario con verbos de Vim básicos y sustantivos.

## Vocabulario

### Sustantivos \(Movimientos\)

Hablemos de los movimientos como sustantivos. Los movimientos son utilizados para movernos por el texto en Vim. También hay sustantivos de Vim. En la lista a continuación veremos algunos ejemplos de movimientos:

```text
h    Izquierda
j    Abajo
k    Arriba
l    Derecha
w    Mover el cursor hacia adelante al principio de la palabra siguiente
}    Saltar al siguiente párrafo
$    Ir al final de la línea
```

Aprenderás más sobre los movimientos en el capítulo siguiente, así que no te preocupes demasiado si todavía no entiendes algunos de los mencionados.

### Verbos \(Operadores\)

De acuerdo a lo que podemos leer en la ayuda de Vim mediante el comando `:h operator`, Vim tiene 16 operadores. Sin embargo, en mi experiencia, con aprender estos 3 es suficiente para el 80% de las necesidades a la hora de editar mis textos:

```text
y    Copiar un texto (*yank* en Vim sería la acción de copiar, de ahí la letra `y`)
d    Eliminar un texto y guardarlo en el registro (*delete* en Vim sería la acción de eliminar, de ahí la letra `d`)
c    Eliminar un texto, guardarlo en el registro y comenzar en el modo de insertar
```

Por cierto, después de que copies un texto, lo puedes pegar con `p` (después de la posición del cursor) o con `P` (antes de la posición del cursor).

### Verbo y sustantivo

Ahora que ya conoces los sustantivos y verbos básicos, ¡Vamos a aplicar la regla gramática de verbo + sustantivo! Supongamos que tenemos la siguiente expresión en un texto:

```javascript
const learn = "vim";
```

* Para copiar \(_yank_\) todo desde la ubicación actual del cursor hasta el final de la línea: `y$`.
* Para eliminar desde la ubicación actual del cursor hasta el principio de la siguiente palabra: `dw`.
* Para cambiar el texto desde la posición actual del cursor hasta el final de párrafo actual, di: `c}`.

Los movimientos también aceptan números de conteo como argumentos _\(Veremos esto más en profundidad en el siguiente capítulo\)_. Si necesitas subir tres líneas, en vez de presionar la letra `k` 3 veces, simplemente puedes ejecutar `3k`. Esas opciones de pasar un valor también funcionan a la hora de conjugar la gramática de Vim.

* Para copiar dos caracteres a la izquierda: `y2h`.
* Para eliminar las siguientes dos palabras: `d2w`.
* Para cambiar las siguientes dos líneas: `c2j`.

Ahora mismo, quizás tengas que pensar bastante para construir comandos simples. Pero no creas que solo te pasa a ti. No estás solo. Cuando comencé, tuve dificultades similares pero progresé rápidamente con el tiempo. Y también te pasará a ti.

Como nota complementaria, las operaciones que afectan a una línea completa son las tareas más comunes a la hora de editar texto, y por eso Vim te permite realizar operaciones que implican a la línea completa tecleando los operadores que ya conocemos dos veces seguidas. Por ejemplo, `dd`, `yy`, y `cc` lo que hace es **eliminar**, **copiar** y **cambiar** el contenido de la línea completa. ¡Intenta esto con otros operadores!

Espero que todo comience a tener sentido poco a poco. Pero todavía no he terminado. Vim tiene otro tipo más de sustantivos: objetos de texto.

### Más sustantivos \(Objetos de texto\)

Imagina que estás en algún lugar dentro de un paréntesis, como por ejemplo `(hola Vim)` y necesitas eliminar la frase entera dentro del paréntesis. ¿Cómo puedes hacer esto rápidamente? ¿Hay una manera de borrar el "grupo" dentro del que estás?

La respuesta es sí. Los textos a menudo vienen estructurados. A menudo están puestos dentro de paréntesis, comillas, corchetes, llaves y cosas similares. Vim tiene una manera de capturar esta estructura con los objetos de texto.

Los objetos de texto son usados con operadores. Hay dos tipos de objetos de texto:

```text
i + objeto    Dentro del objeto de texto
a + objeto    Fuera del objeto de texto
```

Dentro del objeto del texto selecciona el objeto del interior _sin_ los espacios en blanco o los objetos que lo rodean.

Fuera del objeto de texto selecciona el objeto interior _incluyendo_ los espacios en blanco y los objetos que lo rodean.

La selección de fuera del objeto de texto siempre selecciona más texto que la selección de dentro del objeto de texto. Así que si tu cursor está en algún lugar dentro del paréntesis dentro de la expresión `(hola Vim)`:

* Para eliminar el texto dentro del paréntesis sin borrar el propio paréntesis, ejecuta: `di(`.
* Para eliminar el paréntesis y el texto interior, ejecuta: `da(`.

Echemos un vistazo a un ejemplo diferente. Supongamos que tenemos esta función de Javascript y tu cursor está en la letra "H" de la palabra "Hello":

```javascript
const hello = function() {
  console.log("Hello Vim"); 
  return true;
}
```

* Para eliminar por completo el texto "Hello Vim": `di(`.
* Para eliminar el contenido de la función \(rodeado por `{}`\): `di{`.
* Para eliminar la palabra "Hello": `diw`. 

Los objetos de texto son muy potentes porque puedes seleccionar diferentes objetos desde una misma ubicación, como hemos visto en el ejemplo anterior. Puedes borrar objetos dentro de un par de paréntesis, el bloque de la función entera o una palabra. Además, cuando ves `di(`, `di{`, o `diw`, te haces una buena idea de qué objetos están representando \(un par de paréntesis, un par de llaves y una palabra\).

Echemos un vistazo a un último ejemplo. Supongamos que tenemos estas etiquetas \(_tags_ en inglés\) de código HTML:

```markup
<div>
  <h1>Cabecera1</h1>
  <p>Párrafo1</p>
  <p>Párrafo2</p>
</div>
```

Si tu cursor está sobre el texto "Cabecera1":

* Para eliminar "Cabecera1": `dit`.
* Para eliminar `<h1>Cabecera1</h1>`: `dat`.

Si tu cursor está sobre la palabra "div":

* Para eliminar `h1` y ambas líneas con la etiqueta `p`: `dit`.
* Para eliminar todo: `dat`.
* Para eliminar "div": `di<`.

A continuación tienes una lista de los objetos de texto más comunes:

```text
w         Una palabra
p         Un párrafo
s         Una frase (*sentence* en inglés)
( o )     Un par de ( )
{ o }     Un par de { }
[ o ]     Un par de [ ]
< o >     Un par de < >
t         Etiquetas XML (*tags* en inglés)
"         Un par de " "
'         Un par de ' '
`         Un par de ` `
```

Para aprender más, echa un vistazo a la ayuda de Vim con este comando `:h text-objects`.

## Cómo componer frases y gramática

Después de aprender la gramática de Vim, vamos a hablar sobre la capacidad de componer frases \(N.d.T: En el texto original utiliza una palabra que se podría traducir como "componibilidad" que traduciré como la capacidad o habilidad de componer frases\) en Vim y de porqué esta es una gran funcionalidad para tener en un editor de texto.

La capacidad o habilidad de construir frases significa disponer de un conjunto de comandos generales que pueden ser combinados para desarrollar comandos más complicados. De manera similar a como se hace en programación, donde puedes crear abstracciones más complejas de abstracciones simples, en Vim puedes ejecutar comandos complejos utilizando comandos más simples. La gramática de Vim es la manifestación de la naturaleza componible de Vim.

El verdadero poder de la capacidad de componer frases en Vim se manifiesta cuando se integra con programas externos. Vim tiene un operador de filtro \(`!`\) para utilizar programas externos como filtros para nuestros textos. Supongamos que tenemos este texto un poco desordenado que aparece a continuación y quieres ordenarlo con tabuladores.

```text
Id|Name|Cuteness
01|Puppy|Very
02|Kitten|Ok
03|Bunny|Ok
```

Esto no puede realizarse de manera sencilla utilizando simplemente comandos de Vim, pero puedes realizarlo rápidamente utilizando un comando de terminal como `column`. Con el cursor situado en "Id", ejecuta `!}column -t -s "|"`. ¡Voila! Con esto obtendrás el texto tabulado de la siguiente manera:

```text
Id  Name    Cuteness
01  Puppy   Very
02  Kitten  Ok
03  Bunny   Ok
```

Diseccionemos el comando que hemos ejecutado. El verbo era `!` \(operador de filtro\) y el sustantivo era `}` \(para ir al siguiente párrafo\). El operador de filtro `!` aceptó otro argumento, un comando de terminal, así que le añadí `column -t -s "|"`. No detallaré cómo funciona `column`, pero resumiendo mucho se puede decir que ordena el texto mediante tabulaciones.

Supongamos que quieres no solo ordenar mediante tabuladores el texto, también mostrar solo las filas que tienen el texto "Ok". Quizás ya sabes que `awk` puede hacer ese trabajo fácilmente. En ese caso ejecutarías:

```text
!}column -t -s "|" | awk 'NR > 1 && /Ok/ {print $0}'
```

Este sería el resultado:

```text
02  Kitten  Ok
03  Bunny   Ok
```

¡Genial! Incluso en Vim funciona el encadenar comandos mediante "tuberías" con `|`.

Este es el poder de la "componibilidad" de Vim. Cuantos más operadores, movimientos y comandos de terminales conoces tu habilidad para componer acciones más complejas se ve _multiplicada_.

Permíteme desarrollar esa idea. Supongamos que solo conocemos cuatro movimientos: `w, $, }, G` y el operador de eliminar \(`d`\). Puedes realizar 8 cosas: moverte de 4 maneras diferentes \(`w, $, }, G`\) y eliminar contenido de 4 objetivos diferentes \(`dw, d$, d}, dG`\). Entonces un día aprendes el operador para convertir en mayúsculas un texto \(`gU`\). Con esto no solo has añadido una nueva habilidad al cinturón de herramientas que utilizas en Vim, si no _cuatro_: `gUw, gU$, gU}, gUG`. Ahora tienes 12 herramientas nuevas en tu cinto de herramientas de Vim. Cada nuevo aprendizaje es un multiplicador de tus habilidades actuales con Vim. Si conoces 10 movimientos y 5 operadores, ahora tienes 60 herramientas en tu arsenal \(50 operaciones + 10 movimientos\). Además el movimiento a un número de línea \(`nG`\) te da `n` movimientos, donde `n` indica cuantas líneas tienes en tu archivo \(ejemplo: para ir a la línea 5, `5G`\). El movimiento de búsqueda \(`/`\) prácticamente te da un número ilimitado de movimientos debido a que puedes buscar cualquier cosa. El operador de comandos externos \(`!`\) te ofrece un número de herramientas de filtrado igual al número de comandos de terminal que conozcas. Utilizando la capacidad para componer de Vim, todo lo que conoces puede estar conectado para realizar operaciones más complejas. Cuanto más conoces, más poderoso puedes llegar a ser.

Este comportamiento componible proviene de la filosofía Unix: _hacer una cosa, y hacerlo bien_. Un movimiento tiene una tarea: Ir al lugar _X_. Un operador tiene una tarea: hacer _Y_. Combinando un operador con un movimiento, obtienes _XY_: Ve a _X_ y haz _Y_.

Esto es incluso mejor, los movimientos y operadores son extensibles. Puedes crear tus propios movimiento y operadores personalizados para añadirlos a tu conjunto de herramientas de Vim. [`vim-textobj-user`](https://github.com/kana/vim-textobj-user) tiene una [lista](https://github.com/kana/vim-textobj-user/wiki) de objetos de texto personalizados.

Por cierto, no pasa nada si no conoces los comandos `column` o `awk`. El punto fundamental que debe quedar de esto es que Vim se integra perfectamente con los comandos de terminal que conozcas.

## Aprendiendo la gramática de Vim de la manera más inteligente

Simplemente has aprendido la única regla en la gramática de Vim:

```text
verbo + sustantivo
```

Uno de mis grandes momentos de Vim donde dije _eureka_ fue cuando aprendí el operador para convertir en mayúsculas y quise convertir a mayúsculas una palabra, de manera instintiva simplemente ejecuté `gUiw` ¡y funcionó! La palabra en la que estaba situado mi cursor se convirtió a mayúsculas. En ese momento empecé a comprender Vim. Espero que si aún no lo has tenido, tengas pronto tu momento _eureka_.

El objetivo en este capítulo es mostrarte el patrón de `verbo + sustantivo` en Vim, así irás aprendiendo Vim de la misma manera que se aprende un nuevo idioma en vez de memorizar cada combinación de comandos.

Aprende el patrón y comprende las implicaciones de este. Esa es la manera más inteligente de aprender.

