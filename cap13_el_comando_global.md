# Capítulo 13: El comando global

Hasta ahora has aprendido cómo repetir el último cambio con el comando del punto \(`.`\), repetir acciones con macros \(`q`\) y almacenar textos en los registros \(`"`\).

En este capítulo, aprenderás cómo repetir un comando ejecutado en la línea de comandos de Vim con el comando global. Ejecútalo una vez, aplícalo en todas partes \(dentro de un _buffer_\).

## Un vistazo al comando global

El comando global de Vim es utilizado para ejecutar un comando de la línea de comandos en múltiples líneas de manera simultánea.

Por cierto, puede que hayas leído anteriormente el término "comandos Ex". En este libro, yo me refiero a ellos como comandos de la línea de comandos, pero ambos tanto los comandos Ex como los comandos de la línea de comandos son lo mismo. Hay comandos que comienzan con el símbolo de dos puntos \(`:`\). En el capítulo anterior aprendiste sobre los comandos de sustitución. Fue un ejemplo de los comandos Ex. Son llamados Ex porque originalmente provenían del editor de texto Ex. Me seguiré refiriendo a ellos en este libro como comandos de la línea de comandos. Para obtener una lista completa de los comandos Ex, echa un vistazo en Vim a `:h ex-cmd-index`.

El comando global tiene la siguiente sintaxis:

```text
:g/patrón/comando
```

El `patrón` busca todas las líneas que contengan ese patrón, de manera similar al patrón en el comando de sustitución. El `comando` puede ser cualquier comando de la línea de comandos. El comando global funciona al ejecutar `comando` en cada línea que coincida con el `patrón`.

Si tienes las siguiente expresiones:

```text
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Para eliminar todas las líneas que contengan la palabra "console", puedes ejecutar:

```text
:g/console/d
```

Este sería el resultado:

```text
const one = 1;

const two = 2;

const three = 3;
```

El comando global ejecuta el comando eliminar \(`d`\) en todas las líneas que coincidan con el patrón "console".

Al ejecutar el comando `g`, Vim realiza dos escaneos del archivo. En el primero, escanea cada línea y marca las líneas que coinciden con el patrón `/console/`. Una vez que todas las líneas que coinciden están marcadas, vuelve a hacer otra pasada por todo el archivo, donde ejecuta el comando `d` en las líneas marcadas.

Si en vez de eso, quieres eliminar todas las líneas que contienen "const", ejecuta:

```text
:g/const/d
```

Este sería el resultado:

```text
console.log("one: ", one);

console.log("two: ", two);

console.log("three: ", three);
```

## Invertir las coincidencias

Para ejecutar el comando global en las líneas que no cumplan la coincidencia con el patrón, puedes ejecutar:

```text
:g!/patrón/comando
```

o

```text
:v/patrón/comando
```

Si ejecutas `:v/console/d`, esto eliminará todas las líneas que _no_ contengan la palabra "console".

## Patrón

El comando global utiliza el mismo sistema de patrones que el comando de sustitución, así que esta sección servirá como repaso. Si lo deseas, siéntete libre de saltar a la siguiente sección o sigue leyendo.

Si tienes las siguientes expresiones:

```text
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Para eliminar las líneas que contienen tanto "one" o "two", ejecuta:

```text
:g/one\|two/d
```

Para eliminar las líneas que contienen algún número de una cifra, ejecuta una de estas dos opciones:

```text
:g/[0-9]/d
```

o

```text
:g/\d/d
```

Si tienes las siguientes expresiones:

```text
const oneMillion = 1000000;
const oneThousand = 1000;
const one = 1;
```

Para encontrar las líneas que contienen entre tres a seis ceros, ejecuta:

```text
:g/0\{3,6\}/d
```

## Pasando un rango

Puedes pasar un rango antes del comando `g`. Aquí tienes algunas maneras de cómo lo puedes hacer:

* `:1,5g/console/d`  encuentra la cadena "console" entre las líneas 1 y 5 y las elimina.
* `:,5g/console/d` si no se especifica un número delante de la coma, entonces esto indica que comience el comando desde la línea actual. Busca la cadena "console" entre la línea actual y la línea 5 y las elimina.
* `:3,g/console/d` si no se especifica un número después de la coma, entonces indica que el comando finaliza en la línea actual. Busca la cadena "console" entre la línea 3 y la línea actual y las elimina.
* `:3g/console/d` si solo se le pasa un número sin el símbolo de la coma, esto ejecuta el comando solo en la línea 3. Busca en la línea 3 y la elimina si existe la cadena "console".

Además de los números, también se pueden utilizar esto símbolos como rango:

