# Capítulo 6: EL modo insertar

El modo insertar es el modo predeterminado de la mayoría de los editores de texto. En este modo, lo que escribes es lo que aparece en la pantalla.

Sin embargo, eso no significa que no haya cosas que aprender. El modo insertar de Vim contiene muchas funcionalidades útiles. En este capítulo, aprenderás cómo utilizar  estas funcionalidades del modo insertar en Vim para mejorar tu eficiencia a la hora de escribir.

## Formas de entrar al modo insertar

Hay varias formas de entrar en el modo insertar desde el modo normal. Aquí tienes algunas de ellas:

```
i    Inserta texto antes del cursor
I    Inserta texto antes del primer carácter que no sea un espacio en blanco de la linea
a    Añadir texto después del cursor
A    Añadir texto al final de la linea
o    Crea una nueva línea debajo del cursor y cambia al modo insertar
O    Crea una nueva línea encima del cursor y cambia al modo insertar
s    Elimina el carácter debajo del cursor e inserta texto
S    Elimina la línea actual e inserta texto
gi   Inserta texto en la misma posición donde el modo insertar fue detenido por última vez en al *buffer* actual
gI   Inserta texto al comienzo de una línea (columna 1)
```

Ten en cuenta el patrón de minúscula/mayúscula. Para cada comando en minúscula existe su contraparte en mayúscula. Si eres un usuario nuevo, no te preocupes si no recuerdas toda la lista anterior. Empieza con `i` y `o`. Estos comandos deberían ser suficientes para empezar. Gradualmente ve aprendiendo más con el paso del tiempo.

## Formas diferentes de salir del modo insertar

Hay unas cuantas maneras de regresar al modo normal mientras estás en el modo insertar:

```
<Esc>    Salir del modo insertar y volver al modo normal
Ctrl-[   Salir del modo insertar y volver al modo normal
Ctrl-c   Similar a Ctrl-[ y <Esc>, pero no controla las abreviaciones
```

Encuentro la tecla `<Esc>` está algo apartada, por lo que mapeo el teclado de mi equipo para que la tecla de `<Bloq mayús>` se comporte como `<Esc>`. Si buscas el teclado de la ADM-3A de Bill Joy (creador de Vi), verás que la tecla `<Esc>` no estaba en la esquina superior izquierda como en los teclados modernos, si no al lado de la tecla `q`. Esta es la razón por lo que creo que tiene sentido cambiar el `<Bloq mayús>` por `<Esc>`.

Otra convención común que he visto en personas que utilizan Vim es que mapean `<Esc>` a `jj` o `jk` en el modo insertar. Si quieres probar esta opción, añade una de estas líneas (o ambas) en tu archivo vimrc.

```
inoremap jj <esc>
inoremap jk <esc>
```

## Repetir el modo insertar

Puedes pasar un parámetro de conteo antes de entrar al modo insertar. Por ejemplo:

```
10i
```

Si después escribes "¡Hola mundo!" y sales del modo insertar, Vim repetirá el texto 10 veces. Esto funcionará con cualquier otro método del modo insertar (por ejemplo: `10I`, `11a`, `12o`).

## Borrar segmentos en el modo insertar

Cuando comentes un error al escribir, puede ser molesto pulsar la tecla `<Retroceso>` repetidamente. Podría tener más sentido si vas al modo normal y borras tu error. También puedes borrar varios caracteres al mismo tiempo mientras estás en el modo insertar:

```
Ctrl-h    Borrar un carácter
Ctrl-w    Borrar una palabra
Ctrl-u    Borrar una línea entera
```

## Insertar desde un registro

Los registros de Vim pueden almacenar textos para un uso futuro. Para insertar un texto en cualquier registro nominal mientras estás en el moco insertar, escribe `Ctrl-R` más el símbolo del registro. Hay muchos símbolos que puedes utilizar, pero para esta sección vamos a utilizar solo los registros nominales (a-z).

Para ver esto en acción, primero necesitas copiar una palabra al registro "a". Situa tu cursor encima de cualquier palabra y después teclea:

```
"ayiw
```
Veamos el comando en detalle:

- `"a` le dice a Vim que resultado de tu siguiente acción irá al registro "a"
- `yiw` copia la palabra completa sobre la que está el cursor. Revisa el capítulo sobre la gramática de Vim

