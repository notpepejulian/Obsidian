# Pregunta 1

```
Un bufete de abogados quiere construir una aplicación de IA utilizando modelos de lenguaje grande (LLM). La solicitud leerá documentos legales y extraerá puntos clave de los documentos. ¿Qué solución cumple estos requisitos?
```

- [ ] A. Construir un sistema automático de reconocimiento de entidades con nombre. 
- [ ] B. Crea un motor de recomendación. 
- [x] C. Desarrolle un chatbot de resumen.
- [ ] D. Desarrollar un sistema de traducción multilingüe. 

### 📝 Contexto del Caso:

Un bufete de abogados quiere construir una aplicación de IA utilizando **Modelos de Lenguaje Grande (LLM)**. La aplicación debe ser capaz de **leer documentos legales** y **extraer puntos clave** de estos documentos. Aquí estamos buscando una solución que permita procesar y entender texto, especialmente textos legales.

Ahora, veamos las opciones.

---

### A. Construir un sistema automático de reconocimiento de entidades con nombre (NER)

**Definición:**  
El **Reconocimiento de Entidades con Nombre** (NER, por sus siglas en inglés) es una tarea de procesamiento de lenguaje natural (NLP) que se enfoca en identificar y clasificar entidades en un texto. Estas entidades pueden ser **nombres de personas, lugares, organizaciones, fechas, cantidades**, entre otras.

**Aplicación para el caso:**  
Si el objetivo es extraer puntos clave de documentos legales, como nombres de personas, leyes, fechas de eventos importantes, etc., entonces un sistema de NER podría ser útil. Sin embargo, este tipo de sistema solo identifica y clasifica entidades, no resume el contenido o extrae un resumen completo de la información clave.

**Limitación:**  
No sería suficiente si el bufete de abogados quiere obtener un resumen general o una extracción más amplia de la información clave del documento, ya que NER solo identifica entidades específicas dentro del texto, no realiza un análisis completo o extracción de ideas principales.

**Conclusión:**  
Aunque útil en ciertos casos, esta solución no cumple con los requisitos de **extraer puntos clave** de documentos legales en su totalidad.

---

### B. Crea un motor de recomendación

**Definición:**  
Un **motor de recomendación** es un sistema que sugiere productos, servicios o contenido a los usuarios en función de sus comportamientos previos, preferencias y datos de usuario. Este tipo de sistema es común en **e-commerce**, servicios de **streaming** como **Netflix** o **Spotify**, o sitios web que ofrecen contenido personalizado.

**Aplicación para el caso:**  
Aunque un motor de recomendación es útil para sugerir artículos, productos o servicios a los usuarios, no es adecuado para leer documentos legales y extraer puntos clave. Un motor de recomendación no está diseñado para analizar y procesar el contenido de textos, sino para recomendar elementos basados en patrones de comportamiento de los usuarios.

**Limitación:**  
No cumple con el objetivo de **procesar documentos legales y extraer información clave**.

**Conclusión:**  
Esta solución no es relevante para la tarea de leer documentos legales y extraer puntos clave.

---

### C. Desarrollar un chatbot de resumen

**Definición:**  
Un **chatbot de resumen** utilizaría tecnologías de procesamiento de lenguaje natural (NLP) para resumir un texto largo en una versión más corta y comprensible. Los chatbots pueden generar resúmenes automáticos de documentos o responder preguntas sobre el contenido de estos.

**Aplicación para el caso:**  
Esta opción parece la más adecuada para el caso del bufete de abogados. Un chatbot de resumen puede **leer** un documento legal (o un conjunto de documentos) y **extraer puntos clave o un resumen general**. Esto es útil porque permite a los abogados obtener una versión condensada de un documento largo sin tener que leerlo completo.

Además, si el chatbot está basado en **Modelos de Lenguaje Grande (LLM)**, como los desarrollados por OpenAI (GPT-3, GPT-4) o AWS, puede generar resúmenes de alta calidad que capturan los aspectos más importantes de los documentos legales.

**Conclusión:**  
Este tipo de solución **cumple perfectamente con los requisitos** del bufete de abogados: procesar documentos legales y extraer puntos clave de manera efectiva.

---

### D. Desarrollar un sistema de traducción multilingüe

**Definición:**  
Un **sistema de traducción multilingüe** es una solución que traduce texto o contenido de un idioma a otro. Las aplicaciones más conocidas de traducción multilingüe incluyen **Google Translate** o **DeepL**.

**Aplicación para el caso:**  
Aunque la traducción puede ser útil si los documentos están en diferentes idiomas, **no es el objetivo principal** del bufete de abogados. Ellos no están buscando traducir documentos, sino **extraer información clave** de ellos. Por lo tanto, un sistema de traducción multilingüe no aborda directamente la necesidad de resumir y extraer puntos clave.

**Limitación:**  
Este tipo de sistema no tiene la capacidad de **analizar o extraer información relevante** de los documentos. Su único propósito es la traducción de texto.

**Conclusión:**  
Aunque útil para la traducción, esta solución no cumple con los requisitos de **extraer puntos clave** de los documentos legales.

---

## Resumen:

- **A. Reconocimiento de entidades con nombre (NER)**: útil para identificar entidades específicas (personas, fechas, etc.) pero no extrae resúmenes o puntos clave completos.
    
- **B. Motor de recomendación**: no es adecuado para procesar documentos legales, ya que está diseñado para recomendar productos o servicios, no para extraer información clave.
    
- **C. Chatbot de resumen**: la mejor opción, ya que un chatbot de resumen puede leer documentos y extraer puntos clave de manera eficiente, cumpliendo con los requisitos.
    
