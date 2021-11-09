# Capítulo 16: Etiquetas

Una característica muy útil a la hora de editar texto es poder ser capaz de ir a cualquier definición rápidamente. En este capítulo aprenderás cómo utilizar las etiquetas de Vim para hacer eso.

## Descripción general de las etiquetas

Supongamos que alguien te ha enviado un nuevo código, por ejemplo:

```text
one = One.new
one.donut
```

¿`One`? ¿`donut`? Bueno, estos nombre podrían ser obvios para el desarrollador o desarrolladora que escribieran el código hace tiempo, pero ahora esas personas ya no están aquí y te corresponde a ti entender estas líneas de código tan enrevesadas. Una manera de ayudar a entender esto es seguir el código fuente donde `One` y `donut` están definidas.

Puedes buscarlas tanto con `fzf` como con `grep` (o `vimgrep`), pero en este caso es más rápido utilizar etiquetas.

Piensa en que las etiquetas son como una libreta de direcciones de contactos:

```text
Nombre   Direcciones
Iggy1    1234 Calle felicidad, 11111
Iggy2    9876 Avenida alegría, 2222
```

En vez de tener un par de nombres y direcciones, las etiquetas almacenan las definiciones emparejadas con direcciones.

Vamos a asumir que tenemos estos dos archivos escritos en lenguaje Ruby que están guardados dentro del mismo directorio:

```text
# one.rb
class One
  def initialize
    puts "Initialized"
  end

  def donut
    puts "Bar"
  end
end
```

y

```text
# two.rb
require './one'

one = One.new
one.donut
```

Para saltar a una definición, puedes utilizar la combinación de teclas `Ctrl-]` estando en el modo normal. Dentro del archivo `two.rb`, ve a la línea donde aparece `one.donut` y posiciona el cursor encima de la palabra `donut`. Pulsa `Ctrl-]`.

¡Vaya! Vim no puede encontrar el archivo de etiquetas. Necesitas primero generar el archivo de etiquetas.

## Generador de etiquetas

El Vim actual no trae incorporado un generador de etiquetas, así que tendrás que descargar un generador de etiquetas externo. Hay varias opciones entre las que escoger:

* ctags = solo para C. Disponible casi para todas las plataformas.
* exuberant ctags = Uno de los más populares. Admite muchos lenguajes. 
* universal ctags = Similar a ctags, pero más moderno.
* etags = Para Emacs. Hmm...
* JTags = Java
* ptags.py = Python
* ptags = Perl
* gnatxref = Ada