* `.` significa la línea actual. Un rango de `.,3` significa entre la línea actual y la línea 3.
* `$` significa la última línea del archivo. `3,$` es un rango entre la línea 3 y la última línea del archivo.
* `+n` significa _n_ líneas después de la línea actual. Lo puedes utilizar con `.` o sin ese símbolo. `3,+1` o `3,.+1` ambas significa, entre la línea 3 y la línea después de la línea actual.

Si no pasas ningún rango, de manera predeterminada se entiende que el comando se aplica al archivo completo. Esto realmente no es la norma general. La mayoría de los comandos para la línea de comandos de Vim se ejecutan solo en la línea actual si no se le pasa ningún rango. Las dos excepciones más notables son los comandos globales \(`:g`\) y el comando de guardar \(`:w`\).

## El comando normal

Puedes ejecutar un comando normal dentro del comando global con el comando `:normal` de la línea de comandos.

Si tienes el siguiente texto:

```text
const one = 1
console.log("one: ", one)

const two = 2
console.log("two: ", two)

const three = 3
console.log("three: ", three)
```

Para añadir un símbolo ";" al final de cada línea, ejecuta:

```text
:g/./normal A;
```

Diseccionemos el comando:

* `:g` es el comando global.
* `/./` es el patrón para "todas las líneas que no estén vacías". Recuerda que el punto \(`.`\) es un registro que representa _cualquier carácter_. Lo que hace es buscar las líneas con al menos un carácter, así que en este caso marcará las líneas con las palabras "const" y "console". Ignorando las líneas vacías.
* `normal A;` ejecuta el comando de la línea de comando `:normal`. `A;` es el comando normal para insertar un ";" al final de la línea.

## Ejecutar una macro

También puedes ejecutar una macro con el comando global. Una macro es simplemente un modo normal de operación, así que es posible ejecutarlo con `:normal`. Si tienes las siguientes expresiones:

```text
const one = 1
console.log("one: ", one);

const two = 2
console.log("two: ", two);

const three = 3
console.log("three: ", three);
```

Ten en cuenta que las líneas con "const" no tienen un símbolo de punto y coma al final de la línea. Vamos a crear una macro, que almacenaremos en el registro a, que añada un símbolo de punto y coma al final de esas líneas:

```text
qaA;<Esc>q
```

Si necesitas refrescar conocimientos, echa un vistazo al capítulo que trata sobre las macros. Ahora ejecuta:

```text
:g/const/normal @a
```

Ahora todas las líneas con "const" tendrán un ";" al final de la línea.

```text
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

## Comando recursivo global

El comando global por sí mismo es un tipo de comando de la línea de comandos, así que técnicamente puedes ejecutar el comando global dentro de un comando global.

Dadas las siguientes expresiones, si quieres eliminar la segunda declaración de `console.log`:

```text
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Si ejecutas:

```text
:g/console/g/two/d
```

Primero, `g` buscará las líneas que contengan el patrón "console" y encontrará 3 coincidencias. El segundo `g` buscará la línea que contenga el patrón "two" para esas tres coincidencias encontradas anteriormente. Finalmente, eliminará esas coincidencias.

Puedes combinar `g` con `v` para encontrar patrones positivos o negativos. Por ejemplo:

```text
:g/console/v/two/d
```

En vez de buscar las líneas que contienen "two", buscará las líneas que _no_ contienen el patrón "two".

## Cambiando el símbolo del delimitador del comando global

