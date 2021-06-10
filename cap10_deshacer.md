# Capítulo 10: Deshacer

Todas las personas cometemos todo tipo de errores tipográficos. Es por eso que deshacer es una funcionalidad esencial en cualquier software moderno. El sistema de deshacer de Vim no solo es capaz de deshacer y rehacer errores, también te permite manipular y recuperar texto a través del tiempo. En este capítulo, aprenderás como deshacer y volver a hacer los cambios en tu texto, navegar por una rama de deshacer, deshacer de forma persistente y viajar a través del tiempo.

## Deshacer, rehacer y DESHACER

Para realizar una tarea básica de deshacer, en el modo normal puedes utilizar simplemente `u` o ejecutar `:undo`.

Si tienes este texto (ten en cuenta la línea vacía debajo del texto "uno"):

```text
uno

```

Añade otro texto:

```text
uno
dos
```

Si estando en el modo normal ejecutas `u`, Vim deshace la escritura del texto "dos".

¿Cómo sabe Vim cuanto tiene que deshacer? Vim deshace un solo "cambio" cada vez, similar al cambio del comando del punto \(pero a diferencia del comando del punto, los comandos ejecutados en la línea de comandos también cuentan como cambios\).

Para volver a rehacer el último cambio, ejecuta `Ctrl-R` o `:redo`. Después de haber deshecho en el texto anterior la eliminación de "dos", puedes ejecutar `Ctrl-R` para volver a traer de vuelta el texto eliminado.

Vim también tiene DESHACER que puedes ejecutar con `U`. Este comando deshace todos los últimos cambios.

¿En qué es diferente `U` de `u`? Primero, `U` elimina _todos_ los cambios de la última línea cambiada, mientras que `u` solo elimina un cambio cada vez. Segundo, mientras que ejecutar `u` no cuenta como cambio, ejecutar `U` cuenta como cambio.

Volvamos a este ejemplo:

```text
uno
dos
```

Cambia la segunda línea a "tres":

```text
uno
tres
```

Cambia la segunda línea de nuevo y reemplaza ahora el texto con "cuatro":

```text
uno
cuatro
```

Si ahora en modo normal presionas `u`, verás "tres". Si presionas `u` de nuevo, verás "dos". Sin embargo, si en vez de presionar `u` cuando todavía tienes el texto "cuatro", hubieras presionado `U`, verías:

```text
uno

```

`U` pasa por alto todos los cambios intermedios y va directamente al estado original cuando comenzaste \(la línea vacía debajo del texto "uno"\). Además, como DESHACER crea un nuevo cambio en Vim, puedes DESHACER tu acción de DESHACER. `U` seguido por `U` se deshará a sí mismo. Puede presionar `U`, después `U`, después `U`, para siempre, y verás los mismos dos textos alternar hacia adelante y hacia atrás.

Personalmente no utilizo `U` porque es difícil recordar el estado original \(rara vez lo he necesitado\). Lo más que he utilizado `U` es cuando accidentalmente he pulsado `Shift-u`.

Vim establece un máximo de cuantas veces puedes deshacer en la variable `undolevels`. Puedes comprobarla con `:echo &undolevels`. Tengo establecida la mía para que sea 1000. Para cambiar la tuya a 1000 ejecuta `:set undolevels=1000`. Pero puedes establecerla al número que prefieras.

## Dividiendo los bloques

He mencionado anteriormente que `u` deshace un único "cambio", de manera similar al cambio del comando del punto. Cualquier texto introducido entre entrar en el modo insertar y salir del modo es tomado en cuenta como cambio.

Si ejecutas `iuno dos tres<Esc>` y después presionas `u`, Vim elimina el texto entero "uno dos tres" porque es considerado un cambio al completo. Esto sería aceptable cuando se trata de un texto corto, pero ¿qué pasa si has escrito varios párrafos bajo una única sesión del modo insertar sin salir de ella y después te das cuenta que has cometido un error? Si presionas `u`, todo lo que habías escrito sería eliminado. ¿No sería más útil si pudieras presionar `u` para eliminar solo una sección de tu texto?

Afortunadamente, puedes dividir los bloques de deshacer. Cuando estás escribiendo en el modo insertar, presiona `Ctrl-G u` para crear un punto de ruptura del comando deshacer. Por ejemplo, si escribes lo siguiente en el ejemplo anterior `iuno <Ctrl-G u>dos <Ctrl-G u>tres<Esc>`, después presiona `u`, así solo perderás es texto "tres". Presiona `u` una vez más para eliminar "dos". Cuando escribes un texto largo, utiliza `Ctrl-G u` de manera estratégica. Al final de cada frase, entre dos párrafos, o después de cada línea de código son buenas ubicaciones para añadir puntos de ruptura para hacer más sencillo el poder deshacer los errores si los comentes.

