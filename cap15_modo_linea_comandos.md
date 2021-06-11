# Capítulo 15: El modo de línea de comandos

En los últimos tres capítulos, aprendiste cómo utilizar los comandos de búsqueda \(`/`, `?`\), sustitución \(`:s`\), el comando global \(`:g`\) y el comando externo \(`!`\). Estos son ejemplos de comandos para la línea de comandos.

En este capítulo, aprenderás varios trucos para el modo línea de comandos.

## Entrar y salir del modo línea de comandos

El modo línea de comandos es un modo en sí mismo, como el modo normal, el modo insertar y el modo visual. Cuando estás en este modo, el cursor se sitúa en la parte inferior de la pantalla en donde se pueden escribir diferentes comandos.

Hay 4 comandos diferentes que puedes introducir cuando estás en el modo línea de comandos:

* Patrones de búsqueda \(`/`, `?`\)
* Comandos de la línea de comandos \(`:`\)
* Comandos externos \(`!`\)

Puedes entrar en el modo línea de comandos desde el modo normal o desde el modo visual.

Para dejar el modo línea de comandos, puedes utilizar `<Esc>`, `Ctrl-C, o Ctrl-[`.

_Algunas veces en otras guías o libros puede que refieran a los "comandos de la línea de comandos" como "comandos Ex" y los "comandos externos" como "comandos de filtros" u "operadores bang"._

## Repitiendo el comando previo

Puedes repetir tanto el comando o comando externo ejecutado previamente mediante `@:`.

Si ejecutaste previamente `:s/foo/bar/g`, ahora ejecutando `@:` repetirá esa sustitución. Si ejecutaste `:.!tr '[a-z]' '[A-Z]'`, ejecutando `@:` repetirá el último filtro del comando externo de traducción.

## Atajos de teclado del modo línea de comandos

Mientras estás en el modo línea de comandos, puede moverte a izquierda o derecha un carácter cada vez mediante las flechas de dirección del cursor `Izquierda` o `Derecha`.

Si necesitas moverte una palabra cada vez, utiliza `Shift-Izquierda` o `Shift-Derecha` \(en algunos sistemas operativos, deberás utilizar la tecla `Ctrl` en vez de `Shift`\).

Para ir al comienzo de la línea, utiliza `Ctrl-B` (B de la palabra _begining_, comienzo en inglés). Para ir al final de la línea, utiliza `Ctrl-E` (E de la palabra _end_, final en inglés).

De manera similar al modo de insertar, dentro del modo línea de comandos, tienes tres maneras de eliminar caracteres:

```text
Ctrl-H    Elimina un caracter
Ctrl-W    Elimina una palabra
Ctrl-U    Elimina la línea entera
```

Finalmente, si quieres editar el comando como si fuera un archivo de texto normal, utiliza `Ctrl-f`.

Esto también te permite buscar entre comandos previos, editarlos y volverlos a ejecutar pulsando `<Enter` en el "modo normal de edición de línea de comandos".

## Registro y autocompletado

Mientras estás en el modo de línea de comandos, puedes insertar textos desde los registros de Vim con `Ctrl-R` de la misma manera que lo haces en el modo insertar. Si tienes la cadena "foo" guardada en el registro a, puedes insertarlo ejecutando `Ctrl-R a`. Todo lo que puedes hacer desde los registros en el modo normal, también puedes hacerlo en el modo línea de comandos.

Además, también puedes insertar la palabra que está bajo el cursor con `Ctrl-R Ctrl-W` \(puedes utilizar `Ctrl-R Ctrl-A` para la PALABRA bajo el cursor\). Para insertar la línea que está bajo el cursor utiliza `Ctrl-R Ctrl-L`. Para insertar todo el nombre del archivo bajo el cursor, usa `Ctrl-R Ctrl-F`.

