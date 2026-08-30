# Meshtastic en HackRF + PortaPack

[Русский](README.md) · [English](README.en.md) · [Deutsch](README.de.md) · **Español**

Esto es una bifurcación del firmware
[Mayhem](https://github.com/portapack-mayhem/mayhem-firmware) para el HackRF con PortaPack. Se
diferencia del original en una cosa: aquí se ha añadido una aplicación **Meshtastic**, y hay un
firmware ya compilado en Releases para poder probarlo sin compilar nada.

El trabajo se ha propuesto al proyecto principal como
[PR #3306](https://github.com/portapack-mayhem/mayhem-firmware/pull/3306). Mientras no se
acepte, esta bifurcación va por su cuenta.

## Lea esto antes de pulsar transmitir

**Mayhem puede transmitir, y eso le impone obligaciones.** Un HackRF con PortaPack no es un
receptor con un adaptador, es un transmisor completo de 1 MHz a 6 GHz. En la mayoría de países
transmitir fuera de las bandas de radioaficionado y de las libres está prohibido, y dentro de
ellas está limitado en potencia y en tiempo de ocupación. La responsabilidad de qué emite y en
qué frecuencia es suya, no del firmware. El propio proyecto Mayhem lo dice con claridad, y aquí
no cambia nada.

**La potencia de transmisión segura no está medida.** Hay un campo `TX pwr` que llega hasta
`Max`, pero nadie ha comprobado a qué potencia real corresponde, si respeta los límites de su
país, ni cómo aguanta el amplificador una antena desadaptada. Considérelo no verificado.
Prudencia razonable:

- use la potencia más baja con la que haya enlace, no la más alta disponible
- no transmita sin antena, ni con una cortada para otra banda: la potencia reflejada vuelve al
  amplificador
- no mantenga una transmisión continua; la banda de 868 MHz tiene límites de tiempo de ocupación
  en Europa
- si no está seguro de que la banda esté libre donde vive, quédese en recepción: la aplicación
  tiene el modo `RX only` justamente para eso

La aplicación no elude ni debilita ningún límite: habla el mismo protocolo en las mismas
frecuencias que los nodos Meshtastic de serie.

**Guía de usuario con capturas:**
[Русский](docs/mesh/guide.ru.md) ·
[English](docs/mesh/guide.en.md) ·
[Deutsch](docs/mesh/guide.de.md) ·
[Español](docs/mesh/guide.es.md)

## Qué tiene de interesante

El HackRF no lleva dentro nada que entienda LoRa. Entrega un flujo de muestras y recibe otro
igual. Por eso toda la capa física está escrita de nuevo y corre en el segundo núcleo: busca el
preámbulo, sincroniza en tiempo y frecuencia, deshace el chirp contra una referencia, aplica la
transformada de Fourier, el código Gray, el entrelazado, Hamming y el blanqueo. En ambos
sentidos, dentro de 54 kB de memoria intermedia y con un plazo de 1,024 ms por bloque.

Dicho en corto: es un transceptor LoRa por software que mantiene enlace con nodos Meshtastic de
serie. Escribe mensajes, cifra canales, lleva una lista de nodos, recibe telemetría y
posiciones, dibuja un mapa y reenvía paquetes ajenos.

Alcance hasta hoy: **2,6 km con visión directa** en LongFast, con bastante margen de señal, y
con una antena del juego del HackRF que no está cortada para esta banda. El límite no se ha
encontrado.

## Puesta en marcha

1. Descargue `portapack-mayhem_OCI.ppfw.tar.gz` de
   [Releases](https://github.com/mamapapa1/mayhem-firmware/releases)
2. Descomprímalo **en la raíz de la tarjeta**. Contiene tres carpetas y hacen falta las tres:

   | carpeta | qué contiene |
   |---|---|
   | `FIRMWARE` | el firmware |
   | `APPS` | aplicaciones externas |
   | `BASEBAND` | imágenes de proceso de señal |

3. Tarjeta en el aparato, **Utilities → Flash Utility**, elegir `portapack-mayhem_dev.bin`
4. Tras grabar, **apague el aparato del todo**, no basta reiniciarlo
5. **Transceiver → Mesh**, luego **Setup → Radio** y elegir la región

Sin región no hay enlace alguno, y todo lo demás parecerá correcto. Es lo primero que conviene
mirar cuando no llega nada.

La carpeta `BASEBAND` no es adorno. La aplicación no cabe entera en la memoria del firmware, así
que once imágenes se han pasado a la tarjeta. Si descomprime solo `FIRMWARE`, once aplicaciones
ajenas dirán `NoImg` y no arrancarán.

## Qué funciona

Siete de los nueve modos de modulación, en ambos sentidos. Comprobado en el aire contra un
Heltec V4 de serie: mensajes, canales cifrados, telemetría, posiciones, la hora tomada de un
vecino, trazado de ruta y las peticiones que la aplicación de móvil de Meshtastic hace a un
nodo.

Los detalles, con la tabla de modos, están en la [guía](docs/mesh/guide.es.md).

## Qué no funciona

Una lista honesta, para que no busque el fallo en sus manos:

- **Recepción en SF12** (`LONG_SLOW`). Transmitir sí, recibir no: el algoritmo no cabe en el
  tiempo disponible en este aparato
- **Weather y SubGhzD no arrancan** y dicen `NoImg`. Sus imágenes son mayores que la memoria que
  queda cuando ha llegado hasta ellas por el menú
- **Las contraseñas de los canales propios** solo sirven entre PortaPack. Para hablar con un
  nodo de serie escriba su clave real, 32 caracteres, en lugar de la contraseña
- **El nivel de señal no está calibrado.** Sirve para comparar nodos entre sí, pero no son dBm
  reales

## Advertencia

Esto **no es una versión oficial de Mayhem** ni de Meshtastic. Está compilado de una rama que
todavía no se ha aceptado y que cambia según avanza la revisión.

No todo funciona, y algo puede romperse. A veces el aparato se detiene con una pantalla de
error; se arregla apagándolo y encendiéndolo. Los ajustes y los mensajes de la tarjeta se
conservan.

La memoria del aparato es escasa y no vuelve hasta que se reinicia. En la práctica: si abre
varias aplicaciones seguidas, la siguiente puede decir `NoImg`. Apague y encienda.

Transmitir está sujeto a las normas de su país. Las bandas de 868 y 915 MHz no están libres en
todas partes ni a cualquier potencia.

La grabación es reversible: el firmware oficial siempre se puede volver a poner del mismo modo.

## Cuénteme qué le falló

Es lo más útil que puede hacer. Yo tengo un PortaPack, un Heltec y una sola ciudad. Su entorno
casi seguro sacará a la luz algo que yo no puedo ver.

Abra una [incidencia](https://github.com/mamapapa1/mayhem-firmware/issues). Lo más útil:

- qué hizo y qué ocurrió en su lugar
- el modelo de PortaPack y el modo de modulación
- si el aparato se detuvo, **fotografíe la pantalla entera**: el motivo está escrito ahí
- si el problema es del enlace: qué muestra el otro extremo y cuál es
- la versión de la compilación, visible en la línea inferior del menú principal

Interesan en especial: el alcance donde usted vive, los nodos que yo no tengo (T-Beam, RAK,
T-Deck) y todo lo relativo al cirílico y a otros alfabetos en el chat.

## Compilar desde el código

```
docker run --rm -v "$(pwd):/havoc" portapack-dev:latest make -j4
```

Comprobaciones que no necesitan aparato:

```
cd tools/lora_bench && make
```

## Créditos y advertencias

Toda la base pertenece al proyecto
[Mayhem](https://github.com/portapack-mayhem/mayhem-firmware) y a quienes lo hacen. Aquí se ha
añadido una aplicación.

Usa el protocolo Meshtastic. Sin relación con Meshtastic LLC ni aprobado por ellos.

La licencia se hereda de Mayhem: GPL-2.0-or-later.
