# Capítulo 19: Compilar

Compilar es un tema importante en muchos lenguajes de programación. En este capítulo, aprenderás cómo compilar desde Vim. También podrás ver formas de sacar partido del comando `:make` de Vim.

## Compilar desde la línea de comandos

Puedes utilizar el comando _bang_ \(`!`\) para compilar. Si necesitas compilar tu archivo `.cpp` con `g++`, ejecuta:

```text
:!g++ hola.cpp -o hola
```

Sin embargo, tener que escribir manualmente el nombre del archivo fuente y del archivo de salida todas las veces provoca que podamos cometer un error y además es tedioso. Por eso un archivo _makefile_ nos puede ayudar con eso.

## El comando _make_

Vim tiene el comando `:make` para ejecutar un archivo _makefile_. Cuando lo ejecutas, Vim busca un archivo _makefile_ en el directorio actual para ejecutarlo.

Crea un archivo llamado `makefile` en el directorio actual y escribe esto dentro del archivo:

```text
all:
    echo "Hola all"
foo:
    echo "Hola foo"
list_pls:
    ls
```

Ejecuta esto desde Vim:

```text
:make
```

Vim lo ejecuta de la misma manera que si lo estuvieras ejecutando desde la terminal. El comando `:make` acepta parámetros igual que el comando de la terminal. Ejecuta:

```text
:make foo
" Outputs "Hola foo"

:make list_pls
" Outputs the ls command result
```

El comando `:make` utiliza la ventana _quickfix_ de Vim para almacenar cualquier error si has ejecutado mal un comando. Vamos a ejecutar un archivo objetivo inexistente:

```text
:make noexistente
```

Deberías ver un error al ejecutar ese comando. Para ver ese error, ejecuta `:copen` para ver la ventana _quickfix_:

```text
|| make: *** No rule to make target `noexistente'.  Stop.
```

## Compilando con make

Vamos a utilizar un archivo _makefile_ para compilar un programa `.cpp`. Primero vamos a crear un archivo llamado `hola.cpp`:

```text
#include <iostream>

int main() {
    std::cout << "¡Hola!\\n";
    return 0;
}
```

Actualiza tu _makefile_ para compilar y ejecutar el archivo `.cpp`:

```text
all:
    echo "compilar, ejecutar"
build:
    g++ hola.cpp -o hola
run:
    ./hola
