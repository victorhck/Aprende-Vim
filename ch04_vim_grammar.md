# La gramática de Vim

Es sencillo sentirse intimidado por la complejidad de muchos de los comandos de Vim. Si ves a una persona que usa Vim habitualmente ejecutando `gUfV` o `1GdG`, puede que no sepas inmediatamente qué es lo que realizan esos comandos. En este capítulo, voy a romper la estructura general de los comandos de Vim en reglas simples de gramática.

Este es el capítulo más importante de todo el libre. Una vez que entiendas la estructura similar a la gramática de los comandos de Vim, serás capaz de "hablarle" a Vim. Por cierto, cuando me refiero a *lenguaje de Vim* en este capítulo, no estoy refiriéndome a Vimscript (el lenguaje de programación propio para adaptar y crear complementos de Vim). Aquí se refiere al patrón general de los comandos del modo normal de Vim.

# Cómo aprender un lenguaje

El inglés no es mi lengua nativa. Aprendí inglés cuando tenía 13 años. Tuve que hacer tres cosas para aumentar mis habilidades linguísticas:

1. Aprender las reglas gramáticas
2. Incrementar mi vocabulario
3. Practicar, practicar y practicar

De igual manera, para hablar el lenguaje de Vim, necesitas aprender las reglas gramáticas, incrementar tu vocabulario y practicar hasta que puedas ejecutar los comandos sin necesidad de pensarlos.

# Reglas gramáticas

Solo necesitas saber una regla gramática para hablar el lenguaje de Vim:

```
verbo + sustantivo
```

¡Eso es todo! 

Esto es equivalente a decir en inglés estas frases: (en español aunque se entienden y se construyan de manera similar, quedan un poco forzadas y poco coloquiales, pero son aplicables las mismas reglas):

- *"Comer (verbo) un donut (sustantivo)"*
- *"Patear (verbo) un balón (sustantivo)"*
- *"Aprender (verbo) el editor Vim (sustantivo)"*

Ahora necesitas construir tu vocabulario con verbos de Vim básicos y sustantivos.

# Vocabulario
## Sustantivos (Movimientos)

Hablemos de los movimientos como sustantivos. Los movimientos son utilizados para movernos por el texto en Vim. También hay sustantivos de Vim. En la lista a continuación veremos algunos ejemplos de movimientos:

```
h    Izquierda
j    Abajo
k    Arriba
l    Derecha
w    Mover el cursor hacia adelante al principio de la palabra siguiente
}    Saltar al siguiente párrafo
$    Ir al final de la línea
```

Aprenderás más sobre los movimientos en el capítulo siguiente, así que no te preocupes demasiado si todavía no entiendes algunos de los mencionados.

## Verbos (Operadores)

De acuerdo a lo que podemos leer en la ayuda de Vim mediante el comando `:h operator`, Vim tiene 16 operadores. Sin embargo, en mi experiencia, con aprender estos 3 es suficiente para el 80% de las necesidades a la hora de editar mis textos:

```
y    Copiar un texto (*yank* en Vim sería la acción de copiar, de ahí la letra `y`)
d    Eliminar un texto y guardarlo en el registro (*delete* en Vim sería la acción de eliminar, de ahí la letra `d`)
c    Eliminar un texto, guardarlo en el registro y comenzar en el modo de insertar
```

Ahora que ya conocer los sustantivos y verbos básicos, ¡Vamos a aplicar nuestras reglas gramáticas! Supongamos que tenemos la siguiente expresión en un texto:

```
const learn = "vim"; 
```
Estas serían las acciones que queremos realizar construidas a partir de la gramática de Vim y sus correspondientes comandos:

- Para copiar (*yank*) todo desde la ubicación actual del cursor hasta el final de la línea: `y$`.
- Para eliminar desde la ubicación actual del cursor hasta el principio de la siguiente palabra: `dw`.
- Para cambiar el texto desde la posición actual del cursor hasta el final de párrfo actual, di: `c}`.

Los movimientos también aceptan números de conteo como argumentos *(Veremos esto más en profundidad en el siguiente capítulo)*. Si necesitas subir tres líneas, en vez de presionar la letra If you need `k` 3 veces, simplemente puedes ejecutar `3k`. Contar funciona a la hora de conjugar la gramática de Vim.

- Para copiar dos caracteres a la izquierda: `y2h`.
- Para eliminar las siguientes dos palabras: `d2w`.
- To change the next two lines: `c2j`.

Ahora mismo, quizás tengas que pensar bastante para construir comandos simples. Pero no creas que solo te pasa a ti. No estás solo. Cuando al principio comencé, tuve dificultades similares pero progresé rápidamente en el tiempo. Y también te pasará a ti.

Como nota complementaria, las operaciones que afectan a una línea completa son las tareas más comunes a la hora de editar texto, y por eso Vim te permite realizar operaciones que implican a la línea completa tecleando los operadores que ya conocemos dos veces seguidas. Por ejemplo, `dd`, `yy`, y `cc` lo que hace es **eliminar**, **copiar** y **cambiar** el contenido de la línea completa. ¡Intenta esto con otros operadores!

Espero que todo comience a tener sentido poco a poco. Pero todavía no he terminado. Vim tiene otro tipo más de sustantivos: objetos de texto.

## More Nouns (Text Objects)

Imagine you are somewhere inside a pair of parentheses like `(hello vim)` and you need to delete the entire phrase inside the parentheses. How can you quickly do it? Is there a way to delete the "group" you are inside of?

The answer is yes. Texts often come structured. They are often put inside parentheses, quotes, brackets, braces, and so on. Vim has a way to capture this structure with text objects. 

Text objects are used with operators. There are two types of text objects:

