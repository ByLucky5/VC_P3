# VC_P3

Práctica realizada por el grupo 15 (Lucía Motas Guedes y Raúl Marrero Marichal)

# Tarea reconocimiento de monedas

Para esta primera tarea, se realiza el reconocimiento de monedas de euros y céntimos de varias imágenes. Para ello, se hace uso de los métodos mostrados en la propia práctica, basando el reconocimiento en el tamaño de las monedas.

## Caso de ejemplo y calibración
Para el primer caso de ejemplo con imagen ideal _[Monedas.jpg](./Monedas.jpg)_ proporcionada para la práctica, se comienza convirtiendo la imagen original a escala de grises, con su posterior umbralizado invertido (dado el fondo blanco de la imagen) con un umbral establecido de manera manual, siendo 200 en este caso.

Dado que las imágenes de las monedas pueden estar rotadas en posteriores imágenes o presentar leves deformaciones, se opta por hacer una búsqueda de los contornos externos con la función `cv2.cvtColor`, en lugar de la alternativa de la Transformada de Hough propuesta por la práctica.

Tras la detección de contornos, se recorre el array de resultados, filtrando posibles artefactos detectados. Usando la función `cv2.minEnclosingCircle`, se obtienen el mínimo círculo que contiene al contorno, extrayendo los radios de las monedas que se ordenan de mayor a menor y se les asigna su valor correspondiente. Se debe tener en cuenta que las monedas no siguen el mismo orden para tamaño y valor, siendo el orden por tamaño: 2€, 0.50€, 1€, 0.20€, 0.05€, 0.10€, 0.02€ y 0.01€.

Finalmente, se procesa ésta primera imagen, recorriendo nuevamente el array de los contornos y comparando el valor del radio con el almacenado anteriormente. Dado que se trata de la misma imagen, la coincidencia debe ser perfecta.

Finalmente, se muestra por pantalla el valor total calculado, siendo el de todas las monedas 3.88€.

Para una visualización clara de los cálculos del algoritmo, se muestra por pantalla, además, la imagen original con los bordes y valor de la moneda calculados, además de la imagen umbralizada a su lado.

![plot](./MonedasRes.jpg)

Una vez realizado este ejemplo, se pueden usar las medidas y valores obtenidos de la imagen "ideal" para obtener los resultados de las demás imágenes.

## Procesado de imágenes

Para las otras imágenes, se sigue un método similar al aplicado durante el ejemplo. Para facilitar el uso de otras imágenes, se ha creado la función `processImage` para ello, la cual acepta varios parámetros siendo estos:
> **imgName:** Nombre del archivo de la imagen. `<String>`   
**useInv:** Si se debe usar el umbralizado invertido. _Default: True_ `<Bool> Opcional`    
**useAuto:** Si se debe usar el método de Otsu para la selección automática del umbral. _Default: True_ `<Bool> Opcional`   
**umbral:** Valor del umbral, si no se usa el método de Otsu. _Default: 200_ `<int> Opcional`  

La principal diferencia con el caso de ejemplo, es un paso intermedio de calibración automática. Debido a ésto, un requisito del método implementado, es que la imagen contenga una moneda de 2€, puesto que es la referencia usada para la calibración.

El proceso de calibración es sencillo: se obtienen los radios del mínimo círculo que contiene cada contorno detectado, del mismo modo que el caso de ejemplo. Dado que las imágenes no tendrán el mismo tamaño, se debe calcular la proporción. Para ello, se toma el mayor radio obtenido y se asume que es la moneda de 2€, por lo que se compara con la medida ya almacenada y con una simple división se obtiene el valor deseado.

Dicho valor es necesario durante el último paso, en el que se comparan los radios de los objetos detectados en la imagen a procesar con los datos del caso de ejemplo. Usando esta propoción, se hace una simple comparativa y, con una tolerancia del 2% se asigna el valor a cada moneda y se muestran por pantalla los resultados.

### Casos de prueba

Para casos controlados y cercanos a condiciones ideales, la función presenta unos resultados perfectos, aunque en ocasiones sea necesario establecer el umbral de manera manual.

1. **monedas2.webp**    
En este primer caso, la detección de umbral automática por el método Otsu falla, por lo que se establece el valor 210 de forma manual. Con este simple cambio, se obtiene un resultado perfecto.    

![plot](./monedas2Res.jpg)

2. **monedas3.jpg**     
En este caso no es necesario hacer ningún ajuste manual y la detección automática Otsu funciona correctamente, obteniendo un resultado perfecto incluso cuando las monedas presentan una ligera sombra que el borde reconoce como parte de la moneda.

![plot](./monedas3Res.jpg)

3. **monedas6.jpg**     
Para este caso, se mantiene la detección automática de umbral, aunque se debe usar el umbral no invertido dado que el fondo de la imagen es negro. Esta imagen permite comprobar que la función es capaz de detectar monedas repetidas, y soportar la detección errónea de bordes internos. A pesar de que algunos números no se puedan distinguir en la imagen resultante, se puede ver que el valor total calculado es correcto, siendo de 7,76€.

![plot](./monedas6Res.jpg)

4. **monedas4.jpg** y **monedasJuntas.jpg**     
Lamentablemente, dado que este método se basa en la detección de bordes exteriores, falla en el momento en el que hay monedas juntas o superpuestas, al no ser capaz de diferenciarlas. Dado que el método asume que la mayor figura es la moneda de 2€, un error en la detección de dicha moneda, provoca un fallo completo en todo el proceso.

![plot](./monedas4Res.jpg)
![plot](./monedasjuntasRes.jpg)

5. **monedas5.webp**    
Por limitaciones similares al caso anterior, el método tiende a fallar en una imagen "natural" en la que el fondo no se pueda diferenciar claramente de las monedas. Además, la inclinación y la diferencia de distancia entre las monedas, provoca que las proporciones no se puedan calcular de manera correcta.

![plot](./monedas5Res.jpg)