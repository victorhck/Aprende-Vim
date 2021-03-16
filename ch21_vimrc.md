# Ch21. Vimrc

En los capitulos anteriores has aprendido sobre como usar el editor de textos VIM. Esto es genial, pero no es todo. Para usar Vim de una manera efectiva, vas a necesitar aprender como configurarlo. El mejor lugar para empezar es tu vimrc. TBC 
En los capitulos anteriores aprendiste a usar Vim. En este capítulo, vas a aprender como organizar y configurar vimrc.

## Como Vim encuentra el archivo vimrc
El uso convencional de vimrc es agregar el archivo `.vimrc` en la carpeta de usuario `~/.vimrc` (esto puede ser diferente dependiendo del Sistema Operativo).


Atras de escena, Vim mira muchos lugares para encontrar el archivo vimrc. Estos son los lugares que revisa:
- `$VIMINIT`
- `$HOME/.vimrc`
- `$HOME/.vim/vimrc`
- `$EXINIT`
- `$HOME/.exrc`
- `$VIMRUNTIME/default.vim`

Cuando inicias Vim, se van a verificar estos 6 lugares en ese orden, buscando el archivo vimrc. El primer lugar con este archivo va a ser usado y los posteriores son ignorados.

Primero Vim va a buscar `$VIMINIT`. Si no hay nada ahí, Vim va a buscar `$HOME/.vimrc`. Si no hay nada ahí, Vim va a buscar `$HOME/.vim/vimrc`. Si Vim lo encuentra va a dejar de buscar y usar `$HOME/.vim/vimrc`. 

La primer ubicacion, `$VIMINIT`, es una variable de entorno. No esta definido de manera predeterminada. Se puede usar `~/dotfiles/testvimrc` como variable `$VIMINIT` , puede crearse una variable de entorno con la ubicación de este archivo. Después de ejecutar `export VIMINIT='let $MYVIMRC="$HOME/dotfiles/testvimrc" | source $MYVIMRC'`, Vim va a usar el archivo `~/dotfiles/testvimrc` como su archivo vimrc.

La segunda ubicacion, `$HOME/.vimrc`, es el path usado por muchos usuarios de Vim de manera convencional. `$HOME` es en muchos casos el directorio del usuario (`~`). Si existe `~/.vimrc`, Vim va a usar esta como su archivo vimrc.

