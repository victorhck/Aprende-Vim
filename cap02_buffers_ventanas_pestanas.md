# Capítulo 2: Buffers, ventanas y pestañas

Si has utilizado un editor de texto moderno, probablemente estás familiarizado con las ventanas y las pestañas. Vim tiene tres abstracciones en vez de dos: _buffers_, ventanas y pestañas.

En este capítulo veremos cómo funcionan en Vim los _buffers_, ventanas y pestañas.

Antes de comenzar, debes asegurarte que la opción `set hidden` está presente en tu archivo `vimrc`. Sin este ajuste, cada vez que cambies a un _buffer_, Vim te pedirá que guardes el archivo \(no quieres eso si quieres moverte rápidamente entre distintos _buffers_. Para más información, echa un vistazo a la ayuda de Vim `:h hidden`.

## _Buffers_

Un _buffer_ es un espacio en la memoria en el que puedes escribir y editar algún texto. Cuando abres un nuevo archivo en Vim, su contenido estará ligado a un nuevo _buffer_: 1. Dentro de Vim, abre un nuevo _buffer_ con el comando `:new` \(crea un nuevo _buffer vacío\) 2. Desde tu terminal, abre un nuevo archivo llamado `file1.js` \(crea un nuevo_ buffer\* con `file1.js` ligado a el\)

Si tu _buffer_ no está ligado todavía a un archivo pero quieres guardar su contenido, puedes guardarlo mediante `:w </ruta/al/archivo>`.

```bash
vim file1.js
```

![un \*buffer\* mostrando resaltado de texto](.gitbook/assets/screen-one-buffer-file1-highlighted.png)

Lo que se muestra en la imagen anterior es el _buffer_ del archivo `file1.js`. Cada vez que abrimos un nuevo archivo, Vim crea un nuevo _buffer_.

Sal de Vim. Esta vez, abre dos nuevos archivos:

```bash
vim file1.js file2.js
```

![one buffer displayed.png](.gitbook/assets/screen-one-buffer%20%281%29.png)

Vim muestra el _buffer_ de `file1.js`, pero ha creado dos _buffers_: el buffer de `file1.js` y el _buffer_ de `file2.js`. Puedes mostrar todos los _buffers_ mediante el comando `:buffers` \(como alternativa también puedes usar `:ls` o `:files`\).

![comando de buffers mostrando 2 buffers](.gitbook/assets/screen-one-buffer-buffers-command.png)

Hay varias formas de poder desplazarse por los _buffers_: 1. `:bnext` o de manera abreviada puedes utilizar `:bn` para ir al _buffer_ siguiente \(`:bprevious` o `:bp` para ir al _buffer_ previo\). 2. `:buffer` + nombre\_de\_archivo. De manera abreviada puedes utilizar `:b`.Vim puede autocompletar el nombre de archivo con `tab`. 3. `:buffer` + `n`, donde `n` es el número del _buffer_. También puedes reemplazar `:buffer` por `:b`. Por ejemplo, escribiendo: `:buffer 2` o `:b 2` te llevará al _buffer_ número 2. 4. Salta a la antigua posición en la lista de saltos con `Ctrl-o` y a la nueva posición con `Ctrl-i`. Estos no son métodos específicos para _buffers_, pero pueden ser utilizados para saltar entre diferentes _buffers_. Hablaremos más sobre los saltos en el capítulo 5. 5. Ir al _buffer_ previamente editado con `Ctrl-^`.

Una vez que Vim crea un _buffer_, este permanecerá en tu lista de _buffers_. Para eliminarlo, puedes escribir `:bdelete`. También acepta un número de _buffer_ \(`:bdelete 3` para eliminar el _buffer_ \#3\) o un nombre de archivo \(`:bdelete` después utiliza `tab` para autocompletar el nombre\).

La parte más difícil de aprender para mí sobre los _buffer_ fue visualizar cómo funcionan. Una forma de hacerlo es imaginando mis _buffers_ como un mazo de cartas. Si tengo 3 _buffers_, tengo una pila de 3 cartas. Si veo `file1.js` que se muestra en el _buffer_, entonces la carta de `file1.js` es la que está en la parte superior del mazo. No puedo ver las cartas `file2.js` ni `file3.js`, pero sé que están en el mazo de cartas. Si cambio el buffer a `file3.js`, estoy poniendo la carta de `file1.js` en el mazo y poniendo la carta de `file3.js` en la parte superior del mazo.

Si no has usado Vim anteriormente, este es un nuevo concepto. Tómate tu tiempo para entenderlo. Cuando estés preparado, movámonos a las ventanas.

## Saliendo de Vim

Si tienes varios _buffers_ abiertos y has realizado algunos cambios, puedes cerrarlos todos y salir de Vim mediante el comando:

```text
:qall
```

Si quieres salir de Vim sin guardar los cambios realizados, simplemente añade `!` al final del comando anterior:

```text
:qall
```

## Ventanas

Una ventana es una división gráfica en un _buffer_. Puedes tener múltiples ventanas. La mayoría de los editores de texto tienen la posibilidad de mostrar múltiples ventanas. A continuación puedes ver una captura del editor VSCode mostrando 3 ventanas.

![buffers command showing 2 buffers](.gitbook/assets/screen-vscode-3-windows.png)

Abramos el archivo `file1.js` de nuevo desde la terminal:

```bash
vim file1.js
```

![one buffer displayed.png](.gitbook/assets/screen-one-buffer.png)

Anteriormente he dicho que estás viendo el _buffer_ de `file1.js`. Aunque esto es correcto, es incompleto. Estás viendo el _buffer_ de `file1.js` mostrado a través de **una ventana**. La ventana es el medio por el que estás viendo el _buffer_.

No abandones Vim todavía. Ejecuta:

```text
:split file2.js
```

![divisi&#xF3;n horizontal de la ventana](.gitbook/assets/screen-split-window.png)

Ahora estás viendo dos _buffers_ mediante **dos ventanas**. La ventana superior muestra el _buffer_ `file2.js`. La ventana inferior muestra el _buffer_ `file1.js`.

Por cierto, si quieres navegar entre ventanas, puedes utilizar estos comandos:

```text
Ctrl-W h    Mueve el cursor a la ventana de la izquierda
Ctrl-W j    Mueve el cursor a la ventana inferior
Ctrl-W k    Mueve el cursor a la ventana superior
Ctrl-W l    Mueve el cursor a la ventana de la derecha
```

Ahora ejecuta:

```text
:vsplit file3.js
```

![ventana dividida verticalmente y horizontalmente](.gitbook/assets/screen-split-window-vertically-and-horizontally.png)

Estás viendo tres ventanas mostrando tres _buffers_. La ventana superior izquierda muestra el buffer `file3.js`, la ventana superior derecha muestra el _buffer_ `file2.js`, y la ventana inferior muestra el _buffer_ `file1.js`.

Puedes tener múltiples ventana mostranto el mismo _buffer_. Mientras estás en la ventana superior izquierda, escribe:

```text
:buffer file2.js
```

![divisi&#xF3;n vertical y horizontal con dos archivos file2.js](.gitbook/assets/screen-split-window-vertically-and-horizontally-two-file2.png)

Ahora las dos ventanas superiores, tanto la izquierda como la derecha, están mostrando el _buffer_ `file2.js`. Si comienzas a escribir en la superior izquierda, verás que el contenido de la superior izquierda y superior derecha está cambiando en tiempo real.

Para cerrar la ventana actual, puedes ejecutar `Ctrl-W c` o escribir el comando `:quit`. Cuando cierras una ventana, el _buffer_ todavía permanece ahí \(de nuevo, para ver los _buffers_, puedes utilizar `:buffers, :ls, :files`\).

Aquí hay algunos de los comandos para las ventanas en el modo normal:

```text
Ctrl-W v    Abre una nueva división vertical
Ctrl-W s    Abre una nueva división horizontal
Ctrl-W c    Cierra una ventana
Ctrl-W o    Hace que la ventana actual sea la única en la pantalla y cierra las demás ventanas
```

Y aquí hay una lista de los comandos para las ventanas:

```text
:vsplit nombre_de_archivo    Divide la ventana verticalmente
:split nombre_de_archivo     Divide la ventana horizontalmente
:new nombre_de_archivo       Crea una nueva ventana
```

Para más información, echa un vistazo a la ayuda de Vim `:h window`. Dedícale un tiempo para entenderlos.

## Pestañas

Una pestaña en una colección de ventanas. Piensa en ello como en un diseño para ventanas. En la mayoría de los editores modernos \(y en los modernos navegadores de internet\), una pestaña significa un archivo/página abierta y cuando la cierras, el archivo/página desaparece. En Vim, una pestaña no representa un archivo abierto. Cuando cierras una pestaña en Vim, no estás cerrando un archivo. Recuerda, Vim almacena los archivos en la memoria mediante los _buffers_. Cerrar una pestaña \(o una ventana\) no hace que ese archivo desaparezca de los _buffers_.

Veamos las pestañas de Vim en acción. Abre `file1.js`:

```bash
vim file1.js
```

Para abrir `file2.js` en una nueva pestaña:

```text
:tabnew file2.js
```

![captura que muestra 2 pesta&#xF1;as](.gitbook/assets/screen-tab2.png)

También puedes permitir que Vim autocomplete el archivo a abrir en una _nueva pestaña_ presionando `tab`.

A continuación puedes encontrar una lista de navegaciones útiles por las pestañas:

```text
:tabnew archivo.txt    Abre archivo.txt en una nueva pestaña
:tabclose              Cierra la pestaña actual
:tabnext            Ir a la próxima pestaña
:tabprevious        Ir a la pestaña previa
:tablast            Ir a la última pestaña
:tabfirst           Ir a la primera pestaña
```

También puedes ejecutar `gt` para ir a la siguiente pestaña \(con `Gt` vas a la pestaña previa\). También puedes pasar un número como argumento a `gt`, donde el número corresponde al número de la pestaña. Para ir a la tercera pestaña, ejecuta `3gt`.

Una ventaja de tener varias pestañas es que puedes tener diferentes disposiciones de ventanas en diferentes pestañas. Tal vez deseas que tu primera pestaña tenga 3 ventanas verticales y la segunda pestaña tenga un diseño de ventanas horizontal y vertical mixto. ¡Las pestañas son la herramienta perfecta para el trabajo!

![primera pesta&#xF1;a con m&#xFA;ltiples ventanas](.gitbook/assets/tabs-file1js.png)

![segunda pesta&#xF1;a con m&#xFA;ltiples ventanas](.gitbook/assets/tabs-file2js.png)

Para arrancar Vim con múltiples pestañas, puedes ejecutar esto desde la terminal:

```bash
vim -p archivo1.js archivo2.js archivo3.js
```

## Moviéndote en 3D

Moverse entre ventanas es como viajar en dos dimensiones a través de los ejes X-Y en coordenadas cartesianas. Te puedes mover a la parte superior, derecha, inferior e izquierda con `Ctrl-W h/j/k/l`.

![movimiento cartesiano en ejes x e y](.gitbook/assets/cartesian-xy.png)

Moverse entre _buffers_ es cini viajar a través del eje Z en coordenadas Cartesianas. Imagina que los _buufers_ de tus archivos están alineados a través del eje Z. Puedes desplazarte por el eje Z un _buffer_ cada vez con `:bnext` y `:bprevious`. También puedes saltar a cualquier coordenada en el eje Z con `:buffer nombre_de_archivo/número de buffer`.

![movimiento cartesiano en el eje z](.gitbook/assets/cartesian-z.png)

Te puedes mover en un _espacio tridimensional_ combinando los movimientos de ventanas y _buffer_. Te puedes mover a la parte superior, derecha, inferior o la ventana izquierda \(navegación en ejes X-Y\) con las teclas de navegación por ventanas. Como cada ventana contiene _buffers_, te puedes mover hacia adelante o hacia atrás \(navigación en Z\) con los movimientos de _buffer_.

![movimiento cartesiano en ejes x, y y z](.gitbook/assets/cartesian-xyz.png)

## Utilizando los _buffers_, ventanas y pestañas de la manera más inteligente

Has aprendido cómo funcionan los _buffers_, las ventanas y las pestañas en Vim. Para utilizarlas de manera eficiente, necesitas entender para qué están diseñadas y utilizarlas en tu propia forma de trabajar. Cada persona tiene una manera diferente de trabajar, aquí tienes un ejemplo.

Yo utilizaría los _buffers_ para abrir todos los archivos necesarios para realizar la tarea que queremos hacer. Esta tarea puede necesitar que abras siete o siete veces siete archivos en _buffers_, pero no importa porque Vim puede manejar muchos _buffers_ antes de que empiece a ir lento. Además abrir muchos _buffers_ no llena mi pantalla. Solo verá un _buffer_ \(asumiendo que solo tengo una ventana\) cada vez y que puedo volar rápidamente a cualquier _buffer_ que desee.

Yo utilizaría múltiples ventanas para ver múltiples _buffers_ a la vez, normalment cuando tengo archivos que muestran las diferencias entre archivos, comparando código o siguiendo el flujo de un código. Normalmente no tengo más de tres ventanas abiertas a la vez porque mo pantalla estaría llena. Cuando termino, cierro cualquier ventana extra. Prefiero tener las mínimas venttanas posibles.

Utilizaría una pestaña para gestionar los código del lado cliente y una pestaña diferente para los códigos de _back-end_. Personalmente, no utilizo pestañas en mi forma de trabajo. Utilizo ventanas de [tmux](https://github.com/tmux/tmux/wiki) como sustituto de las pestañas de Vim. Mi flujo de trabajo con tmux es similar a la forma de trabajar de las pestañas en Vim. En vez de tener abiertas múltiples **pestañas de Vim** para cada contexto, yo abro **ventanas de tmux** \(una ventana de tmux para código del lado cliente y otra ventana para código de _back-end_\).

Mi forma de trabajar puede ser diferente de la tuya debido a tu forma de editar y eso está bien. Experimenta hasta descubrir tu forma de trabajar y encontrar qué es lo que mejor se adapta a ti.