El registro "a" ahora contiene la palabra que acabas de copiar. Mientras estás en el modo insertar, para pegar el texto almacenado en el registro "a", escribe:

```
Ctrl-r a
```

Hay múltiples de tipos de registros en Vim. Trataré sobre ellos con gran detalle en el siguiente capítulo.

## Desplazamiento de la pantalla (*scroll*)

¿Sabías que puedes mover el contenido de la pantalla (hacer *scroll*) mientras estás en el modo insertar? Mientras estás en el modo insertar, si entras en el submodo con `Ctrl-x`, puedes realizar operaciones adicionales. Desplazar la pantalla es una de ellas.

```
Ctrl-x Ctrl-y    Desplaza el contenido de la pantalla hacia arriba
Ctrl-x Ctrl-e    Desplaza el contenido de la pantalla hacia abajo
```

## Autocompletado

Vim tiene un mecanismo de autocompletado propio utilizando un submodo con `Ctrl-x` (como con el desplazamiento que hemos visto antes). Aunque esto no es tan bueno como [intellisense](https://code.visualstudio.com/docs/editor/intellisense) o cualquier otro Protocolo de lenguaje de servidor (o LSP por sus siglas en inglés), pero para algo que está disponible de manera predeterminada, es una característica muy capaz.

Aquí tienes algunos comandos de autocompletado útiles para empezar a utilizarlo:

```
Ctrl-x Ctrl-l	   Inserta una línea completa
Ctrl-x Ctrl-n	   Inserta un texto desde el archivo actual
Ctrl-x Ctrl-i	   Inserta un texto desde los archivos incluidos
Ctrl-x Ctrl-f	   Inserta un nombre de archivo 
```

Cuando lanzas el autocompletado, Vim mostrará una ventana emergente. Para navegar arriba y abajo por la ventana, utiliza `Ctrl-n` y `Ctrl-p`.

Vim también tiene dos autocompletados que no utiliza el submodo `Ctrl-x`: 

```
Ctrl-n             Encuentra la siguiente palabra que coincide
Ctrl-p             Encuentra la palabra anterior que coincide
```

En general, Vim busca en todo el texto disponible en todos los *buffers* la fuente para el autocompletado. Si tienes abierto un *buffer* con una línea que dice: "Los donuts de chocolate son los mejores":

- Cuando escribes "Choco" y después `Ctrl-x Ctrl-l`, esto encontrará y añadirá la línea complet.
- Cuando escribes "Choco" y después `Ctrl-p`, esto encontrará y añadirá la palabra "Chocolate".

El autocompletado es un tema muy amplio en Vim. Esto es solo la punta de iceberg. Para aprender más, echa un vistazo a la ayuda de Vim `:h ins-completion`.

## Executing a Normal Mode Command

Did you know Vim can execute a normal mode command while inside insert mode?

While in insert mode, if you press `Ctrl-o`, you'll be in `insert-normal` sub-mode. If you look at the mode indicator on bottom left, normally you will see `-- INSERT --`, but pressing `Ctrl-o`  changes it to `-- (insert) --`. In this mode, you can do *one* normal mode command. Some things you can do:

**Centering and jumping**
```
Ctrl-o zz       Center window
Ctrl-o H/M/L    Jump to top/middle/bottom window
Ctrl-o 'a       Jump to mark 'a'
```

**Repeating text**
```
Ctrl-o 100ihello    Insert "hello" 100 times
```

**Executing terminal commands**
```
Ctrl-o !! curl https://google.com    Run curl
Ctrl-o !! pwd                        Run pwd
```

**Deleting faster**
```
Ctrl-o dtz    Delete from current location till the letter "z"
Ctrl-o D      Delete from current location to the end of the line
```

## Learn Insert Mode the Smart Way

If you are like me and you come from another text editor, it can be tempting to stay in insert mode. However, staying in insert mode when you're not entering a text is an anti-pattern. Develop a habit to go to normal mode when your fingers aren't typing new texts.

When you need to insert a text, first ask yourself if that text already exists. If it does, try to yank or move that text instead of typing it. Should you have to enter insert mode, see if you can autocomplete that text as much as possible. Avoid typing the same word more than once if you can.