Si echas un vistazo a tutoriales de Vim en internet, muchos recomendarán [Exuberant ctags](http://ctags.sourceforge.net/). Admite [41 lenguajes de programación](http://ctags.sourceforge.net/languages.html). Yo lo utilizaba y funcionaba genial. Sin embargo, debido a que ya no está mantenido desde 2009, **Universal ctags** sería una opción más adecuada. Funciona de manera similar a **Exuberant ctags** y actualmente está mantenido.

Asumo que tienes el ctags universal instalado, vamos a generar un archivo de etiquetas básico. Ejecuta lo siguiente:

```text
ctags -R .
```

La opción `R` le dice a ctags que ejecute un escaneo desde tu ubicación actual \(`.`\). Ahora se debería poder ver un archivo `tags` en tu directorio actual. Dentro del archivo verás algo parecido a esto:

```text
!_TAG_FILE_FORMAT    2    /extended format; --format=1 will not append ;" to lines/
!_TAG_FILE_SORTED    1    /0=unsorted, 1=sorted, 2=foldcase/
!_TAG_OUTPUT_FILESEP    slash    /slash or backslash/
!_TAG_OUTPUT_MODE    u-ctags    /u-ctags or e-ctags/
!_TAG_PATTERN_LENGTH_LIMIT    96    /0 for no limit/
!_TAG_PROGRAM_AUTHOR    Universal Ctags Team    //
!_TAG_PROGRAM_NAME    Universal Ctags    /Derived from Exuberant Ctags/
!_TAG_PROGRAM_URL    <https://ctags.io/>    /official site/
!_TAG_PROGRAM_VERSION    0.0.0    /b43eb39/
One    one.rb    /^class One$/;"    c
donut    one.rb    /^  def donut$/;"    f    class:One
initialize    one.rb    /^  def initialize$/;"    f    class:One
```

El contenido de tu archivo puede ser algo diferente, dependiendo de los ajustes de tu Vim y del generador ctags. El archivo de etiquetas está compuesto por dos partes: los metadatos de la etiqueta y la lista de etiquetas. Estos metadatos \(`!TAG_FILE...`\) son normalmente controlados por el generador ctags. No entraré en detalle sobre eso aquí, pero ¡no dudes en echar un vistazo a su documentación! La lista de etiquetas es una lista de todas las definiciones indexadas por ctags.

Ahora ve al archivo `two.rb`, y coloca el cursor encima de la palabra `donut`, y escribe `Ctrl-]`. Vim te llevará al archivo `one.rb` a la línea donde está `def donut`. ¡Eureka! ¿Pero cómo hizo esto Vim?

## Anatomía de las etiquetas

Echemos un vistazo a la etiqueta del elemento `donut`:

```text
donut    one.rb    /^  def donut$/;"    f    class:One
```

El elemento de la etiqueta está compuesto por cuatro componentes: un nombre de etiqueta o `tagname`, un nombre de archivo de la etiqueta o `tagfile`, una dirección de etiqueta o `tagaddress` y opciones de la etiqueta. \(N. del T: Me referiré por sus nombres en inglés porque quizás es la nomenclatura más utilizada en los tutoriales y por quienes lo usan\).

* `donut` es el `tagname`. Cuando tu cursor está encima de la palabra "donut", Vim busca el archivo dentro del archivo de la etiqueta una línea que contenga la cadena "donut".
* `one.rb` es el `tagfile`. Vim busca un archivo llamado `one.rb`.
* `/^ def donut$/` es el `tagaddress`. `/.../` es un indicador patrón. `^` es un patrón para el primer elemento de una línea. Está seguido por dos espacios y después la cadena `def donut`. Finalmente, `$` es el patrón para el último elemento de una línea.
* `f class:One` es la opción de la etiqueta que le dice a Vim que la función `donut` es una función \(`f`\) y es parte de la clase `One`.

Echemos un vistazo de otro elemento de la lista de etiquetas:

```text
One    one.rb    /^class One$/;"    c
```

Esta línea funciona de la misma manera que el patrón `donut`:

* `One` es el `tagname`. Ten en cuenta que con las etiquetas, el primer escaneo tiene en cuenta las mayúsculas y minúsculas. Si tienes `One` y `one` en la lista, Vim priorizará `One` sobre `one`.
* `one.rb` es el `tagfile`. Vim buscará un archivo `one.rb`.
* `/^class One$/` es el patrón de `tagaddress`. Vim buscará una línea que comience \(`^`\) con `class` y termine \(`$`\) con `One`.
* `c` es una de las opciones de etiqueta posible. Como `One` es una clase de Ruby y no un procedimiento, lo marca con una `c`.

Dependiendo de qué generador de etiquetas utilices, el contenido de tu archivo de etiquetas puede tener un aspecto algo diferente. Como mínimo, un archivo de etiquetas debe tener uno de estos formatos:

```text
1.  {tagname} {TAB} {tagfile} {TAB} {tagaddress}
2.  {tagname} {TAB} {tagfile} {TAB} {tagaddress} {término} {campo} ..
```

## El archivo de etiquetas

Has aprendido que un nuevo archivo, `tags`, es creado después de ejecutar `ctags -R .`. ¿Cómo sabe Vim dónde buscar el archivo de etiquetas?

Si ejecutas `:set tags?`, podrás ver `tags=./tags,tags` \(dependiendo de tus ajustes de Vim, esto podría ser diferente\). Aquí Vim busca todas las etiquetas en la ruta del archivo actual para el caso de `./tags` y dentro del directorio actual \(la raíz de tu proyecto\) para el caso de `tags`.

También en el caso de `./tags`, Vim primero buscará un archivo de etiquetas dentro de la ruta de tu archivo actual independientemente de lo anidado que esté, después buscará un archivo de etiquetas del directorio actual \(la raíz del proyecto\). Vim detiene la búsqueda después de encontrar la primera coincidencia.

Si tu archivo `'tags'` ha dicho `tags=./tags,tags,/user/iggy/mytags/tags`, entonces Vim también buscará en el directorio `/user/iggy/mytags` un archivo de etiquetas después de finalizar la búsqueda en los directorios `./tags` y `tags`. No es necesario que almacenes tu archivo de etiquetas dentro de tu proyecto, puedes mantenerlo de manera separada.

Para añadir una ubicación de un archivo de etiquetas, simplemente ejecuta:

```text
:set tags+=ruta/a/mi/archivode/etiquetas
```

## Generando etiquetas para un proyecto extenso

Si has intentado ejecutar ctags en un proyecto extenso, puede que haya tardado mucho tiempo porque Vim también busca dentro de los directorios anidados. Si eres desarrollador o desarrolladora de Javascript, ya sabrás que `node_modules` pueden ser muy grandes. Imagina si tienes cinco subproyectos y cada uno contiene su propio directorio `node_modules`. Si ejecutas `ctags -R .`, ctags tratará de escanear todos los cinco `node_modules`. Probablemente quizás no necesites ejecutar ctags en `node_modules`.

Para ejecutar ctags excluyendo `node_modules`, ejecuta:

```text
 ctags -R --exclude=node_modules .
```

Esta vez debería durar menos de un segundo. Por cierto, puedes utilizar la opción `exclude` múltiples veces:

```text
ctags -R --exclude=.git --exclude=vendor --exclude=node_modules --exclude=db --exclude=log .
```

La cuestión es que si quieres omitir un directorio, `--exclude` es la opción que debes utilizar.

## Navegando por las etiquetas

Puedes avanzar mucho tiempo utilizando únicamente `Ctrl-]`, pero vamos a aprender unos cuantos trucos más. La tecla para saltar a una etiqueta `Ctrl-]` tiene un modo alternativo para la línea de comandos: `:tag mi-etiqueta`. Si ejecutas lo siguiente:

```text
:tag donut
```

Vim saltará al método `donut`, de igual manera que si hubieras pulsado `Ctrl-]` en una cadena donde aparezca "donut". También puedes autocompletar el argumento con la tecla `<Tab>`:

```text
:tag d<Tab>
```

Vim muestra un listado con todas las etiquetas que comienzan con "d". En este caso, "donut".

En un proyecto real, te puedes encontrar con múltiples métodos con el mismo nombre. Vamos a actualizar los dos archivos de los ejemplos anteriores. Dentro del archivo `one.rb` tenemos lo siguiente:

```text
# one.rb
class One
  def initialize
    puts "Initialized"
  end

  def donut 
    puts "one donut"
  end

  def pancake
    puts "one pancake"
  end
end
```

Y esto en el archivo `two.rb`:

```text
# two.rb
require './one.rb'

def pancake
  "Two pancakes"
end

one = One.new
one.donut
puts pancake
```

Si has vuelto a escribir el contenido del archivo, no olvides ejecutar de nuevo `ctags -R .` ya que ahora aparecen muchos procedimientos nuevos. Tienes dos instancias del procedimiento `pancake`. Si estás dentro del archivo `two.rb` y pulsas `Ctrl-]`, ¿qué es lo que ocurriría?

Vim saltará a `def pancake` dentro de `two.rb`, no a `def pancake` dentro de `one.rb`. Esto es debido a que Vim ve el procedimiento `pancake` dento del archivo `two.rb` como de mayor prioridad que el del procedimiento dentro del archivo `pancake`.

## Prioridad de las etiquetas

No todas etiquetas son iguales. Algunas etiquetas tienen unas prioridades mayores. Si Vim se encuentra con nombres de elementos duplicados, Vim comprueba la palabra clave. El orden es el siguiente:

1. Una etiqueta estática que coincida completamente en el archivo actual.
2. Una etiqueta global que coincida completamente en el archivo actual.
3. Una etiqueta global que coincida completamente en un archivo diferente.
4. Una etiqueta estática que coincida completamente en otro archivo.
5. Una etiqueta estática sin importar mayúsculas y minúsculas en el archivo actual.
6. Una etiqueta global sin importar mayúsculas y minúsculas en el archivo actual.
7. Una etiqueta global sin importar mayúsculas y minúsculas en un archivo diferente.
8. Una etiqueta estática sin importar mayúsculas y minúsculas en el archivo actual.

De acuerdo a esta lista de prioridades, Vim prioriza una coincidencia exacta encontrada en el mismo archivo. Es por eso que Vim escoge el procedimiento `pancake` dentro de `two.rb` sobre el procedimiento `pancake` dentro del archivo `one.rb`. Hay algunas excepciones a esta lista de prioridades que acabamos de ver, dependiendo de tus ajustes de `'tagcase'`, `'ignorecase'` y `'smartcase'`, pero no las veremos en este caso. Si estás interesado en aprender más, echa un vistazo a este capítulo de la ayuda de Vim `:h tag-priority`.

## Saltos selectivos a etiquetas

Estaría muy bien si se pudiera escoger a qué elementos de etiquetas saltar en vez de siempre hacerlo a la etiqueta de más prioridad. Quizás en un momento dado necesites saltar a `pancake` que está dentro del archivo `one.rb` y no a la que está dentro del archivo `two.rb`. Para hacer eso, puedes utilizar `:tselect`. Ejecuta:

```text
:tselect pancake
```

Ejecutándolo con los archivos del ejemplo, verás algo como esto en la parte inferior de la pantalla:

```text
# pri kind tag               file
1 F C f    pancake           two.rb
             def pancake
2 F   f    pancake           one.rb
             class:One
             def pancake
```

Si escribes `2` y después pulsas `<Enter>`, Vim saltará al procedimiento del archivo `one.rb`. Si escribes `1` y pulsas `<Enter>`, Vim saltará al archivo `two.rb`.

Presta atención a la columna de `pri`. En esta vemos `F C` en la primera ocurrencia y `F` en la segunda. Esto es lo utiliza Vim para determinar la prioridad de la etiqueta. `F C` significa que ha encontrado una coincidencia completa \(en inglés _fully-matched_ `F`\) de una etiqueta global en el archivo actual \(en inglés _current_ `C`\). Si solo aparece `F` esto significa que solo ha encontrado una etiqueta de una coincidencia completa. `F C` siempre tiene una prioridad mayor que `F`.

Si ejecutas `:tselect donut`, Vim también te preguntará que escojas una etiqueta de las disponibles, aunque solo hay una opción para escoger. ¿Hay alguna manera apropiada para hacer que Vim solo pregunte qué etiqueta escoger cuando haya múltiples entre las que escoger y que salte inmediatamente a una etiqueta cuando solo haya una opción?

¡Desde luego que sí! Vim tiene el método `:tjump`. Ejecuta:

```text
:tjump donut
```

Vim saltará inmediatamente al procedimiento `donut` en el archivo `one.rb`, de manera similar a si ejecutáramos `:tag donut`. Ahora ejecuta:

```text
:tjump pancake
```

Vim te mostrará las opciones de las etiquetas entre las que escoger, de manera similar a si hubiéramos ejecutado `:tselect pancake`. Con `tjump` tienes lo mejor de ambos métodos.

Vim tiene una tecla en el modo normal para `tjump`: `g Ctrl-]`. Personalmente me gusta más utilizar `g Ctrl-]` que `Ctrl-]`.

## Autocompletado con etiquetas

Las etiquetas pueden ayudar en los autocompletados. Recuerda el capítulo 6, sobre el modo insertar, donde aprendimos que puedes utilizar `Ctrl-X` como un submodo para realizar varias acciones de autocompletado. Un submodo de autocompletado que no mencioné fue `Ctrl-]`. Si pulsas `Ctrl-X Ctrl-]` mientras estás en el modo insertar, Vim utilizará el archivo de etiquetas para el autocompletado.

Siguiendo el ejemplo anterior, si entras en el modo insertar y escribes `Ctrl-X Ctrl-]`, verás lo siguiente:

```text
One
donut
initialize
pancake
```

## Pila de etiquetas

Vim guarda en una pila una lista de todas las etiquetas a las que has saltado o desde las que has saltado. Puedes consultar esta pila con el comando `:tags`. Si primero habías hecho un salto a la etiqueta `pancake`, seguido por `donut` y ejecutas `:tags`, verás lo siguiente:

```text
  # TO tag         FROM line  in file/text
  1  1 pancake            10  ch16_tags/two.rb
  2  1 donut               9  ch16_tags/two.rb
>
```

Ten en cuenta el símbolo `>`. Muestra tu posición actual en la pila. Para retrasar un puesto el puntero de la posición hasta el elemento anterior, puedes ejecutar el comando `:pop`. Pruébalo y después vuelve a ejecutar `:tags` de nuevo:

```text
  # TO tag         FROM line  in file/text
  1  1 pancake            10  puts pancake
> 2  1 donut               9  one.donut
```

Comprueba cómo el símbolo `>` ahora está en la línea dos, apuntando al elemento `donut`. Ejecuta `pop` una vez más y comprueba de nuevo la posición con el comando `:tags`:

```text
  # TO tag         FROM line  in file/text
> 1  1 pancake            10  puts pancake
  2  1 donut               9  one.donut
```

En el modo normal, puedes ejecutar `Ctrl-T` para conseguir el mismo efecto que si ejecutaras `:pop`.

## Generación automática de etiquetas

Uno de los grandes inconvenientes de las etiquetas en Vim es que cada vez que haces un cambio significativo, necesitas volver a generar el archivo de etiquetas. Si recientemente renombraste el procedimiento llamado `pancake` y ahora se llama `waffle`, el archivo de etiquetas no sabe que el procedimiento `pancake` había sido cambiado de nombre. Todavía es almacenado como `pancake` en la lista de etiquetas. Tienes que ejecutar `ctags -R .` para crear y actualizar el archivo de etiquetas. Volver a crear un nuevo archivo de etiquetas puede ser una tarea incómoda.

Afortunadamente hay varios métodos que puedes utilizar para generar etiquetas automáticamente.

### Generar una etiqueta al guardar

Vim tiene un método para crear comandos automáticos \(`autocmd`\) para ejecutar cualquier comando ante la aparición de un evento. Puedes utilizar esto para generar etiquetas cada vez que guardes el archivo. Ejecuta:

```text
:autocmd BufWritePost *.rb silent !ctags -R .
```

La explicación:

* `autocmd` es el método de comandos automáticos de Vim. Acepta cualquier nombre de evento, patrón de archivo y un comando.
* `BufWritePost` es un evento para guardar un _buffer_. Cada vez que guardas un archivo, se lanza un evento `BufWritePost`.
* `.rb` es el patrón de archivo para archivos ruby \(`rb`\).
* `silent` es parte del comando que estás pasando al comando automático. Sin este, Vim mostrará `press ENTER or type command to continue` cada vez que se dispare el comando automático.
* `!ctags -R .` es el comando a ejecutar. Recuerda que `!cmd` desde dentro del propio Vim ejecuta cualquier comando de la terminal.

Ahora cada vez que guardes desde dentro de un archivo de ruby, Vim ejecutará `ctags -R .`.

Añade un nuevo procedimiento en el archivo `two.rb`:

```text
def waffle
  "Two waffles"
end
```

Guarda el archivo. Puedes comprobar el archivo de etiquetas, verás que `waffle` es parte de las etiquetas. ¡Conseguido!

### Utilizando complementos

Hay muchos complementos para generar ctags automáticamente:

* [vim-gutentags](https://github.com/ludovicchabant/vim-gutentags)
* [vim-tags](https://github.com/szw/vim-tags)
* [vim-easytags](https://github.com/xolox/vim-easytags)
* [vim-autotag](https://github.com/craigemery/vim-autotag)

Yo utilizo vim-gutentags. Si utilizas un gestor de complementos en Vim \([vim-plug](https://github.com/junegunn/vim-plug), [vundle](https://github.com/VundleVim/Vundle.vim), [dein.vim](https://github.com/Shougo/dein.vim), etc\), simplemente instala el complemento ¡y este funcionará sin más configuraciones!

### Ctags y Git Hooks

Tim Pope, autor de un montón de complementos geniales para Vim, escribió un artículo de cómo utilizar _git hooks_. Puedes leerlo en [este enlace](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html).

## Aprende el uso de las etiquetas de la manera más inteligente

Una etiqueta es útil cuando está configurada adecuadamente. Supón que te encuentras con un código nuevo y quieres saber qué hace `functionFood`, podrás leerlo fácilmente saltando a la parte del código donde está definido. Dentro de este, también puedes descubrir que también llama a `functionBreakfast`. Vas hasta su definición y descubre que a su vez llama a `functionPancake`. El gráfico de saltos y llamadas tendría el siguiente esquema:

```text
functionFood -> functionBreakfast -> functionPancake
```

Esto te muestra que el flujo del código está relacionado con tener tortitas para desayunar.

Para aprender más sobre las etiquetas, echa un vistazo a la ayuda de Vim sobre esto con `:h tags`. Ahora que ya sabes utilizar las etiquetas, vamos a explorar otra funcionalidad diferente: el plegado o _folding_ por su término en inglés.

