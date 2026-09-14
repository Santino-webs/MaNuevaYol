# Ma Nueva Yol

Curso de **14 días de inglés de supervivencia** para viajar a Nueva York, pensado para
una persona hispanohablante que arranca de cero y tiene miedo de hablar.

Es **un solo archivo**: `index.html`. Se abre con doble clic, no necesita internet,
no tiene dependencias ni framework. El progreso se guarda en el navegador.

```
index.html   ← la app entera: datos, CSS y JS
README.md    ← esto
```

## Cómo se usa

Abrí `index.html` con el navegador. Cada día tiene tres etapas:
**Aprender** (las frases nuevas, una por pantalla), **Practicar** (cinco tipos de
ejercicio mezclados) y **Usar** (un simulacro de conversación con un personaje).

El buscador y el botón rojo de emergencia están en la barra de arriba, disponibles
desde cualquier pantalla y sin pasar por ningún menú.

## Cómo editar el contenido

Todo el contenido vive en una sola constante, `CURSO`, al principio del `<script>`.
La lógica no sabe nada del contenido: podés agregar, sacar o reordenar frases y días
sin tocar una línea de código.

```js
const CURSO = [
  {
    dia: 1,                            // número de día, en orden
    titulo: "Cortesía y supervivencia",
    linea: "1",                        // letra o número del disco (señalética del subte)
    color: "--mta-rojo",               // variable CSS del color del disco
    intro: "…",                        // párrafo de apertura del día
    frases: [
      {
        en:   "Excuse me",
        pron: "eks-KIÚS mi",           // pronunciación figurada; sílaba tónica en MAYÚSCULAS
        es:   "Permiso / disculpame",
        uso:  "Cuándo se usa y qué te van a contestar."
      }
    ],
    datos: [ { t: "Título del dato", d: "Dato cultural que evita un problema real." } ],
    dialogo: {
      personaje: "Una señora en la vereda",
      escena: "Dónde estás y qué está pasando.",
      turnos: [
        {
          ellos: { en: "Hi, how are you?", pron: "JÁI jau ar IÚ", es: "Hola, ¿cómo estás?" },
          opciones: [
            { en: "Good, thanks. And you?", pron: "GUD zanks. and IÚ", es: "Bien, gracias. ¿Y vos?",
              ok: true,  reaccion: "Qué pasa si elegís esto." },
            { en: "You're welcome", pron: "iur UÉL-com", es: "De nada",
              ok: false, reaccion: "Por qué no funciona, contado como reacción del personaje." }
          ]
        }
      ]
    }
  }
];
```

### Reglas al editar

- **Agregar una frase:** sumá un objeto `{ en, pron, es, uso }` a `frases`. Los cuatro
  campos son obligatorios. Los ejercicios y los distractores se generan solos.
- **Sacar una frase:** borrá el objeto. No hay que tocar nada más.
- **Agregar un día:** copiá un bloque entero y poné el `dia` siguiente. La pantalla de
  inicio, el buscador y el repaso lo toman automáticamente.
- **Cada turno del diálogo necesita exactamente una opción con `ok: true`** y al menos
  tres opciones en total (con dos también funciona, pero no se usa para los ejercicios
  de completar el hueco). Toda opción lleva `reaccion`, incluidas las correctas.
- **Día de repaso:** dejá `frases: []` y agregá `repaso: [1,2,3,4,5,6]` con los días que
  se repasan. La app junta las frases de esos días, priorizando las vencidas, y arma
  el día con eso. El diálogo sí es propio del día de repaso.
- **Colores del disco:** `--mta-rojo`, `--mta-naranja`, `--mta-amarillo`, `--mta-verde`,
  `--mta-azul`, `--mta-violeta`, `--mta-gris`, `--mta-lima`. Si usás amarillo o gris,
  el texto del disco se pone negro solo.
- Las frases del modo emergencia están aparte, en la constante `SOS`, con la misma
  forma `{ en, pron, es, uso }`. Se editan igual y entran solas en el buscador.

### Sobre el identificador de cada frase

El progreso de una frase se guarda usando su texto en inglés normalizado
(`How much is it?` → `how-much-is-it`). Consecuencia práctica: podés **mover** una frase
de día o reordenarla sin perder el progreso, pero si **cambiás el texto en inglés**, esa
frase arranca de cero. El texto en español, la pronunciación y el uso se pueden editar
libremente.

## Cómo funciona el repaso

Sistema Leitner de 5 cajas. Cada frase acertada sube una caja, cada error la manda de
vuelta a la caja 1. La espera de cada caja está en una sola línea, fácil de cambiar:

```js
const ESPERA = [0, 1, 2, 4, 8]; // días de espera según la caja (1 a 5)
```

Al empezar un día, la app mete adelante hasta 6 frases vencidas de días anteriores,
antes del contenido nuevo. En la pantalla de inicio se ve cuántas hay pendientes.
"Dominada" es una frase en caja 4 o 5.

## Audio

Usa `speechSynthesis` con voz `en-US` y un control de velocidad (normal / lento).
Si el navegador no tiene ninguna voz en inglés, los botones de audio y los ejercicios
de escuchar desaparecen y el resto del curso funciona igual.

## Progreso guardado

Se guarda en `localStorage` bajo la clave `manuevayol.v1`: día actual, estado Leitner de
cada frase, racha, fecha de la última sesión, días terminados y la velocidad del audio.
Toda lectura y escritura va dentro de `try/catch`: si el almacenamiento falla (modo
privado, permisos), la app avisa una vez y sigue funcionando en memoria.
El botón **Reiniciar progreso** borra todo, con confirmación.

## Decisión sobre el orden de los 14 días

Se respetó la secuencia propuesta, con una sola diferencia de énfasis: el **día 2 no es
"pedir que repitan"** sino *"cuando no entendés nada"*, e incluye también
`I don't speak English`, `Do you speak Spanish?` y `Can you write it down?`. El motivo es
que el primer día real del viaje el problema no es pedir una repetición puntual, sino
sostener una interacción entera sin entender: con esas frases juntas, el día 2 alcanza
para no quedarse trabado en ninguna situación del resto del curso.
