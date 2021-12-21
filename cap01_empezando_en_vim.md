# Capítulo 1: Ejecutando Vim

En este capítulo, aprenderás diferentes maneras de ejecutar Vim desde la terminal. He utilizado Vim 8.2 para escribir esta guía. Los comandos escritos deberían funcionar bien si utilizas Neovim o una versión menor de Vim, pero quizás algunos comandos podrían no estar disponibles.

## Instalar Vim

No voy a entrar en detalles de cómo instalar Vim en un equipo específico. La buena noticia es que la mayoría de los equipos con sistemas operativos basados en Unix debería venir con Vim instalado. Si no es así, la mayoría de las distribuciones tienen una manera sencilla de instalar Vim.

Para más información de cómo descargar, echa un vistazo a la web oficial de descargas de Vim o el repositorio oficial de Vim en Github:

* [Sitio web de Vim](https://www.vim.org/download.php)
* [Repositorio de Vim en GitHub](https://github.com/vim/vim)

## El comando Vim

Ahora que ya tienes Vim instalado, ejecuta esto desde la terminal:

```bash
vim
```

Deberías ver una pantalla de bienvenida. Ahí será donde trabajarás con tus archivos. A diferencia de la mayoría de editores de texto e IDE's, Vim es un editor modal. Si quieres escribir "Hola", necesitas cambiar al modo Insertar con `i`. Escribe: `ihola<Esc>`.

## Saliendo de Vim

Hay muchas maneras de salir del editor Vim. La más común es escribir:

```text
:quit
```

También puedes escribir `:q` de forma abreviada. El comando es un comando del modo línea de comandos \(otro de los modos de Vim\). Si escribes `:` en el modo normal, el cursor se moverá a la parte inferior de la pantalla donde podrás escribir algunos comandos. Aprenderás más del modo de línea de comandos más tarde en el capítulo 15. Si estás en el modo insertar, al escribir `:` esto literalmente escribirá el carácter ":" en la pantalla. En este caso, necesitas regresar al modo normal. Pulsa sobre la tecla `<Esc>` para cambiar a ese modo normal. Por cierto, puedes regresar al modo normal desde el modo línea de comandos pulsando `<Esc>`. Aprenderás que puedes "escapar" de diversos modos de Vim de vuelta al modo normal pulsando la tecla `<Esc>`.

## Guardando un fichero

Para guardar tus cambios, escribe:

```text
:write
```

También puedes escribir `:w` de forma abreviada. Si este es un archivo nuevo, necesitarás darle un nombre antes de guardarlo. Vamos a llamarlo `archivo.txt`. Ejecuta:

```text
:w archivo.txt
```

Para guardarlo y salir de Vim, puedes combinar los comandos `:w` y `:q`:

```text
:wq
```

Para salir sin guardar los cambios, añade `!` después de `:q` para forzar el cierre de Vim:

```text
:q!
```

Hay otras maneras de salir de Vim, pero estas que hemos visto las utilizarás diariamente.

## Ayuda

A lo largo de la guía, te referiré a varias páginas de ayuda de Vim. Puedes acceder a la página de ayuda escribiendo `:help {comando}` \(puedes usar `:h` como abreviatura de _help_\). Puedes pasarle al comando `:h` un tema o el nombre de un comando como argumento. Por ejemplo, para aprender diferentes maneras de salir de Vim, escribe:

```text
:h write-quit
```

¿Cómo sabía que había que buscar "write-quit"? La verdad, no lo sabía. Solo escribí `:h`, y después "quit" y después pulsé sobre la tecla `<Tab>`. Vim mostró las palabras clave relevantes entre las que escoger. Si alguna vez necesitas buscar algo \("Me gustaría que Vim pudiera hacer esto..."\), simplemente escribe `:h` y prueba algunas palabras clave, y después pulsa `<Tab>`. \(N.del T: lo mejor es escribir términos en inglés, para que Vim los encuentre entre sus temas de ayuda\).

## Abriendo un archivo

Desde la terminal, para abrir el archivo `hola1.txt`, ejecuta:

```bash
vim hola1.txt
```

También puedes abrir varios archivos a la vez:

```bash
vim hola1.txt hola2.txt hola3.txt
```

Vim abre `hola1.txt`, `hola2.txt` y `hola3.txt` en _buffers_ separados. Aprenderás más sobre los _buffers_ en el próximo capítulo.

## Argumentos

Puedes pasarle al comando de la terminal `vim` diferentes banderas y opciones.

Para comprobar la versión actual de Vim, ejecuta:

```bash
vim --version
```

Esto te muestra la versión de Vim y todas la funcionalidades disponibles marcadas con `+` o `-`. Algunas de estas características en esta guía requieren que estén disponibles algunas funcionalidades. Por ejemplo, explorarás el historial de la línea de comandos de Vim en un capítulo posterior con el comando `:history`. Tu editor Vim necesita tener la funcionalidad `+cmdline_history` disponible para que el comando funcione. Hay muchas posibilidades de que el Vim que tienes instalado tenga las funcionalidades necesarias, especialmente si lo has instalado desde las fuentes más conocidas.

Muchas cosas que puedes hacer desde la terminal, también pueden hacerse dentro de Vim. Para ver la versión desde el propio Vim, puedes ejecutar esto:

```text
:version
```

Si quieres abrir el archivo `hola.txt` e inmediatamente ejecutar un comando, podrás hacerlo añadiendo al comando `vim` la opción `+{cmd}`.

En Vim, puedes sustituir texto con el comando `:s` \(abreviatura de `:substitute`\). Si quieres abrir `hola.txt` y sustituir todo el texto "donut" con "rosquilla", ejecuta:

```bash
vim +%s/donut/rosquilla/g hola.txt
```

Estos comandos también pueden ser apilados:

```bash
vim +%s/tarta/rosquilla/g +%s/rosquilla/huevo/g +%s/huevo/donut/g hola.txt
```

Vim primero reemplazará todas las instancias que encuentre de "tarta" con "rosquilla", después reemplazará "rosquilla" con "huevo", después reemplazará "huevo" con "donut" \(aprenderás sobre cómo sustituir texto en un capítulo posterior\).

También puedes añadir la opción `-c` seguida de un comando de Vim en vez de la sintaxis `+`:

```bash
vim -c %s/tarta/rosquilla/g hola.txt
vim -c %s/tarta/rosquilla/g -c %s/rosquilla/huevo/g -c %s/huevo/donut/g hola.txt
```

## Abriendo múltiples ventanas

Puedes ejecutar Vim en ventanas divididas de manera horizontal y vertical con las opciones `-o` y `-O`, respectivamente.

Para abrir Vim con dos ventanas horizontales, ejecuta:

```bash
vim -o2
```

Para abrir Vim con 5 ventanas horizontales, ejecuta:

```bash
vim -o5
```

Para abrir Vim con 5 ventanas horizontales y ocupar las dos primeras con los archivos `hola1.txt` y `hola2.txt`, ejecuta:

```bash
vim -o5 hola1.txt hola2.txt
```

Para abrir Vim con dos ventanas verticales, 5 ventanas verticales y 5 ventanas verticales con 2 archivos:

```bash
vim -O2
vim -O5
vim -O5 hola1.txt hola2.txt
```

## Suspender

Si necesitar suspender la ejecución de Vim mientras estás a la mitad de una edición de texto, puedes presionar la combinación de teclas `Ctrl-z`. También puedes ejecutar los comandos `:stop` o `:suspend`. Para volver a retomar la sesión suspendida de Vim, ejecuta `fg` desde la terminal.

## Arrancando Vim de la manera más inteligente

El comando `vim` puede adoptar diferentes opciones, como a cualquier otro comando de la terminal. Dos opciones permiten pasarle a Vim un comando como parámetro: \(`+{cmd}` y `-c cmd`\). Cuantos más comandos aprendas mientras lees esta guía, comprueba si puedes utilizarlos al iniciar Vim. También al ser un comando para la terminal, puedes combinar `vim` con muchos otros comandos. Por ejemplo, puedes redireccionar la salida del comando `ls` para que pueda ser editado en Vim con `ls -l | vim -`.

Para aprender más sobre el comando `vim` en la terminal, echa un vistazo a la ayuda `man vim`. Para aprender más sobre el editor Vim, continúa leyendo esta guía junto con el comando en Vim `:help`.