```

Ahora ejecuta:

```text
:make build
```

El compilador `g++` compilará `./hola.cpp` y creará `./hola`. Después ejecuta:

```text
:make run
```

Deberías ver `"!Hola!"` mostrado en la terminal.

## Programa diferente para make

Cuando ejecutas `:make`, Vim realmente ejecuta cualquier comando que esté configurado bajo la opción `makeprg`. Si ejecutas `:set makeprg?`, verás:

```text
makeprg=make
```

El comando predeterminado de `:make` es el comando externo `make`. Para cambiar el comando `:make` para que ejecute `g++ {nombre_de_tu_archivo}` cada vez que lo ejecutas, ejecuta lo siguiente:

```text
:set makeprg=g++\ %
```

El símbolo `\` es para escapar el espacio después de `g++`. El símbolo `%` en Vim representa el archivo actual. El comando `g++\\ %` es equivalente a ejecutar `g++ hola.cpp`.

Ve al archivo `./hola.cpp` después ejecuta `:make`. Vim compila `hola.cpp` y crea el archivo `a.out` porque no has especificado un nombre para un archivo de salida. Vamos a cambiar eso para que le asigne un nombre al archivo de salida compilado con el nombre del archivo original menos la extensión. Añade o ejecuta esto a tu archivo vimrc:

```text
set makeprg=g++\ %\ -o\ %<
```

Veamos en detalle el comando:

* `g++\ %` es lo mismo que hemos visto anteriormente. Es equivalente a ejecutar `g++ <tu_archivo>`.
* `-o` es la opción de salida.
* `%<` en Vim representa el nombre del archivo actual sin la extensión \(`hola.cpp` se convierte en `hola`\).

Cuando ejecutas `:make` desde dentro de `./hola.cpp`, este es compilado a `./hola`. Para ejecutar rápidamente `./hola` desde dentro de `./hola.cpp`, ejecuta `:!./%<`. De nuevo, esto es lo mismo que ejecutar `:!./{nombre_del_archivo_actual_menos_la_extensión}`.

Para más información puedes leer la información de Vim en `:h :compiler` o `:h write-compiler-plugin`.

## Compilado automático al guardar el archivo

Puedes hacer tu vida más sencilla automatizando el proceso de compilación. Recuerda que en Vim puedes utilizar `autocmd` para lanzar una acción automática basada en ciertos eventos. Para compilar automáticamente los archivos `.cpp` cada vez que guardes los cambios añade esto en tu archivo vimrc:

```text
autocmd BufWritePost *.cpp make
```

Cada vez que guardes un archivo `.cpp` file, Vim ejecutará el comando `make`.

## Cambiando de compilador

Vim tiene el comando `:compiler` para cambiar rápidamente entre compiladores. La versión compilada de tu Vim probablemente venga ya incluida con configuraciones de compiladores ya preconfiguradas. Para comprobar qué compiladores tienes disponibles, ejecuta:

```text
:e $VIMRUNTIME/compiler/<Tab>
```

Deberías ver una lista de compiladores para diferentes lenguajes de programación.

Para utilizar el comando `:compiler`, supongamos que tienes un archivo en Ruby llamado `hola.rb` y dentro de ese archivo este código:

```text
puts "Hola Ruby"
```

Recuerda que si ejecutas `:make`, Vim ejecutará cualquier comando que este asignado a `makeprg` \(de manera predeterminada es `make`\). Si ejecutas:

```text
:compiler ruby
```

Vim ejecuta el script `$VIMRUNTIME/compiler/ruby.vim` y cambia el `makeprg` para utilizar el comando `ruby`. Ahora si ejecutas `:set makeprg?`, debería aparecer `makeprg=ruby` \(esto depende de qué haya dentro de tu archivo `$VIMRUNTIME/compiler/ruby.vim` o si tienes otros compiladores personalizados para Ruby. El tuyo podría ser diferente que el mostrado\). El comando `:compiler {tu_lenguaje}` te permite cambiar a diferentes compiladores rápidamente. Esto es útil si tu proyecto utiliza múltiples lenguajes.

No tienes que utilizar el `:compiler` y `makeprg` para compilar un programa. Puedes ejecutar un script de prueba, enviar una señal o cualquier cosa que quieras.

## Crear un compilador personalizado

Vamos a crear un simple compilador de Typescript. Instala Typescript \(`npm install -g typescript`\) en tu equipo. Deberías tener disponible el comando `tsc`. Si no has utilizado antes typescript, `tsc` compila un archivo de formato Typescript en un archivo Javascript. Supongamos que tienes un archivo llamado, `hola.ts`:

```text
const hola = "hola";
console.log(hola);
```

Si ejecutas `tsc hola.ts`, esto lo compilará en `hola.js`. Sin embargo, si tienes la siguiente expresión dentro de `hola.ts`:

```text
const hola = "hola";
hola = "hola de nuevo";
console.log(hola);
```

Esto dará como resultado un error, porque no puedes mutar una variable de tipo `const`. Al ejecutar `tsc hola.ts` mostrará el error:

```text
hola.ts:2:1 - error TS2588: Cannot assign to 'person' because it is a constant.

2 person = "hola de nuevo";
  ~~~~~~