El tercer lugar es para  `$HOME/.vim/vimrc`, esta alojado dentro del directorio `~/.vim`. You might have the `~/.vim` directory already for your plugins, custom scripts, or View files. Note that there is no dot in vimrc file name (`$HOME/.vim/.vimrc` won't work, but `$HOME/.vim/vimrc` will).

El cuarto, `$EXINIT` funciona de manera similar a `$VIMINIT`. 

El quinto, `$HOME/.exrc` trabaja como `$HOME/.vimrc`.

El sexto, `$VIMRUNTIME/defaults.vim` es el predeterminado al compilar Vim. En mi caso, tengo instalado Vim 8.2 usando Homebrew (en MAC), entonces el path es (`/usr/local/share/vim/vim82`). Si Vim, en ninguna de las 5 ubicaciones encuentra el archivo vimrc, entonces usará este archivo.

Para lo que resta de este capitulo, estamos asumiendo que Vim utiliza el archivo en el path `~/.vimrc`. 

## ¿ Que poner en mi Vimrc ?

La pregunta que me hice cuando me inicie fue: "¿Que debo poner en mi archivo vimrc?"

La respuesta es, "cualquier cosa que quieras". La tentacion de copiar y pegar el vimrc de otras personas es real, pero debes evitar esto. Si insistes en usar el vimrc de alguien más, revisá que sabes lo que esto hace, porqué y cómo él/ella usa esto, y lo más importante, si es relevante para tí. Solo porque alguien mas lo use no quiere decirque tu tambien lo vas a usar.


## Contenido Básico de Vimrc

En resumen, un archivo vimrc es una colección de:
- Plugins
- Configuraciones
- Funciones Personalizadas
- Comandos Personalizados
- Mapeos

Hay otras cosas que no fueron mencionadas, pero en general, esto cubre la mayoría de los casos.

### Complementos (Plugins)

En los capítulos anteriores, mencionamos diferentes complementos, como ser [fzf.vim](https://github.com/junegunn/fzf.vim), [vim-mundo](https://github.com/simnalamburt/vim-mundo) y [vim-fugitive](https://github.com/tpope/vim-fugitive).

Hace 10 años, manejar los complementos era una pesadilla. Sin embargo, con la llegada de modernos gestores de complementos, instalarlos hoy se puede hacer en segundos. Actualmente utilizo [vim-plug](https://github.com/junegunn/vim-plug) como mi gestor de complementos, por esto lo usaré en esta sección. El concepto es similar al de otros gestores de complementos populares. La recomendación es que revises los disponibles, como ser:
- [vundle.vim](https://github.com/VundleVim/Vundle.vim)
- [vim-pathogen](https://github.com/tpope/vim-pathogen)
- [dein.vim](https://github.com/Shougo/dein.vim)

Hay mas gestores que los listados arriba, sientase libre de buscarlos. Para instalar vim-plug, si utiliza Unix, ejecute:


```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Para agregar nuevos complementos, escriba el nombre del mismo (`Plug 'github-username/repository-name'`) entre las lineas `call plug#begin()` y `call plug#end()`. Por ejemplo, si quiere instalar `emmet-vim` y `nerdtree`, agregue las siguientes lineas en su archivo ~/.vimrc:

```
call plug#begin('~/.vim/plugged')
  Plug 'mattn/emmet-vim'
  Plug 'preservim/nerdtree'
call plug#end()
```

Guarde los cambios, relea el archivo de configuracion con el comando (`:source %`) y ejecuter `:PlugInstall` para instalar ambos.

En el futuro, si necesita remover los complementos que no utiliza, solo necesita remover el complemento del bloque escrito anteriormente, guardar, releer el archivo de configuracion ( con `:source %} ) y ejecutar el comando `PlugClean` para eliminarlo de su PC.

Vim 8 tiene su propio gestor de paquetes. Puedes consultar la ayuda con `:h packages` para mas información. En el próximo capítulo, vamos a mostrar como usarlo.

### Configuraciones

Es comun ver muchas opciones que empiezan con `set` en cualquier vimrc. Si ejecutas la linea en el modo linea de comandos, esta configuración no será permanente. Se perderán las mismas al cerrar Vim. Por ejemplo, en vez de ejecutar `:set relativenumber number` desde el modo linea de comandos cada vez que inicias Vim, se puede agregar dentro del archivo .vimrc:

```
set relativenumber number
```

Algunas veces es requerido pasar algún valor, como ser `set tabstop=2`. Revise la página de ayuda para cada configuración para aprender que tipo de valores acepta.

Tambien se puede usar `let` en cambio de `set` (verifique anteponer el mismo con `&`). Con `let`, se puede usar espresiones como un valor. Por ejemplo, para configurar la opción `'dictionary'` a un directorio solo si ese directorio existe: 

```
let s:english_dict = "/usr/share/dict/words"

if filereadable(s:english_dict)
  let &dictionary=s:english_dict
endif
```

Va a aprender sobre hacer scripts con Vim para asignar valores y condicionales en los próximos capítulos.

Para una lista de todas las posibles opciones de Vim, consulte `:h E355`.

### Funciones Personalizadas

Vimrc es un buen lugar para las funciones personalizadas. Va a aprender como escribir sus propias funciones de vim en los capítulos posteriores.

### Comandos Personalizados

Se pueden crear un comando personalizado para la linea de comandos con `command`.

Para crear el comando `GimmeDate` para mostrar el día actual:

```
:command! GimmeDate echo call("strftime", ["%F"])
```

Cuando ejecuta `:GimmeDate`, Vim va a mostrar la fecha como "2021-01-1".

Para crear un comando que recibe argumentos, se puede usar `<args>`. Si se le quiere pasar un formato específico a `GimmeDate`:

```
:command! GimmeDate echo call("strftime", [<args>])

:GimmeDate "%F"
" 2020-01-01

:GimmeDate "%H:%M"
" 11:30
```

Si se quiere restringir el número de argumentos, se puede pasar el flag `-nargs`. Use `-nargs=0` para que no se puedan pasar argumentos, `-nargs=1` para pasar un argumento, `-nargs=+` para pasar por lo menos un argumento, `-nargs=*` para pasar cualquier cantidad de argumentos, y `-nargs=?` para pasar 0 ó un argumento. Si quiere pasar n argumentos, use `-nargs=n` (donde `n` es un entero).

`<args>` tiene dos variantes: `<f-args>` y `<q-args>`. El primero es para pasar argumentos a una función VimScript. El ultimo es usado para convertir la entrada del usuario en strings.

Usando `argumentos`:

```
:command! -nargs=1 Hola echo "Hola " . <args>
:Hola "Iggy"
" devuelve 'Hola Iggy'

:Hola Iggy
" Da un error de que la variable no ha sido definida.
```

Usando `q-args`:

```
:command! -nargs=1 Hola echo "Hola " . <q-args>
:Hola Iggy
" Devuelve 'Hola Iggy'
```

Usando `f-args`:

```
:function! PrintHola(persona1, persona2)
:  echo "Hola " . a:persona1 . " y " . a:persona2
:endfunction

:command! -nargs=* Hola call PrintHola(<f-args>)

:Hello Iggy1 Iggy2
" Devuelve "Hola Iggy1 and Iggy2"
```

La función de arriba va a tener mucho mas sentido una vez que llegues al capítulo de las funciones Vimscripts.

Para aprender más acerca de los comandos y argumentos, consulte `:h command` y `:args`.

### Mapeos

Si te vez a ti mismo haciendo de manera repetitiva la misma tarea compleja, este es un buen indicador para querer crear un mapeo para esta tarea.

Por ejemplo, tenemos estos dos mapeos en el archivo vimrc:

```
nnoremap <silent> <C-f> :GFiles<CR>

nnoremap <Leader>tn :call ToggleNumber()<CR>
```

En el primero, se mapea la combinacion de teclas `Ctrl-F` para el plugin [fzf.vim](https://github.com/junegunn/fzf.vim)  al comando `:Gfiles` (que busca rapidamente por archivos en un repositorio git). En el segundo, se mapea la combinación `<Leader>tn` para llamar a la función personalizada `ToggleNumber` (que cambia entre si las opciones `norelativenumber` y `relativenumber`). La combinación `Ctrl-F` mapea y sobreescribe la función nativa de Vim para moverse en la pantall. El mapeo va a sobreescribir los controles de Vim si entran en conflicto. En mi caso, como nunca uso esta característica, he decidido que es conveniente sobreescribirla.

Por otro lado, a mi personalmente me gusta usar la tecla espaciadora que se escribe como `<Space>` como mi tecla para llamar a estos mapeos, conocida como "leader key" en inglés, para usarla, agregar esto en el archivo vimrc:

```
let mapleader = "\<space>"
```

El comando `nnoremap` command usado anteriormente puede descomponerse en tres partes:
- `map` es el nombre del comando para mapear.
- `n` representa el modo normal.
- `nore` quiere decir no-recursivo.

Como mínimo, se podría haber usado `nmap` en vez de `nnoremap` (`nmap <silent> <C-f> :Gfiles<CR>`). Igualmente, es una buena práctica el usar la variante que no es recursiva para prevenir potenciales loops infinitos al ejecutar el comando.

Esto es lo que podria haber pasado si no se usa el mapeo no recursivo. Supongamos que se quiere agregar el mapeo  `B` para agregar el punto y coma (;) al final de la linea, luego, volver una palabra hacia atrás (rellamamos a `B`que en el modo normal vuelve para atrás una palabra tal como necesitamos).

```
nmap B A;<esc>B
```

Cuando se presiona `B` ... ouch! Vim agrega de manera descontrolada `;` (se puede interrumpir presionando `Ctrl-C`). ¿Porque pasó esto? Porque en el mapeo `A;<esc>B`, la letra `B` no hace referencia a la función nativa de Vim que es volver una palabra hacia atrás, porque se esta refiriendo a la función mapeada que actualmente es:

```
A;<esc>A;<esc>A;<esc>A;esc>...
```

Para resolver este problema, se tiene que agregar la opción para que no sea recursivo:

```
nnoremap B A;<esc>B
```

Ahora, intente llamar a `B`. Esta vez si va a agregar al final de la linea `;` y volver una palabra antes. El `B` en este mapeo representa al mapeo original de Vim al llamar a la función `B`. 

Vim tiene diferentes mapeos para diferentes modos. Si quiere crear un mapeo para el modo de insersión para salir del mismo cuando presiona `jk`:


```
inoremap jk <esc>
```

Los otros modos son: `map` (Normal, Visual, Select, and Operator-pending), `vmap` (Visual y Select), `smap` (Select), `xmap` (Visual), `omap` (Operator-pending), `map!` (Insert y Command-line), `lmap` (Insert, Command-line, Lang-arg), `cmap` (Command-line), and `tmap` (terminal-job). No vamos a cubrirlos en detalle. Para aprender más de ellos ejecute `:h map.txt`.

Cree un mapeo que sea lo más intuitivo, consistente y fácil de recordar.

## Organizando Vimrc

A travéz del tiempo, tu vimrc va a crecer y se va a volver confuso. Hay varias maneras de mantener tu vimrc y que se vea limpio:
- Separar el archivo vimrc en varios archivos.
- Plegando tu archivo vimrc.

### Separando tu vimrc

Puedes separar tu vimrc en multiples archivos usando el comando de Vim `source`. Este comando lee los comandos que hay en el archivo utilizado como argumento.

Vamos a crear dentro del la carpeta `~/.vim`y vamos a llamarla `/settings` (`~/.vim/settings`). El nombre en sí es arbitrario y lo puedes llamar como quieras.

Lo vamos a separar en 4 componentes: 
- Componentes de terceros (plugins) (`~/.vim/settings/plugins.vim`).
- Configuraciones Generales (`~/.vim/settings/configs.vim`).
- Funciones Personalizadas (`~/.vim/settings/functions.vim`).
- Mapeos de teclado (`~/.vim/settings/mappings.vim`) .

Dentro del archivo `~/.vimrc`:

```
source $HOME/.vim/settings/plugins.vim
source $HOME/.vim/settings/configs.vim
source $HOME/.vim/settings/functions.vim
source $HOME/.vim/settings/mappings.vim
```

Dentro del archivo `~/.vim/settings/plugins.vim`:

```
call plug#begin('~/.vim/plugged')
  Plug 'mattn/emmet-vim'
  Plug 'preservim/nerdtree'
call plug#end()
```

Dentro del archivo `~/.vim/settings/configs.vim`:

```
set nocompatible
set relativenumber
set number
```

Dentro del archivo `~/.vim/settings/functions.vim`:

```
function! ToggleNumber()
  if(&relativenumber == 1)
    set norelativenumber
  else
    set relativenumber
  endif
endfunc
```

Dentro del archivo `~/.vim/settings/mappings.vim`:

```
inoremap jk <esc>
nnoremap <silent> <C-f> :GFiles<CR>
nnoremap <Leader>tn :call ToggleNumber()<CR>
```

Tu archivo vimrc va a lucir como siempre, ¡pero ahora solo tiene 4 lineas de largo!

Con esta configuración, facilmente ahora vez a donde hay que ir. Si necesita agregar mapeos, agregarlos en el archivo `/mappings.vim`. En el futuro, siempre se peude agregar mas directorios mientras el archivo vimrc crece. Por ejemplo si se necesita crear una configuracion para el color del tema, se puede agregar al archivo `~/.vim/settings/themes.vim`.

### Manteniendo un solo archivo Vimrc

Si la preferencia es tener un solo archivo vimrc para que sea portable, se puede usar el plegado para mantener todo organizado. Agregue esto al inicio de su archivo vimrc: 

```
" setup folds {{{
augroup filetype_vim
  autocmd!
  autocmd FileType vim setlocal foldmethod=marker
augroup END
" }}}
```

Vim puede detectar que tipo de archivo hay en el buffer actual (con `:set filetype?` puede comprobarlo). Si este es un archivo del tipo `vim`, se puede usar el método de plegado. El metodo de plegado utiliza `{{{` y `}}}` para indicar el inicio y el final de un grupo que se esta plegando.

Agregue `{{{` y `}}}` para plegar al resto de su vimrc (no se olvide de comentar estas lineas con `"`):

```
" Configuracion de Plegado {{{
augroup filetype_vim
  autocmd!
  autocmd FileType vim setlocal foldmethod=marker
augroup END
" }}}

" Complementos {{{
call plug#begin('~/.vim/plugged')
  Plug 'mattn/emmet-vim'
  Plug 'preservim/nerdtree'
call plug#end()
" }}}

" Configuraciones {{{
set nocompatible
set relativenumber
set number
" }}}

" Funciones {{{
function! ToggleNumber()
  if(&relativenumber == 1)
    set norelativenumber
  else
    set relativenumber
  endif
endfunc
" }}}

" Mapeos {{{
inoremap jk <esc>
nnoremap <silent> <C-f> :GFiles<CR>
nnoremap <Leader>tn :call ToggleNumber()<CR>
" }}}
```

Your vimrc should look like this:

```
+-- 6 lines: Configuracion de Plegado -----

+-- 6 lines: Complementos ---------

+-- 5 lines: Configuraciones ---------

+-- 9 ilnes: Funciones -------

+-- 5 lines: Mapeos --------
```

## Correr Vim con o sin Vimrc y Complementos

Si necesita correr Vim sin vimrc y los complementos, ejecute:

```
vim -u NONE
```

Si necesita iniciar Vim sin vimrc pero si con complementos, ejecute:

```
vim -u NORC
```

Si quiere correr Vim con vimrc pero sin complementos: 

```
vim --noplugin
```

Si lo que necesita es correr vim con un vimrc *diferente*, digamos `~/.vimrc-backup`, ejecute:

```
vim -u ~/.vimrc-backup
```

## Configure Vimrc - La Manera Inteligente 


Vimrc es un componente importante de la configuración de Vim. Una buena manera de empezar a crear su vimrc es leyendo los vimrcs de otras personas y de manera gradual ir creando el suyo a travéz del tiempo. El mejor vimrc no es el que usa el desarrollador X, es el que esta configurado de la manera que mejor encaja a su pensamiento y a su forma personal de editar. 