También es útil el crear puntos de interrupción de deshacer cuando se borran fragmentos en el modo insertar con las combinaciones de teclas `Ctrl-W` \(elimina la palabra anterior al cursor\) o `Ctrl-U` \(elimina todo el texto anterior al cursor\). Un amigo sugirió el utilizar el siguiente mapeado de teclas para que haga la acción automáticamente al pulsar dichas combinaciones de teclas:

```text
inoremap <c-u> <c-g>u<c-u>
inoremap <c-w> <c-g>u<c-w>
```

Con esto, puedes recuperar fácilmente los textos eliminados de esta manera.

## El árbol de cambios del comando deshacer

Vim almacena cada cambio que se ha escrito en un árbol del comando deshacer. Comienza con un archivo vacío. Y añades un texto nuevo:

```text
uno

```

Después añades otro texto:

```text
uno
dos
```

Deshaces los cambios una vez:

```text
uno

```

Añades un texto diferente:

```text
uno
tres
```

Vuelves a deshacer los cambios:

```text
uno

```

Y añades otro texto diferente:

```text
uno
cuatro
```

Ahora, si realizas una acción de deshacer, se eliminará el texto "cuatro" que acabas de añadir:

```text
uno

```

Si deshaces los cambios una vez más:

```text

```

Entonces perderás el texto "uno". En la mayoría de los editores de texto, volver a recuperar los textos "dos" y "tres" sería imposible, pero no con Vim. Ejecuta `g+`, verás que vuelve a aparecer el texto "uno":

```text
uno

```

Escribe `g+` de nuevo y verás a un antiguo amigo, el texto "dos":

```text
uno
dos
```

Continuemos. Volvamos a presionar `g+`:

```text
uno
tres
```

Presiona `g+` una vez más:

```text
uno
cuatro
```

En Vim, cada vez que presionas `u` y después haces un cambio diferente, Vim almacena el estado previo del cambio creando una "rama de deshacer". En este ejemplo, después de haber escrito "dos" y después presionado `u` y después haber escrito "tres", creaste una rama que almacena el estado que contenía el texto "dos". En ese momento, el árbol de deshacer contenía al menos dos nodos: el nodo principal que contenía el texto "tres" \(el cambio más reciente\) y el nodo de la rama de deshacer que contenía el texto "dos". Si hubieras realizado otra acción de deshacer y escrito la palabra "cuatro", ahora tendrías al menos tres nodos: un nodo principal que contiene el texto "cuatro" y dos nodos conteniendo los textos "tres" y "dos".

Para recorrer cada nodo del árbol de deshacer, puedes utilizar `g+` para ir a un nuevo estado y `g-` para ir a un estado anterior. La diferencia entre `u`, `Ctrl-R`, `g+`, y `g-` es que tanto `u` como `Ctrl-R` recorren solo los nodos _principales_ en el árbol de deshacer, mientras que `g+` y `g-` recorren _todos_ los nodos en árbol de cambios del comando deshacer.