Found 1 error.
```

Para crear un compilador simple de Typescript, en tu directorio `~/.vim/`, añade un directorio llamado `compiler` \(`~/.vim/compiler/`\), después crea dentro de ese directorio un archivo llamado `typescript.vim` \(`~/.vim/compiler/typescript.vim`\). Y escribe dentro:

```text
CompilerSet makeprg=tsc
CompilerSet errorformat=%f:\ %m
```

La primero línea establece el `makeprg` para ejecutar el comando `tsc`. La segunda línea establece el formato de error para mostrar el archivo \(`%f`\), seguido por un símbolo de dos puntos \(`:`\) y un espacio en blanco, por lo que necesitamos una barra invertida para escapar ese espacio \(`\`\), seguido por el mensaje de error \(`%m`\). Para aprender más sobre cómo darle formato al error, echa un vistazo en `:h errorformat`.

También deberías leer algo sobre otros compiladores que se hayan hecho para ver cómo lo hacen otras personas. Echa un vistazo en `:e $VIMRUNTIME/compiler/<cualquier_lenguaje>.vim`.

Debido a que algunos complementos pueden interferir con el archivo Typescript, vamos a abrir el archivo `hola.ts` sin ningún complemento activado, utilizando la opción `--noplugin`:

```text
vim --noplugin hola.ts
```

Comprueba el `makeprg`:

```text
:set makeprg?
```

Debería mostrar el programa `make` predeterminado. Para utilizar el nuevo compilador de Typescript, ejecuta:

```text
:compiler typescript
```

Ahora cuando ejecutes de nuevo `:set makeprg?`, debería mostrar `tsc`. Vamos a ponerlo a prueba. Ejecuta:

```text
:make %
```

Recuerda que `%` hace referencia al archivo actual. ¡Comprueba que tu compilador de Typescript funciona como debería! Para ver la lista de error\(es\), ejecuta `:copen`.

## Compilador asíncrono

A veces el proceso de compilación puede durar mucho tiempo. No quieres quedarte mirando fijamente a una pantalla congelada de Vim mientras esperas que termine tu proceso de compilación. ¿No estaría bien que pudieras compilar de manera asíncrona para así poder utilizar Vim durante el proceso de compilación?

Por suerte hay complementos para ejecutar el proceso de manera asíncrona. Los dos más importantes son:

* [vim-dispatch](https://github.com/tpope/vim-dispatch)
* [asyncrun.vim](https://github.com/skywind3000/asyncrun.vim)

Para finalizar este capítulo, trataré sobre vim-dispatch, pero te recomendaría encarecidamente que probaras todos los existentes.

_Vim y NeoVim realmente admiten tareas asíncronas, pero eso se escapa del tema a tratar en este capítulo. Si tienes curiosidad, echa un vistazo en `:h job-channel-overview.txt`._

## Complemento: Vim-dispatch

Vim-dispatch tiene varios comandos, pero los dos más importantes son los comandos `:Make` y `:Dispatch`.

### Make asíncrono

El comando `:Make` de Vim-dispatch es similar al `:make` de Vim, pero este se ejecuta de manera asíncrona. Si estás en un proyecto de Javascript y necesitas ejecutar `npm t`, podrías intentarlo configurando tu makeprg para que sea:

```text
:set makeprg=npm\\ t
```

Si ejecutas:

```text
:make
```

Vim ejecutará `npm t`, pero te quedarás mirando a una pantalla congelada mientras se está ejecutando el test de JavaScript. Con vim-dispatch, simplemente puedes ejecutar:

```text
:Make
```

Y Vim ejecutará `npm t` de manera asíncrona. De esta manera, mientras `npm t` se está ejecutando en un proceso de segundo plano, puedes continuar haciendo cualquier cosa que estuvieras haciendo con Vim. ¡Asombroso!

### Dispatch asíncrono

El comando `:Dispatch` es como el `:compiler` y el comando `:!`. Puede ejecutar cualquier comando externo de manera asíncrona en Vim.

Vamos a imaginar que estás dentro de un archivo _spec_ de Ruby y necesitas ejecutar un test. Ejecuta:

```text
:Dispatch bundle exec rspec %
```

Vim ejecutará de manera asíncrona el comando `rspec` en el archivo actual \(`%`\).

### Dispatch automatizado

Vim-dispatch tiene una variable de _buffer_ `b:dispatch` que puedes configurar para evaluar un comando específico de manera automática. Puedes aprovecharlo con `autocmd`. Si añades esto en tu archivo vimrc:

```text
autocmd BufEnter *_spec.rb let b:dispatch = 'bundle exec rspec %'
```

Ahora cada vez que abras un archivo \(`BufEnter`\) que acabe con `_spec.rb`, al ejecutar `:Dispatch` automáticamente se ejecutará `bundle exec rspec {tu_archivo_spec_de_ruby_actual}`.

## Aprender a compilar de la manera más inteligente

En este capítulo has aprendido que puedes utilizar los comandos `make` y `compiler` para ejecutar _cualquier_ proceso desde el propio Vim de manera asíncrona para complementar tu forma de trabajo al programar. La capacidad de Vim de poder extenderse con otros programas lo hacer realmente muy potente.