```
i + object    Inner text object
a + object    Outer text object
```
Inner text object selects the object inside *without* the white space or the surrounding objects. Outer text object selects the object inside *including* the white space or the surrounding objects. Outer text object always selects more text than inner text object. So if your cursor is somewhere inside the parentheses in the expression `(hello vim)`:

- To delete the text inside the parentheses without deleting the parentheses: `di(`.
- To delete the parentheses and the text inside: `da(`.

Let's look at a different example. Suppose you have this Javascript function and your cursor is on "Hello":

```
const hello = function() {
  console.log("Hello Vim"); 
  return true;
}
```

- To delete the entire "Hello Vim": `di(`.
- To delete the content of function (surrounded by `{}`): `di{`.
- To delete the "Hello" string: `diw`. 

Text objects are powerful because you can target different objects from one location. You can delete the objects inside the pair of parentheses, the function block, or the whole word. Moreover, when you see `di(`, `di{`, and `diw`, you get a pretty good idea what text objects they represent (a pair of parentheses, a pair of braces, and a word). 

Let's look at one last example. Suppose you have these HTML tags:
```
<div>
  <h1>Header1</h1>
  <p>Paragraph1</p>
  <p>Paragraph2</p>
</div>
```
If your cursor is on "Header1" text:
- To delete "Header1": `dit`.
- To delete `<h1>Header1</h1>`: `dat`.

If your cursor is on "div":
- To delete `h1` and both `p` lines: `dit`.
- To delete everything: `dat`.
- To delete "div": `di<`.

Below is a list of common text objects:

```
w         A word
p         A paragraph
s         A sentence
( or )    A pair of ( )
{ or }    A pair of { }
[ or ]    A pair of [ ]
< or >    A pair of < >
t         XML tags
"         A pair of " "
'         A Pair of ' '
`         A pair of ` `
```
To learn more, check out `:h text-objects`.

# Composability and Grammar

After learning Vim grammar, let's discuss composability in Vim and why this is a great feature to have in a text editor.

Composability means having a set of general commands that can be combined (composed) to perform more complex commands. Just like in programming where you can create more complex abstractions from simpler abstractions, in Vim you can execute complex commands from simpler commands. Vim grammar is the manifestation of Vim's composable nature.

The true power of Vim's composability shines when it integrates with external programs. Vim has a filter operator (`!`) to use external programs as filters for our texts. Suppose you have this messy text below and you want to tabularize it:
```
Id|Name|Cuteness
01|Puppy|Very
02|Kitten|Ok
03|Bunny|Ok
```
This cannot be easily done with Vim commands, but you can get it done quickly with `column` terminal command. With your cursor on "Id", run `!}column -t -s "|"`. Voila! Now you have this pretty tabular data:
```
Id  Name    Cuteness
01  Puppy   Very
02  Kitten  Ok
03  Bunny   Ok
```

Let's break down the command. The verb was `!` (filter operator) and the noun was `}` (go to next paragraph). The filter operator `!` accepted another argument, a terminal command, so I gave it `column -t -s "|"`. I won't go through how `column` worked, but in short, it tabularized the text.

Suppose you want to not only tabularize your text, but to display only the rows with "Ok". You know that `awk` can do the job easily. You can do this instead:
```
!}column -t -s "|" | awk 'NR > 1 && /Ok/ {print $0}'
```
Result:
```
02  Kitten  Ok
03  Bunny   Ok
```

Great! Even piping works from inside Vim. 

This is the power of Vim's composability. The more you know your operators, motions, and terminal commands, your ability to compose complex actions is *multiplied*.

Let me elaborate. Suppose you only know four motions: `w, $, }, G` and the delete (`d`) operator. You can do 8 things: move 4 different ways (`w, $, }, G`) and delete 4 different targets (`dw, d$, d}, dG`). Then one day you learn about the uppercase (`gU`) operator. You have added not just one new ability to your Vim tool belt, but *four*: `gUw, gU$, gU}, gUG`. Now you have 12 tools in your Vim tool belt. Each new knowledge is a multiplier to your current abilities. If you know 10 motions and 5 operators, now you have 60 moves (50 operations + 10 motions) in your arsenal. Moreover, the  line number motion (`nG`) gives you `n` motions, where `n` is how many lines you have in your file (example: to go to line 5, `5G`). The search motion (`/`) practically gives you near unlimited number motion because you can search for anything. External command operator (`!`) gives you as many filtering tools as the number of terminal commands you know. Using a composable tool like Vim, everything you know can be connected together to do more complex operations. The more you know, the more powerful you become.

This composable behavior echoes Unix philosophy: *do one thing well*. A motion has one job: go to X. An operator has one job: do Y. By combining an operator with a motion, you get YX: do Y on X.

Even better,  motions and operators are extendable. You can create custom motions and operators to add to your Vim toolbelt. [`vim-textobj-user`](https://github.com/kana/vim-textobj-user) has a [list](https://github.com/kana/vim-textobj-user/wiki) of custom text objects.

By the way, it's okay if you don't know `column` or `awk` commands I just did. The point is that Vim integrates very well with terminal commands.

# Learn Grammar the Smart Way

You just learned about Vim grammar's only rule:
```
verb + noun
```
One of my biggest Vim "AHA!" moments was when I had just learned about the uppercase (`gU`) operator and wanted to uppercase a word, I instinctively ran `gUiw` and it worked! The word I was on was uppercased. I finally began to understand Vim. My hope is that you will have your own "AHA!" moment soon, if not already.

The goal is this chapter is to show you the `verb + noun` pattern in Vim so you will approach learning Vim like learning a new language instead of memorizing every command combinations. 

Learn the pattern and understand the implications. That's the smart way to learn.
