# Capítulo 13: El comando global

Hasta ahora has aprendido cómo repetir el último cambio con el comando del punto (`.`), repetir acciones con macros (`q`) y almacenar textos en los registros (`"`). 

En este capítulo, aprenderás cómo repetir un comando ejecutado en la línea de comandos de Vim con el comando global. Ejecútalo una vez, aplícalo en todas partes (dentro de un *buffer*).

## Un vistazo al comando global

El comando global de Vim es utilizado para ejecutar un comando de la línea de comandos en múltiples líneas de manera simultánea. 

Por cierto, puede que hayas leído anteriormente el témino "comandos Ex". En este libro, yo me refiero a ellos como comandos de la línea de comandos, pero ambos tanto los comandos Ex como los comandos de la línea de comandos son lo mismo. Hay comandos que comienzan con el símbolo de dos puntos (`:`). En el capítulo anterior aprendiste sobre los comandos de sustitución. Fue un ejemplo de los comandos Ex. Son llamados Ex porque originalmente provenían del editor de texto Ex. Me seguiré refiriendo a ellos en este libro como comandos de la línea de comandos. Para obtener una lista completa de los comandos Ex, echa un vistazo en Vim a `:h ex-cmd-index`.

El comando global tiene la siguiente sintaxis:

```
:g/patrón/comando
```

El `patrón` busca todas las líneas que contengan ese patrón, de manera similar al patrón en el comando de sustitución. El `comando` puede ser cualquier comando de la línea de comandos. El comando global funciona al ejecutar `comando` en cada línea que coincida con el `patrón`.

Si tienes las siguiente expresiones:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Para eliminar todas las líneas que contengan la palabra "console", puedes ejecutar:

```
:g/console/d
```

Este sería el resultado:

```
const one = 1;

const two = 2;

const three = 3;
```

El comando global ejecuta el comando eliminar (`d`) en todas las líneas que coincidan con el patrón "console".

Al ejecutar el comando `g`, Vim realiza dos escaneos del archivo. En el primero, escanea cada línea y marca las líneas que coinciden con el patrón `/console/`. Una vez que todas las líneas que coinciden están marcadas, vuelve a hacer otra pasada por todo el archivo, donde ejecuta el comando `d` en las líneas marcadas.

Si en vez de eso, quieres eliminar todas las líneas que contienen "const", ejecuta:

```
:g/const/d
```

Este sería el resultado:

```
console.log("one: ", one);

console.log("two: ", two);

console.log("three: ", three);
```

## Invertir las coincidencias

Para ejecutar el comando global en las líneas que no cumplan la coincidencia con el patrón, puedes ejecutar:

```
:g!/{patrón}/{comando}
```

o

```
:v/{patrón}/{comando}
```

Si ejecutas `:v/console/d`, esto eliminará todas las líneas que *no* contengan la palabra "console".

## Patrón

El comando global utiliza el mismo sistema de patrones que el comando de sustitución, así que esta sección servirá como repaso. Si lo deseas, sientete libre de saltar a la siguiente sección o sigue leyendo.

Si tienes las siguientes expresiones:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Para eliminar las líneas que contienen tanto "one" o "two", ejecuta:

```
:g/one\|two/d
```

Para eliminar las líneas que contienen algún número de una cifra, ejecuta una de estas dos opciones:

```
:g/[0-9]/d
```

o

```
:g/\d/d
```

Si tienes las siguientes expresiones:

```
const oneMillion = 1000000;
const oneThousand = 1000;
const one = 1;
```

Para encontrar las líneas que contienen entre tres a seis ceros, ejecuta:

```
:g/0\{3,6\}/d
```

## Pasando un rango

Puedes pasar un rango antes del comando `g`. Aquí tienes algunas maneras de cómo lo puedes hacer:

- `:1,5/g/console/d`  encuentra la cadena "console" entre las líneas 1 y 5 y las elimina.
- `:,5/g/console/d` si no se especifica un número delante de la coma, entonces esto indica que comience el comando desde la línea actual. Busca la cadena "console" entre la línea actual y la línea 5 y las elimina.
- `:3,/g/console/d` si no se especifica un número después de la coma, entonces indica que el comando finaliza en la línea actual. Busca la cadena "console" entre la línea 3 y la línea actual y las elimina.
- `:3g/console/d` si solo se le pasa un número sin el símbolo de la coma, esto ejecuta el comando solo en la línea 3. Busca en la línea 3 y la elimina si existe la cadena "console".