El árbol de cambios de deshacer no es sencillo de visualizar. He encontrado el complemento [vim-mundo](https://github.com/simnalamburt/vim-mundo) muy útil a la hora de ayudar a visualizar el árbol de cambios de deshacer. Dedícale algún tiempo para jugar con él.

## Modo persistente en deshacer

Si inicias Vim y abres un archivo nuevo e inmediatamente presionas `u`, Vim probablemente mostrará un mensaje de advertencia como este "_Ya en el cambio más antiguo_" \(o en inglés: "_Already at oldest change_"\). Vim puede preservar un historial de acciones de deshacer en un archivo mediante el comando `:wundo`.

Crea un archivo llamado `misnúmeros.txt`. Y escribe en el:

```text
uno
```

Después escribe otra línea con el texto "dos" \(asegúrate que cada línea cuenta como un cambio\):

```text
uno
dos
```

Escribe otra línea con el texto "tres":

```text
uno
dos
tres
```

Ahora crea el archivo de deshacer. La sintaxis para ello es `:wundo misnúmeros`. Si necesitas sobreescribir un archivo de deshacer ya existente, puedes añadir `!` después de `wundo`.

```text
:wundo! misnúmeros.undo
```

Después sal de Vim.

Por ahora deberías tener los archivos `misnúmeros.txt` y `misnúmeros.undo` en tu directorio. Ahora abre de nuevo el archivo `misnúmeros.txt` y trata de presionar `u`. No ocurre nada. No has realizado ningún cambio desde que has abierto el archivo. Ahora carga el historial de deshacer leyendo el archivo en el que se guarda el historial y que hemos creado antes, con el comando `:rundo`:

```text
:rundo misnúmeros.undo
```

Ahora, si presionas `u`, Vim elimina "tres". Presiona `u` de nuevo para eliminar "dos". ¡Es como si nunca hubieras cerrado Vim!

Si quieres tener la funcionalidad del comando deshacer de forma persistente de forma automática, una forma de hacerlo es añadiendo estas líneas en tu archivo vimrc:

```text
set undodir=~/.vim/undo_dir
set undofile
```

El comando anterior pone todos los archivos de historial de deshacer en una carpeta centralizada en tu equipo, en este caso dentro del directorio `~/.vim`. El nombre `undo_dir` es aleatorio, y puedes poner el quieras. El ajuste `set undofile` le dice a Vim que debe activar la funcionalidad `undofile` porque está desactivada de manera predeterminada. Ahora todo lo que guardes, Vim automáticamente creará y actualizará el archivo relevante dentro del directorio `undo_dir` \(asegúrate de que has creado la carpeta `undo_dir` dentro de la carpeta `~/.vim` antes de ejecutar esto\).

## Viajar en el tiempo

¿Quién dice que los viajes en el tiempo no existen? Vim puede viajar al estado de un texto en el pasado mediante el comando `:earlier`.

Si tienes este texto:

```text
uno
```

Y después escribe otra línea:

```text
uno
dos
```

Si has escrito "dos" hace menos de 10 segundos, puedes regresar a un estado del archivo en el que la palabra "dos" no existía hace 10 segundos mediante:

```text
:earlier 10s
```

Puedes utilizar `:undolist` para ver el último cambio que se ha realizado. `:earlier` también acepta minutos \(`m`\), horas \(`h`\) y días \(`d`\) como argumentos.

```text
:earlier 10s    Ir al estado en que se encontraba el archivo hace 10 segundos
:earlier 10m    Ir al estado en que se encontraba el archivo hace 10 minutos
:earlier 10h    Ir al estado en que se encontraba el archivo hace 10 horas
:earlier 10d    Ir al estado en que se encontraba el archivo hace 10 días
```

Además, también acepta un argumento de conteo para decirle a Vim que vaya a un estado anterior que está un número de posiciones establecido por ese argumento de conteo. Por ejemplo, si escribes `:earlier 2`, Vim regresará a un estado anterior que estaba dos cambios atrás. Sería similar a escribir `g-` dos veces. Por último, también puedes decirle a Vim `:earlier` para ir a un estado anterior del texto en que estaba hace 10 veces que se guardó con `:earlier 10f`.

Los mismo tipos de argumentos que funcionan con `:earlier` también se aplican a: `:later`.

```text
:later 10s     Ir al estado en que se encontraba el archivo 10 segundos después
:later 10m     Ir al estado en que se encontraba el archivo 10 minutos después
:later 10h     Ir al estado en que se encontraba el archivo 10 horas después
:later 10d     Ir al estado en que se encontraba el archivo 10 días después
:later 10      Ir a un nuevo estado 10 veces posterior 
:later 10f     Ir a un estado grabado 10 veces más tarde
```

## Aprendiendo a deshacer de la manera más inteligente

`u` y `Ctrl-R` son dos comandos indispensables de Vim. Apréndalos en primer lugar. Personalmente no utilizo DESHACER en mi manera de trabajo con Vim, sin embargo creo que está bien saber que existe esa posibilidad. Después, aprende a utilizar `:earlier` y `:later` utilizando primero los argumentos de tiempo. Después de eso, tómate tu tiempo para entender el concepto de árbol de cambios de deshacer. El complemento [vim-mundo](https://github.com/simnalamburt/vim-mundo) me ha ayudado mucho. Practica escribiendo los textos de este capítulo y comprueba cómo cómo funciona ese árbol de deshacer cuando haces cada cambio. Una vez que lo entiendas, nunca volverás a ver el sistema de deshacer de la misma manera.

Antes de este capítulo, has aprendido a encontrar cualquier texto en un proyecto, con _undo_ o deshacer, puedes encontrar cualquier texto en el tiempo. Ahora eres capaz de buscar cualquier texto por su localización o en el tiempo en el que fue escrito. Has conseguido la Vim-omnipresencia.

