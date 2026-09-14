# Ma Nueva Yol

Curso de **7 días de inglés de supervivencia** para viajar a Nueva York, pensado para
una persona hispanohablante que arranca de cero y tiene miedo de hablar.

Es **un solo archivo**: `index.html`. Se abre con doble clic, funciona sin internet,
no tiene frameworks ni dependencias. El progreso se guarda en el navegador.

```
index.html   ← la app entera: datos, CSS y JS
README.md    ← esto
```

## Los 7 días

| Día | Línea | Tema | Frases |
|----|----|----|----|
| 1 | N | Romper el hielo — cortesía y no entender nada | 17 |
| 2 | 4 | Plata — precios, pagar, impuestos y propina | 15 |
| 3 | A | Llegar — aeropuerto, migraciones y valijas | 14 |
| 4 | F | Moverse — subte, taxi y direcciones | 18 |
| 5 | 7 | Comer — café, restaurante y la cuenta | 17 |
| 6 | G | Comprar y dormir — negocios, probadores y hotel | 16 |
| 7 | 1 | Emergencias y prueba final — 911, salud y un día completo | 14 |

Son 111 frases, más 12 en el modo emergencia. Cada día lleva entre 20 y 25 minutos
y tiene tres etapas: **Aprender** (una frase por pantalla, se pasa deslizando o con
las flechas), **Practicar** (cinco tipos de ejercicio mezclados) y **Usar** (uno o dos
simulacros de conversación con reintento).

El buscador y el botón rojo de emergencia están en la barra de arriba y en el pie,
disponibles desde cualquier pantalla y sin pasar por ningún menú.

## Cómo editar el contenido

Todo vive en la constante `CURSO`, al principio del `<script>`. La lógica no sabe nada
del contenido: podés agregar, sacar o reordenar frases y días sin tocar código.

```js
const CURSO = [
  {
    dia: 1,                                 // número de día, en orden
    titulo: "Romper el hielo",
    linea: "N",                             // letra o número del disco de línea
    resumen: "Cortesía y no entender nada", // subtítulo en la línea de progreso
    intro: "…",                             // párrafo de apertura del día
    frases: [
      {
        en:   "Excuse me",
        pron: "eks-KIÚS mi",                // figurada; sílaba tónica en MAYÚSCULAS
        es:   "Permiso / disculpame",
        uso:  "Cuándo se usa y qué te van a contestar."
      }
    ],
    datos: [ { t: "Título del dato", d: "Dato cultural que evita un problema real." } ],
    dialogo: {                              // o `dialogos: [ {...}, {...} ]`
      personaje: "Un empleado de un negocio",
      escena: "Dónde estás y qué está pasando.",
      turnos: [
        {
          ellos: { en: "Hi! What can I get you?", pron: "JÁI! uát can ái GUET iu", es: "¡Hola! ¿Qué te doy?" },
          nota: "(opcional) aclaración que aparece como aviso en ese turno",
          opciones: [
            { en: "A coffee, please", pron: "a CÓ-fi pliis", es: "Un café, por favor",
              ok: true,  reaccion: "Qué pasa si elegís esto." },
            { en: "I don't understand", pron: "ái dont an-ders-TÁND", es: "No entiendo",
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
- **Agregar un día:** copiá un bloque entero y poné el `dia` siguiente. La línea de
  progreso, el buscador y el repaso lo toman automáticamente. Si agregás un octavo día,
  definí también su color (ver abajo): sin `--dia8` el día queda sin color propio.
- **Una frase no se puede repetir en dos días**, porque el progreso se guarda por frase.
- **Cada turno necesita exactamente una opción con `ok: true`** y al menos tres opciones
  (con menos de tres, ese turno no se usa para los ejercicios de completar el hueco).
  Toda opción lleva `reaccion`, incluidas las correctas.
- **Dos simulacros en un día:** usá `dialogos: [ {...}, {...} ]` en vez de `dialogo`.
  Se juegan uno después del otro. Los días 4, 5, 6 y 7 lo usan.

### Los colores de los días

Cada día toma su color de las variables `--diaN` en `:root`, definidas dos veces: una
para tema claro y otra adentro del `@media (prefers-color-scheme: dark)`, con hexadecimales
distintos para que funcionen sobre los dos fondos. Cada día tiene tres roles:

```css
--dia1:       #f5c400;  /* color bandera: disco, fondo del cierre de día */
--dia1-tinta: #0b0d10;  /* texto que va ARRIBA del color bandera */
--dia1-txt:   #7a5c00;  /* el color usado COMO texto o trazo fino, sobre el panel */
```

El riel de la línea y los anillos usan `--diaN-txt`, no el color bandera: por eso el
amarillo del día 1 se ve amarillo en el disco y la línea igual se distingue del fondo.
Las clases `.d1` … `.d7` reapuntan `--acento` al color del día; cualquier componente que
use `var(--acento)` se tiñe solo.

### Sobre el identificador de cada frase

El progreso se guarda usando el texto en inglés normalizado (`How much is it?` →
`how-much-is-it`). Podés **mover** una frase de día o reordenarla sin perder el progreso,
pero si **cambiás el texto en inglés**, esa frase arranca de cero. El español, la
pronunciación y el uso se editan libremente.

## Cómo funciona el repaso

Sistema Leitner de 5 cajas. Cada acierto sube una caja, cada error manda a la caja 1.
La espera de cada caja está en una línea:

```js
const ESPERA = [0, 1, 2, 4, 8]; // días de espera según la caja (1 a 5)
```

Al empezar un día, la app mete adelante hasta 6 frases vencidas de días anteriores.
"Dominada" es una frase en caja 4 o 5.

## Audio

Usa `speechSynthesis` con voz `en-US` y un control de velocidad (normal / lenta).
Si el navegador no tiene ninguna voz en inglés, los botones de audio y los ejercicios
de escuchar desaparecen y el resto funciona igual.

## Progreso guardado

Se guarda en `localStorage` bajo la clave `manuevayol.v2`: día actual, estado Leitner de
cada frase, racha, fecha de la última sesión, días terminados y la velocidad de la voz.
Toda lectura y escritura va dentro de `try/catch`; si el almacenamiento falla, la app
avisa una vez y sigue funcionando en memoria. Todo lo que se lee se sanea antes de usarse,
así que datos corruptos o absurdos no rompen nada.

**Migración desde la versión de 14 días.** Si encuentra la clave vieja `manuevayol.v1`,
la convierte sola: conserva el estado de cada frase, la racha y la velocidad, y traduce
los días terminados con este mapa (los días 7 y 14 eran de repaso y no tienen equivalente):

```
viejo 1,2 → nuevo 1     viejo 5,6,12 → nuevo 4     viejo 10,11 → nuevo 6
viejo 3   → nuevo 2     viejo 8,9    → nuevo 5     viejo 13    → nuevo 7
viejo 4   → nuevo 3
```

Un día nuevo cuenta como terminado sólo si estaban terminados todos los viejos que lo
forman. El día actual pasa a ser el primero que quede sin hacer.

## Tipografía

Dos familias de Google Fonts más una monoespaciada, todas con pila de reserva real:
**Archivo** (peso 800, eje de ancho 112) para títulos y frases en inglés, **Inter** para
el texto en español y **IBM Plex Mono** para la pronunciación figurada y las etiquetas.
Sin internet cae a la sans del sistema en peso 800, así que los títulos siguen viéndose
pesados en vez de quedar livianos.