Además de los números, también se pueden utilizar esto símbolos como rango:
- `.` significa la línea actual. Un rango de `.,3` significa entre la línea actual y la línea 3.
- `$` significa la última línea del archivo. `3,$` es un rango entre la línea 3 y la última línea del archivo.
- `+n` significa *n* líneas después de la línea actual. Lo puedes utilizar con `.` o sin ese símbolo. `3,+1` o `3,.+1` ambas significa, entre la línea 3 y la línea después de la línea actual.

Si no pasas ningún rango, de manera predeterminada se entiende que el comando se aplica al archivo completo. Esto realmente no es la norma general. La mayoría de los comandos para la línea de comandos de Vim se ejecutan solo en la línea actual si no se le pasa ningún rango. Las dos excepciones más notables son los comandos globales (`:g`) y el comando de guardar (`:w`). 

## El comando normal

Puedes ejecutar un comando normal dentro del comando global con el comando `:normal` de la línea de comandos.

Si tienes el siguiente texto:
```
const one = 1
console.log("one: ", one)

const two = 2
console.log("two: ", two)

const three = 3
console.log("three: ", three)

```
Para añadir un símbolo ";" al final de cada línea, ejecuta:
```
:g/./normal A;
```

Diseccionesmos el comando:
- `:g` es el comando global.
- `/./` es el patrón para "todas las líneas que no estén vacías". Recuerda que el punto (`.`) es un registro que representa *cualquier caracter*. Lo que hace es buscar las líneas con al menos un caracter, así que en este caso marcará las líneas con las palabras "const" y "console". Ignorando las líneas vacías.
- `normal A;` ejecuta el comando de la línea de comando `:normal`. `A;` es el comando normal para insertar un ";" al final de la línea.

## Ejecutar una macro

También puedes ejecutar una macro con el comando global. Una macroes simplemente un modo normal de operación, así que es posible ejecutarlo con `:normal`. Si tienes las siguiente expresiones:

```
const one = 1
console.log("one: ", one);

const two = 2
console.log("two: ", two);

const three = 3
console.log("three: ", three);
```

Ten en cuenta que las líneas con "const" no tienen un símbolo de punto y coma al final de la línea. Vamos a crear una macro, que almacenaremos en el registro "a", que añada un símbolo de punto y coma al final de esas líneas:

```
qa0A;<esc>q
```

Si necesitas refrescar conocimientos, echa un vistazo al capítulo que trata sobre las macros. Ahora ejecuta:

```
:g/const/normal @a
```

Ahora todas las líneas con "const" tendrán un ";" al final de la línea.

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

## Comando recursivo global

El comando global pro sí mismo es un tipo de comando de la línea de comandos, así que técnicamente puedes ejecutar el comando global dentro de un comando global.

Dadas las siguientes expresiones:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Si ejecutas:

```
:g/console/g/two/d
```

Primero, `g` buscará las líneas que contengan el patrón "console" y encontrará 3 coincidencias. El segundo `g` buscará la línea que contenga el patrón "two" para esas tres coincidencias encontradas anteriormente. Finalmente, eliminará esas coincidencias.

Puedes combinar `g` con `v` para encontrar patrones positivos o negativos. Por ejemplo:

```
:g/console/v/two/d
```

En vez de buscar las líneas que contienen "two", buscará las líneas que *no* contienen el patrón "two".

## Cambiando el símbolo del delimitador del comando global