- **D. Sistema de traducción multilingüe**: útil solo si el propósito es traducir, no extraer información clave de los documentos.


---

# Pregunta 2

```
Una empresa quiere clasificar los genes humanos en 20 categorías basadas en las características genéticas. La compañía necesita un algoritmo ML para documentar cómo el mecanismo interno del modelo afecta la salida. ¿Qué algoritmo ML cumple estos requisitos?
```

- [x] A. Árboles de decisión
- [ ] B. Regresión lineal
- [ ] C. Regresión logística
- [ ] D. Redes neuronales

### 📝 Contexto del Caso:

La empresa necesita clasificar genes humanos en 20 categorías basadas en características genéticas. Además, requiere un modelo de **Machine Learning (ML)** que sea **interpretable**, es decir, que se pueda entender cómo el mecanismo interno del modelo afecta la salida.

Ahora, vamos a analizar cada opción.

---

### A. Árboles de decisión

**Definición:**  
Un **árbol de decisión** es un algoritmo de clasificación o regresión que divide los datos en subconjuntos basándose en características específicas, creando una estructura similar a un árbol. Cada nodo representa una decisión sobre una característica, y las hojas representan las categorías o valores de salida.

**Aplicación para el caso:**  
Los árboles de decisión son **fáciles de interpretar** porque muestran explícitamente cómo las decisiones de clasificación se toman en función de las características de entrada. Para clasificar los genes humanos en 20 categorías, un árbol de decisión podría dividir los datos de manera clara y comprensible, lo que permitiría documentar fácilmente el mecanismo interno del modelo.

**Ventaja:**  
La interpretabilidad es una de las mayores ventajas de los árboles de decisión, ya que puedes ver cómo se toman las decisiones en cada nodo. Esto cumple con el requisito de **documentar cómo el mecanismo interno del modelo afecta la salida**.

**Conclusión:**  
Esta opción cumple perfectamente con los requisitos de **interpretabilidad y clasificación**.

---

### B. Regresión lineal

**Definición:**  
La **regresión lineal** es un modelo estadístico que trata de ajustar una línea recta (o plano en dimensiones superiores) para predecir una variable continua en función de una o más variables independientes.

**Aplicación para el caso:**  
Aunque la regresión lineal puede ser útil para **predicciones continuas**, no es adecuada para clasificación de categorías, especialmente si las categorías son discretas (como las 20 categorías genéticas mencionadas en el caso). Además, la regresión lineal no es particularmente **adecuada para documentar cómo se toman las decisiones** en un modelo de clasificación, ya que es más útil para entender relaciones lineales entre variables.

**Limitación:**  
No es adecuada para la clasificación de múltiples categorías y no proporciona la misma claridad en cuanto a las decisiones internas que los árboles de decisión.

**Conclusión:**  
Este modelo no cumple con los requisitos de **clasificación** de 20 categorías y tampoco ofrece una interpretación tan clara como un árbol de decisión.

---

### C. Regresión logística

**Definición:**  
La **regresión logística** es un modelo utilizado para clasificación binaria (es decir, solo dos categorías), aunque puede ser extendido a clasificación multiclase mediante técnicas como la "regresión logística multinomial". Este modelo predice la probabilidad de que una entrada pertenezca a una de las clases posibles.

**Aplicación para el caso:**  
Aunque la regresión logística es adecuada para clasificación multiclase (y podría extenderse para clasificar genes en varias categorías), la **interpretabilidad** de cómo el modelo llega a una conclusión no es tan directa como en los árboles de decisión. Los coeficientes de la regresión logística indican la influencia de cada característica, pero no proporcionan una visualización tan clara de cómo se toman las decisiones en cada paso del proceso.

**Limitación:**  
Aunque la regresión logística es interpretativa hasta cierto punto, **no es tan explicativa** en cuanto a cómo el modelo afecta la salida en comparación con los árboles de decisión.

**Conclusión:**  
La regresión logística podría funcionar para la clasificación, pero no proporciona una interpretación tan clara de las decisiones del modelo como los árboles de decisión.

---

### D. Redes neuronales

**Definición:**  
Las **redes neuronales** son modelos complejos y poderosos de Machine Learning inspirados en el cerebro humano. Son especialmente eficaces para tareas de **clasificación y predicción**, pero a menudo son difíciles de interpretar debido a su estructura interna de múltiples capas y nodos.

**Aplicación para el caso:**  
Aunque las redes neuronales podrían clasificarse eficazmente en 20 categorías y proporcionar resultados muy precisos, son conocidas por ser **modelos de caja negra**. Esto significa que, a pesar de su eficacia, no es fácil entender cómo el modelo llega a una conclusión, lo que hace que **documentar el mecanismo interno sea difícil**.

**Limitación:**  
Las redes neuronales no son fácilmente interpretables. Esto va en contra del requisito de **documentar cómo el mecanismo interno afecta la salida**.

**Conclusión:**  
Las redes neuronales no cumplen con el requisito de **interpretabilidad** del caso.

---

## Resumen:

- **A. Árboles de decisión**: es la opción más adecuada ya que proporciona una **interpretabilidad clara** del proceso de clasificación, permitiendo documentar cómo el modelo llega a su decisión.
    
- **B. Regresión lineal**: no es adecuada para clasificación de múltiples categorías y no es tan interpretativa como los árboles de decisión.
    
- **C. Regresión logística**: adecuada para clasificación multiclase, pero no proporciona una interpretación tan clara como los árboles de decisión.
    
- **D. Redes neuronales**: muy eficaces para clasificación, pero no son fácilmente interpretables (caja negra), lo que dificulta la documentación de cómo el modelo llega a la salida.