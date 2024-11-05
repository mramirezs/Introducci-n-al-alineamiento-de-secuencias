# Introducción a las alineaciones

## ¿Qué es una alineación de secuencias?
Una alineación de secuencias (específicamente alineación por pares) significa organizar dos secuencias de forma que las regiones con similitudes se alineen. Por ejemplo, la alineación de `GATTACA` y `GATCA` podría verse así:

```
GATTACA
||| |
GATCA--
```

A simple vista, las alineaciones suelen tener sentido de forma intuitiva, pero cuidado, ya que nuestra intuición puede ser engañosa.

Nuestro cerebro está predispuesto a ver patrones y similitudes siempre que sea posible. Una vez que percibimos una similitud, nos resulta difícil reconocer otras alternativas que podrían ser igual de buenas (o incluso mejores) que la que estamos observando. Es difícil “dejar de ver” un patrón. Por ejemplo, podríamos haber dispuesto las secuencias así:

```
GATTACA
|||  ||
GAT--CA
```

o también así:

```
GATTACA
|| | ||
GA-T-CA
```

Aquí vemos dos alineaciones más de las mismas secuencias que también parecen concordar bastante bien. Una vez que observamos un patrón, puede llevar un esfuerzo considerable notar que podríamos haber ordenado las letras de otras formas igual de válidas.

## ¿Para qué se usan las alineaciones?
Las alineaciones tienen dos aplicaciones principales:

1. Encontrar regiones similares entre dos secuencias, explorando la relación entre ellas.
2. Encontrar qué secuencia (de entre muchas alternativas) es la más similar a una secuencia de consulta (entrada).

## ¿Qué determina una alineación?
Cada alineación está determinada por dos factores:

1. **Algoritmo de alineación**: global, local o semi-global.
2. **Puntuación de alineación (parámetros)**: valores numéricos que ajustan la disposición.

Recuerda siempre que:

- Diferentes algoritmos suelen producir alineaciones diferentes para las mismas secuencias.
- Diferentes puntuaciones también suelen generar alineaciones distintas para las mismas secuencias.

### ¿Cómo se generan las alineaciones?
Supón que tienes las siguientes alineaciones de `GATTACA` con `GATCA`:

```
GATTACA      GATTACA      GATTACA
|||.|        |||  ||      || | ||
GATCA--      GAT--CA      GA-T-CA
```

¿Cuál de estas es la alineación “mejor”, “correcta” o “significativa”?

La “mejor” alineación depende de cómo valoras la forma en que se alinean las bases. ¿Consideras que una discrepancia es menos perturbadora que los espacios vacíos? El valor que asignes a una coincidencia, discrepancia o espacio vacío se denomina puntuación.

## Concepto fundamental de la alineación:
No existe una alineación universalmente mejor. Solo existe la mejor alineación relativa a una elección de puntuación. Cambiar la puntuación generalmente cambia la alineación seleccionada como mejor. Los algoritmos de alineación encuentran la disposición que maximiza la puntuación total de la alineación.

Los algoritmos de alineación aceleran el proceso de seleccionar la alineación con la mejor puntuación. Puedes pensar en los alineadores como métodos para evaluar rápidamente todas las combinaciones posibles y luego seleccionar las alineaciones con la puntuación más alta.

## ¿Cómo funciona la puntuación de alineación?
Las puntuaciones son valores, tanto positivos como negativos, que un algoritmo asignará a varias bases al alinearlas de cierta manera. Un alineador intenta crear una disposición que maximice la puntuación.

Por ejemplo, podríamos elegir las siguientes puntuaciones:

- 1 punto por coincidencia.
- -1 punto por discrepancia.
- -2 puntos por abrir un espacio.
- -1 punto por extender un espacio ya abierto.

Luego le indicamos al alineador que encuentre las mejores alineaciones utilizando estas puntuaciones. Con este sistema, las alineaciones se puntuarían así (las puntuaciones se muestran en la parte inferior):

```
GATTACA      GATTACA      GATTACA
|||.|        |||  ||      || | ||
GATCA--      GAT--CA      GA-T-CA

   0            2             1
```

Con esta puntuación, la segunda alineación tiene la puntuación más alta (2) y se considera la "mejor" alineación.

Cuando las secuencias tienen diferentes longitudes, a menudo queremos encontrar la secuencia más larga que cubra la secuencia más corta. Para ajustar esto, aplicamos una corrección que no penaliza los espacios al final de ninguna de las secuencias. Si aplicamos esta corrección, las puntuaciones serían:

```
GATTACA      GATTACA      GATTACA
|||.|        |||  ||      || | ||
GATCA--      GAT--CA      GA-T-CA

   3            2             1
```

Ahora, la primera alineación tiene la puntuación máxima y se convierte en la "mejor alineación". Otros cambios en la puntuación podrían afectar qué alineación se considera mejor.

## ¿Cómo elijo las puntuaciones?
En la mayoría de los casos, se comienza con puntuaciones conocidas, calculadas a partir de la tasa de sustitución a lo largo de la historia evolutiva. Existen varios esquemas de puntuación alternativos creados a partir de estas observaciones. Para obtener matrices de puntuación recomendadas, puedes consultar, por ejemplo:

```bash
curl -O ftp://ftp.ncbi.nlm.nih.gov/blast/matrices/NUC.4.4
cat NUC.4.4
```

## Tipos de matrices de puntuación
Existen dos tipos de matrices de puntuación: para nucleótidos y para proteínas. Las matrices de proteínas vienen en muchas variantes, calculadas bajo diferentes suposiciones sobre lo que significa la similitud. Además, las matrices de puntuación pueden estar normalizadas o no, lo cual debe tenerse en cuenta al comparar puntuaciones.

## Otras propiedades de las matrices de puntuación
Las matrices de puntuación generalmente no incluyen penalizaciones para los espacios. La penalización por extensión de un espacio es típicamente mucho menor que la penalización por abrir uno, lo que tiene una justificación biológica.

## Cómo se muestran las alineaciones
No existe un formato universal para mostrar alineaciones, pero comúnmente usamos un formato visual con caracteres adicionales para interpretarlas:

- El carácter `-` indica un espacio.
- El carácter `|` muestra una coincidencia.
- El carácter `.` muestra una discrepancia.

Ejemplo:

```
ATGCAAATGACAAAT-CGA
||||   |||.||.| |||
ATGC---TGATAACTGCGA
```

## ¿Qué es una cadena CIGAR?
La cadena CIGAR (Compact Idiosyncratic Gapped Alignment Report) es un formato de alineación utilizado en los archivos de alineación de secuencias (SAM). Para la alineación anterior, el formato CIGAR se vería así:

```
4M3D3M1X2M1X1M2D
```

Esto se lee como:

- 4 coincidencias seguidas de
- 3 eliminaciones,
- 3 coincidencias,
- 1 discrepancia,
- 2 coincidencias,
- 1 discrepancia,
- 1 coincidencia,
- 2 eliminaciones.

En el formato CIGAR extendido, `X` representa discrepancias. En el formato SAM, las coincidencias y discrepancias se agrupan bajo `M`, por lo que `4M3D7M2D` representa la misma alineación pero con un formato más compacto.

---

### ¿Dónde aprender más sobre alineaciones?
Hay muchos recursos para profundizar en alineaciones y puntuación. Wikipedia y otros recursos en línea son un buen punto de partida:

- Wikipedia: [Alineación de Secuencias](https://es.wikipedia.org/wiki/Alineaci%C3%B3n_de_secuencias)
- Wikipedia: [Penalización de Espacios](https://es.wikipedia.org/wiki/Penalizaci%C3%B3n_de_espacios)