Puedes cambiar el delimitador del comando global de igual manera que se podía hacer en el comando de sustitución. Las reglas son las mismas: puedes utilizar cualquier carácter de un único byte excepto las letras del alfabeto, números, `"`, `|`, y `\`.

Para eliminar las líneas que contienen la palabra "console":

```text
:g@console@d
```

Si estás utilizando el comando de sustitución con el comando global, puedes utilizar dos delimitadores diferentes:

```text
g@one@s+const+let+g
```

Aquí el comando global buscará todas las líneas que contienen "one". El comando de sustitución, sustituirá de esas líneas encontradas, la cadena "const" con "let".

## El comando predeterminado

¿Qué pasa si no especificas ningún comando de la línea de comandos en el comando global?

El comando global utilizará el comando _print_ o mostrar por pantalla \(`:p`\) para mostrar la línea actual. Si ejecutas:

```text
:g/console
```

Esto mostrará en la parte inferior de la pantalla todas las líneas que contengan "console".

Por cierto, aquí hay un hecho curioso. Debido a que el comando predeterminado utilizado por el comando global es `p`, esto hace que la sintaxis de `g` sea:

```text
:g/re/p
```

* `g` = el comando global
* `re` = la expresión regular del patrón de búsqueda
* `p` = el comando _imprimir_ por pantalla

Esto forma _"grep"_, el mismo `grep` que se utiliza en la línea de comandos. Esto **no** es una coincidencia. El comando `g/re/p` procede originalmente del editor Ed, uno de los primeros editores de texto. El comando `grep` toma su nombre de Ed.

Tu equipo probablemente todavía tenga un editor Ed instalado. Ejecuta `ed` en una terminal \(pista: para salir, escribe `q`\).

## Más ejemplos

### Invertir el _buffer_ completo

Para invertir un archivo por completo, ejecuta:

```text
:g/^/m 0
```

`^` es un patrón que significa "el comienzo de una línea". Utiliza `^` para seleccionar todas las líneas, incluyendo las líneas vacías.

Si solo necesitas invertir unas cuantas líneas y no el archivo por completo, puedes añadir un rango. Para invertir las líneas de la cinco a la diez, ejecuta:

```text
:5,10g/^/m 0
```

Para aprender más sobre el comando de mover, echa un vistazo a `:h :move`.

### Agregar todas las tareas pendientes o _TODO_

Mientras estás escribiendo código, a veces te vienen a la cabeza diferentes ideas aleatoria brillantes. Si no quieres perder la concentración de lo que estás haciendo, puedes anotarlas en el propio archivo que estás editando, y etiquetarlas como _TODO_ o PENDIENTE, por ejemplo:

```text
const one = 1;
console.log("one: ", one);
// TODO: dar de comer a las gatas

const two = 2;
// TODO: dar de comer a las gatas automáticamente
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
// TODO: crear un invento que sirva para dar de comer a las gatas automáticamente
```

Puede ser complicado llevar la cuenta de todas las tareas _TODO_ que hemos ido creando. Vim tiene un método para copiar todas las coincidencias encontradas a una dirección con el comando `:t`. Para aprender más sobre este método de copiado, echa un vistazo a `:h :copy`.

Para copiar todas las tareas _TODOs_ al final del archivo para darles un repaso, ejecuta:

```text
:g/TODO/t $
```

Este sería el resultado:

```text
const one = 1;
console.log("one: ", one);
// TODO: dar de comer a las gatas

const two = 2;
// TODO: dar de comer a las gatas automáticamente
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
// TODO: crear un invento que sirva para dar de comer a las gatas automáticamente

// TODO: dar de comer a las gatas
// TODO: dar de comer a las gatas automáticamente
// TODO: crear un invento que sirva para dar de comer a las gatas automáticamente
```

Ahora puedes revisar fácilmente todas las tareas _TODO_ que has ido creando, y encontrar el tiempo para realizarlas o delegarlas a otra persona y continuar en mi trabajo con mis siguientes tareas.

Otra alternativa es utilizar `m`:

```text
:g/TODO/m $
```

Y este sería el resultado:

```text
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);

// TODO: dar de comer a las gatas
// TODO: dar de comer a las gatas automáticamente
// TODO: crear un invento que sirva para dar de comer a las gatas automáticamente
```

Puedo simplemente eliminar la lista una vez que decida qué hacer con ella.

### Mandar el contenido borrado a un agujero negro

Recuerda del capítulo de los registros que los textos eliminados son almacenados dentro de los registros numerados \(aunque son suficientemente grandes\). Cada vez que ejecutas `:g/console/d`, Vim almacena las líneas eliminadas en los registros numerados. Si eliminas muchas líneas, puedes rápidamente llenar el contenido de esos registros numerados. Para evitar esto, siempre puedes utilizar el registro del agujero negro \(`"_`\) para _no_ almacenar las líneas eliminadas en los registros. Ejecuta:

```text
:g/console/d_
```

Al pasar al comando el símbolo `_` después del comando de borrar `d`, Vim no almacenará las líneas eliminadas en ningún registro.

### Reducir múltiples líneas vacías en una única línea vacía

Si tienes un archivo con múltiples líneas vacías como este:

```text
const one = 1;
console.log("one: ", one);


const two = 2;
console.log("two: ", two);





const three = 3;
console.log("three: ", three);
```

Puedes reducir rápidamente cada conjunto de líneas vacías consecutivas a una única línea vacía. Ejecuta:

```text
:g/^$/,/./-1j
```

Este es el resultado:

```text
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Normalmente el comando global acepta el siguiente formato: `:g/patrón/comando`. Sin embargo puedes ejecutar también el comando global con el siguiente formato: `:g/patrón1/,/patrón2/comando`. De esta manera, Vim aplicará el comando `comando` dentro del `patrón1` y `patrón2`.

Con esto en mente, vamos a ver en detalle el anterior comando `:g/^$/,/./-1j` que sigue el formato de: `:g/patrón1/,/patrón2/comando`:

* `/patrón1/` es `/^$/`. Esto representa una línea vacía \(una línea sin ningún carácter\).
* `/patrón2/` es `/./` con el modificador de línea `-1`. `/./` representa una línea no vacía \(una línea con al menos un carácter\). El `-1` significa la línea superior a esa línea no vacía.
* `comando` es en este caso `j`, el comando de unión de líneas \(`:j`\). En este contexto, este comando global unirá todas las líneas especificadas en una.

Por cierto, si quieres reducir múltiples líneas vacías y eliminarlas por completo, en vez de utilizar `,/./-1` como rango para el comando `j`, simplemente utiliza `,/./` como rango.

```text
:g/^$/,/./j
```

O simplificado:

```text
:g/^$/-j
```

Tu texto ahora es reducido a:

```text
const one = 1;
console.log("one: ", one);
const two = 2;
console.log("two: ", two);
const three = 3;
console.log("three: ", three);
```

### Ordenado avanzado

Vim dispone del comando `:sort` para ordenar líneas de un rango. Por ejemplo:

```text
d
b
a
e
c
```

Puedes ordenarlas ejecutando `:sort`. Si le añades un rango, solo ordenará las líneas dentro de ese rango. Por ejemplo `:3,5sort` ordenará solo las líneas entre las líneas tres y cinco.

Si tienes las siguiente expresiones:

```text
const arrayB = [
  "i",
  "g",
  "h",
  "b",
  "f",
  "d",
  "e",
  "c",
  "a",
]

const arrayA = [
  "h",
  "b",
  "f",
  "d",
  "e",
  "a",
  "c",
]
```

Si necesitar ordenar los elementos dentro de los arrays, pero no los propios arrays, puedes ejecutar lo siguiente:

```text
:g/\[/+1,/\]/-1sort
```

Este es el resultado:

```text
const arrayB = [
  "a",
  "b",
  "c",
  "d",
  "e",
  "f",
  "g",
  "h",
  "i",
]

const arrayA = [
  "a"
  "b",
  "c",
  "d",
  "e",
  "f",
  "h",
]
```

¡Esto es genial! Pero el comando parece complicado. Vamos a verlo en detalle. Este comando también sigue el formato `:g/patrón1/,/patrón2/comando`.

* `:g` es el patrón del comando global.
* `/\[/+1` es el primer patrón. Busca literalmente un símbolo de apertura de corchete "[". El `+1` se refiere a la línea debajo de este.
* `/\]/-1` es el segundo patrón. Busca literalmente un símbolo de cierre de corchete "]". El `-1` se refiere a la línea encima de este.
* `/\[/+1./\]/-1` entonces se refiere a cualquier línea entre "[" y "]".
- `sort` es el comando de la línea de comandos para ordenar objetos.

## Aprendiendo el comando global de la manera más inteligente

El comando global ejecuta comandos de la línea de comandos en todas las líneas especificadas. Con este comando, solo necesitarás ejecutar el comando una vez y Vim hará el resto por ti. Para convertirte en un usuario eficiente del comando global, se necesitan dos cosas: un buen vocabulario de los comandos de la línea de comandos y un conocimiento de las expresiones regulares. Cuanto más tiempo pases utilizando Vim, más fácil será aprender más comandos de la línea de comandos de manera natural. Un conocimiento de las expresiones regulares requerirá un acercamiento más activo. Pero una vez que llegues a sentirte cómodo y cómoda con las expresiones regulares, llegarás a dominar muchas de estas.

Algunos de los ejemplos que has podido leer aquí son complicados. Pero no te sientas intimidado ni intimidada. Realmente lleva tiempo el entenderlos. Aprende a leer patrones. Asegúrate que comprendes qué significa cada letra y qué representa en cada comando. No te rindas.

Allí donde necesites aplicar un comando en diferentes sitios, detente y reflexiona si puedes utilizar el comando `g`. Busca el mejor comando para el trabajo y escribe un patrón que pueda hacer muchas cosas de una vez. Después repítelo hasta que puedas hacerlo sin necesidad de pensarlo. La siguiente vez, comprueba si existe una manera de hacerlo de una manera incluso más rápida y más eficiente.

Ahora que conoces lo potente que es el comando global, vamos a aprender a utilizar los comandos externos para incrementar tu arsenal de herramientas.