Puedes cambiar el delimitador del comando global de igual manera que se podía hacer en el comando de sustitución. Las reglas son las mismas: puedes utilizar cualquier caracter de un único byte excepto las letras del alfabeto, números, `"`, `|`, y `\`.

Para eliminar las líneas que contienen la palabra "console":

```
:g@console@d
```

Si estás utilizando el comando de sustitución con el comando global, puedes utilizar dos delimitadores diferentes:

```
g@one@s+const+let+g
```

Aquí el comando global buscará todas laslíneas que contienen "one". El comando de sustitución, sustituirá de esas líneas encontradas, la cadena "const" con "let".

## El comando predeterminado

¿Qué pasa si no especificas ningún comando de la línea de comandos en el comando global? 

El comando global utilizará el comando *print* o mostrar por pantalla (`:p`) para mostrar la línea actual. Si ejecutas:

```
:g/console
```

Esto mostrará en la parte inferior de la pantalla todas las líneas que contengan "console".

Por cierto, aquí hay un hecho curioso. Debido a que el comando predeterminado utilizado por el comando global es `p`, esto hace que la sintaxis de `g` sea:

```
:g/re/p
```

- `g` = el comando global
- `re` = la expresión regular del patrón de búsqueda
- `p` = el comando *imprimir* por pantalla

Esto forma "grep"*, el mismo `grep` que se utiliza en la línea de comandos. Esto **no** es una coincidencia. El comando `g/re/p` procede originalmente del editor Ed, uno de los primeros editores de texto. El comando `grep` toma su nombre de Ed. 

Tu equipo probablemente todavía tenga un editor Ed instalado. Ejecuta `ed` en una terminal (pista: para salir, escribe `q`).

## Más ejemplos

### Invertir el *buffer* completo

Para invertir un archivo por completo, ejecuta:

```
:g/^/m 0 
```

`^` es un patrón que significa "el comienzo de una línea". Utiliza `^` para seleccionar todas las líneas, incluyendo las líneas vacías.

Si solo necesitas invertir unas cuantas líneas y no el archivo por completo, puedes añadir un rango. Para invertir las líneas de la cinco a la diez, ejecuta:

```
:5,10g/^/m 0
```

Para aprender más sobre el comando de mover, echa un vistazo a `:h :move`.

### Agregar todas las tareas pendientes o *TODO*

Mientras estás escribiendo código, a veces te vienen a la cabeza diferentes ideas aleatoria brillantes. Si no quieres perder la concentración de lo que estás haciendo, puedes anotarlas en el propio archivo que estás editando, y etiquetarlas como *TODO* o PENDIENTE, por ejemplo:

```
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

Puede ser complicado llevar la cuenta de todas las tareas *TODO* que hemos ido creando. Vim tiene un método para copiar todas las coincidencias encontradas a una dirección con el comando `:t`. Para aprender más sobre este método de copiado, echa un vistazo a `:h :copy`.

Para copiar todas las tareas *TODOs* al final del archivo para darles un repaso, ejecuta:

```
:g/TODO/t $
```

Este sería el resultado:

```
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

Ahora puede revisar fácilmente todas las tareas *TODO* que he ido creando, y encontrar el tiempo para realizarlas o delegarlas a otra persona y continuar en mi trabajo con mis siguientes tareas.

Otra alternativa es utilizar `m`:

```
:g/TODO/m $
```

Y este sería el resultado:

```
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

Recuerda del capítulo de los registros que los textos eliminados son almacenados dentro de los registros numerados (aunque son suficientemente grandes). Cada vez que ejecutas `:g/console/d`, Vim almacena las líneas eliminadas en los registros numerados. Si eliminas muchas líneas, puedes rápidamente llenar el contenido de esos registros numerados. Para eliminar esto, siempre puedes utilizar el registro del agujero negro (`"_`) para *no* almacenar las líneas eliminadas en los registros. Ejecuta:

```
:g/console/d _
```

Al pasar al comando el símbolo `_` después del comando de borrar `d`, Vim no almacenará las líneas eliminadas en ningún registro.

### Reducir múltiples líneas vacías en una única línea vacía

Si tienes un archivo con múltiples líneas vacías como este:

```
const one = 1;
console.log("one: ", one);


const two = 2;
console.log("two: ", two);





const three = 3;
console.log("three: ", three);
```

Puedes reducir rápidamente cada conjunto de líneas vacías consecutivas a una única línea vacía. Ejecuta:

```
:g/^$/,/./-1j
```

Este es el resultado:

```
const one = 1;
console.log("one: ", one);

const two = 2;
console.log("two: ", two);

const three = 3;
console.log("three: ", three);
```

Normalmente el comando global acepta el siguiente formato: `:g/patrón/comando`. Sin embargo puedes ejecutar también el comando global con el siguiente formato: `:g/patrón1/,/patrón2/comando`. De esta manera, Vim aplicará el comando `comando` dentro del `patrón1` y `patrón2`.

Con esto en mente, vamos a ver en detalle el anterior comando `:g/^$/,/./-1j` que sigue el formato de: `:g/patrón1/,/patrón2/comando`:

- `/patrón1/` es `/^$/`. Esto representa una lína vacía (una línea sin ningún caracter).
- `/patrón2/` es `/./` con el modificador de línea `-1`. `/./` representa una línea no vacía (una línea con al menos un caracter). El `-1` significa la línea superior a esa línea no vacía.
- `comando` es en este caso `j`, el comando de unión de líneas (`:j`). En este contexto, este comando global unirá todas las líneas especificadas en una.

Por cierto, si quieres reducir múltiples líneas vacías y eliminarlas por completo, en vez de utilizar `,/./-1` como rango para el comando `j`, simplemente utiliza `,/./` como rango.

```
:g/^$/,/./j
```

O simplificado:

```
:g/^$/-j
```

Tu texto ahora es reducido a:

```
const one = 1;
console.log("one: ", one);
const two = 2;
console.log("two: ", two);
const three = 3;
console.log("three: ", three);
```

### Ordenado avanzado

Vim dispone del comando `:sort` para ordenar líneas de un rango. Por ejemplo:

```
d
b
a
e
c
```

Puedes ordenarlas ejecutando `:sort`. Si le añades un rango, solo ordenará las líneas dentro de ese rango. Por ejemplo `:3,5sort` ordenará solo las líneas entre las líneas 3 y 5. 

Si tienes las siguiente expresiones:

```
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

```
:g/\[/+1,/\]/-1sort
```

Este es el resultado:

```
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

¡Esto es genial! Pero el comando parece complicado. Vamos a verlo en detalle. El comando consiste en tres partes principales: el patrón del comando global, el rango del comando `sort` y el propio comando.

`:g/\[/` es el patrón del comando global. 
- `:g` es el comando global.
- `/\[/` es el patrón utilizado por el comando global. `\[` busca literalmente la cadena "[".

`+1,/\]/-1` es el rango del comando `sort`.
- Un rango puede tener una dirección de comienzo y otra de final. En este caso, `+1` es la dirección de comienzo y `/\]/-1` es la dirección final.
- `+1` representa la linea después de la línea actual, que es la línea que coincide con el patrón "[" del comando global. `+1` añade un offset de una línea a la línea actual. Así en la primera ocurrencia encontrada, el rango comenzará una línea *después* del texto `const arrayB = [`.
- `/\]/-1` es la dirección del final. `\]` representa de manera literal un corchete de cierre "]". `-1` añade un offset de una línea. La dirección final será la línea superior a "]".

`sort` es el comando para ordenar en la línea de comandos. Ordena todo con el rango dado. Todo lo que hay después de "[" hasta todo lo que hay encima de "]" será ordenado.

Si todavía estás confundido con el comando, no te preocupes. Me llevó mucho tiempo llegar a dominar el comando. Date un descanso, deja de mirar a la pantalla y regresa de nuevo con la mente despejada.

## Aprendiendo el comando global de la manera más inteligente

El comando global ejecuta comandos de la línea de comandos en todas las líneas especificadas. Con este comando, solo necesitarás ejecutar el comando una vez y Vim hará el resto por ti. Para convertirte en un usuario eficiente del comando global, se necesitan dos cosas: un buen vocabulario de los comandos de la línea de comandos y un conocimiento de las expresiones regulares. Cuanto más tiempo pases utilizando Vim, más fácil será aprender más comandos de la línea de comandos de manera natural. Un conocimiento de las expresiones regulares requerirá un acercamiento más activo. Pero una vez que llegues a sentirte cómodo y cómoda con las expresiones regulares, llegarás a dominar muchas de estas. 

Algunos de los ejemplos que has podido leer aquí son somplicados. Pero no te sientas intimidado ni intimidada. Realmente lleva tiempo el entenderlos. Aprende a leer patrones. Asegúrate que comprendes qué significa cada letra y qué representa en cada comando. No te rindas.

Allí donde necesites aplicar un comando en diferentes sitios, detente y reflexiona si puedes utilizar el comando `g`. Busca el mejor comando para el trabajo y escribe un patrón que pueda hacer muchas cosas de una vez. Después repítelo hasta que puedas hacerlo sin necesidad de pensarlo. La siguiente vez, comprueba si existe una manera de hacerlo de una manera incluso más rápida y más eficiente.

Ahora que conoces lo potente que es el comando global, vamos a aprender a utilizar los comandos externos para incrementar tu arsenal de herramientas.
