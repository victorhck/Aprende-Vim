# Capítulo 1: Ejecutando Vim

En este capítulo, aprenderás diferentes maneras de ejecutar Vim desde la terminal. Te recomiendo encarecidamente que utilices Vim desde la terminal mientras estás escribiendo. Actualmente estoy usando Vim 8.2. Los comandos escritos deberían funcionar bien si utilizas una versión menor, pero quizás algunos comandos podrían no estar disponibles.

## Instalar Vim

No voy a entrar en detalles de cómo instalar Vim (porque hay demasiadas distribuciones diferentes ahí fuera). La buena noticia es, la mayoría de los equipos con Unix debería venir con Vim instalado. Si no es así, la mayoría de las distribuciones tienen una manera sencilla de instalar Vim.

Para más información de cómo descargar, echa un vistazo a la web oficial de descargas de Vim:

[https://www.vim.org/download.php](https://www.vim.org/download.php)

De manera alternativa, también puedes echar un vistazo al [repositorio oficial de Vim en GitHub](https://github.com/vim/vim).

## `vim`

Ahora que ya tienes Vim instalado, ejecuta esto desde la terminal:

```bash
vim
```

Deberías ver una pantalla de bienvenida. Ahí será donde trabajarás con tus archivos. A diferencia de la mayoría de editores de texto e IDE's, Vim es un editor modal. Si quieres escribir "Hola", necesitas cambiar al modo Insertar con `i`. Escribe: `ihello<Esc>`.

## Saliendo de Vim

Hay muchas maneras de salir del editor Vim. La más común es escribir:

```
:quit
```

También puedes escribir `:q` de forma abreviada. El comando es un comando del modo de comandos (otro de los modos de Vim). Si escribes `:` en el modo Normal, el cursor se moverá a la parte inferior de la pantalla donde podrás escribir algunos comandos. Aprenderás más del modo de línea de comandos más tarde en el capítulo 15. Si estás en el modo Insertar, al escribir `:` esto literalmente escribirá el caracter ":" en la pantalla. En este caso, necesitas regresar al modo Normal. Pulsa sobre la tecla `<Esc>` para cambiar a ese modo Normal. Por cierto, puedes regresar al modo Normal desde el modo Línea de comandos pulsando `<Esc>`. Notarás que puedes "escapar" de diversos modos de Vim de vuelta al modo Normal pulsando la tecla `<Esc>`.

Para guardar tus cambios, escribe:

```
:write
```

También puedes escribir `:w` de forma abreviada. Si este es un archivo nuevo, necesitarás darle un nombre antes de guardarlo. Vamos a llamrlo `archivo.txt`. Ejecuta:

```
:w archivo.txt
```

Para guardarlo y salir de Vim, puedes combinar los comandos `:w` y `:q`:

```
:wq
```

Para salir sin guardar los cambios, añade `!` después de  `:q` para forzar el cierre de Vim:

```
:q!
```

Hay otras maneras de salir de Vim, pero estas que hemos visto las utilizarás diariamente.

## Ayuda

A lo largo del libro, te referiré a varias páginas de ayuda de Vim. Puedes acceder a la página de ayuda escribiendo el siguiente comando en la línea de comandos:

```
:help
```

O `:h` de forma abreviada. También puedes indicarle al comando `:h` el tema sobre el que quiere aprender pasándoselo como un argumento. Por ejemplo, para aprender más sobre diferentes maneras de salir de Vim, puedes escribir:

```
:h write-quit
```

## Abriendo un archivo

Desde la terminal, para abrir el archivo `hola1.txt`, ejecuta:

```bash
vim hola1.txt
```

También puedes abrir varios archivos a la vez:

```bash
vim hola1.txt hola2.txt hola3.txt
```

Vim abre `hola1.txt`, `hola2.txt` y `hola3.txt` en *buffers* separados. Aprenderás más sobre los *buffers* en el próximo capítulo.

## Argumentos

Puedes pasarle al comando de la terminal `vim` diferentes banderas y opciones.

Para comprobar la versión actual de Vim, ejecuta:

```bash
vim --version
```

Esta bandera te muestra la versión de Vim y todas la funcionalidades disponibles marcadas con `+` o `.` Algunas de estas características en esta guía requieren que están disponibles algunas funcionalidades. Por ejemplo, explorarás el historial de la línea de comandos de Vim en el capítulo 15 con el comando `:history`. Tu editor Vim necesita tener la funcionalidad `+cmdline_history` disponible para que el comando funcione.

Esto podría sonar a tener que hacer un montón de trabajo, pero las descargas de Vim más populares deberían tener todas las funcionalidades necesarias. En mi equipo Mac (N.d.T: El equipo Mac del escritor del libro en Inglés) la versión de Vim que tengo instalada desde `brew install vim` tiene todas las funcionalidades que necesito. (N.d.T: En el Vim instalado desde los repositorios de la distribución de GNU/Linux openSUSE que utilizo, también tiene todo lo necesario).

De manera alternativa, para ver la versión estando *dentro* de Vim, también puedes ejecutar este comando:

```
:version
```

Más adelante, probablemente comenzarás a añadir complementos a Vim. Si alguna vez necesitas ejecutar Vim sin ningún complemento, podrás hacerlo pasándole la bandera `noplugin`:

```
vim --noplugin
```

Si quieres abrir el archivo `hola.txt` e inmediatamente ejecutar un comando, podrás hacerlo añadiendo al comando `vim` la opción `+{cmd}`.

En Vim, puedes sustituir texto con el comando `:s` (abreviatura de `:substitute`). Si quieres abrir `hola.txt` y sustituir todo el texto "foo" con "bar", ejecuta:

```bash
vim +%s/foo/bar/g hola.txt
```

Estos comandos también pueden ser apilados:

```bash
vim +%s/foo/bar/g +%s/bar/baz/g +%s/baz/donut/g hola.txt
```

Vim primero reemplazará todas las instancias que encuentre de "foo" con "bar", después reemplazará "bar" con "baz", después reemplazará "baz" con "donut" (aprenderás sobre cómo sustituir texto en el capítulo 12).

También puedes añadir la bandera `c` seguida con el comando en vez de la sintáxis `+`:

```bash
vim -c %s/foo/bar/g hola.txt
vim -c %s/foo/bar/g -c %s/bar/baz/g -c %s/baz/donut/g hola.txt
```

Durante todo este libro aprenderás varios comandos para la Línea de comandos de Vim. Todos estos comandos pueden ser ejecutados al inicio.

## Abriendo múltiples ventanas

Puedes ejecutar Vim en ventanas divididas de manera horizontal y vertical con `o` y `O`, respectivamente.

Para abrir Vim con dos ventanas horizontales, ejecuta:

```bash
vim -o
```

Para abrir Vim con 5 ventanas horizontales, ejecuta:

```bash
vim -o5
```

Para abrir Vim con 5 ventanas horizontales y ocupar las dos primeras con los archivos `hola1.txt` and `hola2.txt`, ejecuta:

```bash
vim -o5 hola1.txt hola2.txt
```

Para abrir Vim con dos ventanas verticales, 5 ventanas verticales y 5 ventanas verticales con 2 archivos:

```bash
vim -O
vim -O5
vim -O5 hola1.txt hola2.txt
```

## Suspender

Si necesitar suspender la ejecución de Vim mientras estás a la mitad de una edición de texto, puedes presionar la combinación de teclas `Ctrl-Z`. De manera alternativa, también puedes ejecutar los comandos `:stop` o `:suspend`.

Para volver a retomar la sesión suspendida de Vim, ejecuta `fg` desde la terminal.

## Arrancando Vim de la manera más inteligente

Puedes pasarle al comando `vim` diferentes opciones y banderas, como a cualquier otro comando de la terminal. Una de las opciones es el comando de la línea de comandos (`+{cmd}` o `c cmd`). Cuantos más comandos para la propia línea de comandos de Vim aprendas en este libro, verás que puedes utilizarlos al arrancar Vim.

Para aprender más sobre las diferentes opciones que puedes utilizar desde la terminal, echa un vistazo mediante el comando `man vim`. Para aprender más sobre los modos de Vim y los comandos, echa un vistazo con `:help`.