También puedes autocompletar los comandos existentes. Para autocompletar el comando `echo`, mientras estás en el modo línea de comandos, escribe "ec", después pulsa `<Tab>`. Deberías ver en la parte inferior izquierda de la pantalla los comandos de Vim que empiezan con "ec" \(ejemplo: `echo echoerr echohl echomsg econ`\). Para ir a la siguiente opción, pulsa tanto `<Tab>` o `Ctrl-N`. Para ir a la opción previa, puedes pulsar tanto `<Shift-Tab>` o `Ctrl-P`.

Algunos de los comandos de la línea de los comandos aceptan nombres de archivos como argumentos. Un ejemplo es `edit`. También puedes utilizar el autocompletado en este caso. Después de escribir el comando, `:e` \(no olvides añadir un espacio al final\), presiona `<Tab>`. Vim mostrará un listado de todos los nombres de archivos relevantes entre los que puedes escoger, así no tendrás que escribirlo tu mismo.

## La ventana del historial y la ventana de la línea de comandos

Puedes consultar el historial de comandos ejecutados en la línea de comandos y buscar términos \(para eso es necesario que Vim incluya la funcionalidad `+cmdline_hist` \).

Para abrir el historial de la línea de comandos, ejecuta `:his :`. Deberías ver algo similar a esto \(pero con los comandos que tu hayas ejecutado recientemente en tu editor Vim\):

```text
##  cmd History
2  e file1.txt
3  g/foo/d
4  s/foo/bar/g
```

Vim muestra un listado de todos los comandos `:` que has ejecutado, Vim de manera predeterminada almacena 50 registros en ese historial. Para cambiar la cantidad total de comandos que Vim puede recordar a 100, deberás ejecutar `set history=100`.

Para manejar mejor ese historial de comandos de la línea de comandos lo mejor es hacerlo mediante su ventana, para ello ejecutaremos ,`q:`. Esto abrirá una ventana en la que podremos buscar y editar el historial. Supongamos que nos aparecen estas expresiones en el historial al pulsar `q:`:

```text
51  s/patróndesustituciónsuperlargo/tarta/g
52  his :
53  wq
```

Si lo que necesitas hacer es `s/patróndesustituciónsuperlargo/donut/g`, en vez de escribir el comando de nuevo, porque no reutilizar el comando que encontramos en el historial `s/patróndesustituciónsuperlargo/tarta/g`? Después de todo, la única diferencia es la palabra que se quiere sustituir, "donut" vs "tarta". Todo lo demás es lo mismo.

Después de ejecutar `q:`, encuentra ese comando `s/patróndesustituciónsuperlargo/tarta/g` en el historial \(puedes utilizar las teclas de navegación de Vim cuando estás en esta pantalla\) ¡y editarlo directamente! Cambia "tarta" por "donut" dentro de la propia ventana del historial, y después pulsa `<Enter>`. ¡Boom! Vim ejecuta `s/patróndesustituciónsuperlargo/donut/g` para ti. ¡Justo lo que querías!

De manera similar, para ver el historial de búsquedas, ejecuta `:his /` o `:his ?`. Para abrir la ventana del historial de búsquedas en donde podrás buscar y editar entradas anteriores del historial, ejecuta `q/` o `q?`.

Para salir de esta ventana, pulsa `Ctrl-C`, `Ctrl-W C`, o escribe `:quit`.

## Más comandos para la línea de comandos

Vim tiene cientos de comandos propios. Para ver todos los comandos que tiene Vim, echa un vistazo a `:h ex-cmd-index` o `:h :index`.

## Aprender el modo línea de comandos de la manera más inteligente

Comparado con los otros tres modos, el modo de línea de comandos de Vim es como una "navaja suiza" de la edición de textos. Puedes editar texto, modificar archivos y ejecutar comandos, solo por nombrar unos pocos. Este capítulo cierra la serie de los modos de Vim. Ahora que ya conoces y sabes utilizar el modo normal, insertar, visual y el modo línea de comandos de Vim ya puedes editar textos en Vim más eficaz y rápidamente.

Es la hora de movernos de los modos de Vim y aprender cómo hacer una navegación más veloz con las etiquetas de Vim.

