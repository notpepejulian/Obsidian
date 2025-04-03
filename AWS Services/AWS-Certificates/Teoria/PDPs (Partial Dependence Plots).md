Los **Partial Dependence Plots (PDPs)** o **Gráficos de Dependencia Parcial** son una herramienta de interpretación de modelos de aprendizaje automático que muestran la relación entre una o varias características de entrada y la predicción del modelo.

### Concepto clave

Los PDPs ilustran cómo cambia la predicción promedio del modelo a medida que se varía el valor de una característica en particular, manteniendo las demás constantes.

### ¿Para qué sirven?

1. **Interpretabilidad:** Ayudan a entender cómo influyen las variables en el modelo.
2. **Detección de relaciones no lineales:** Permiten visualizar efectos como interacciones o comportamientos no lineales. 
3. **Validación del modelo:** Permiten evaluar si el modelo sigue un comportamiento lógico respecto a los datos.

###  ¿Cómo se calculan?

Para una variable XjX_jXj​:

1. Se elige un conjunto de valores para XjX_jXj​.
2. Para cada valor, se predice el resultado usando el modelo mientras se mantienen las demás variables con sus valores originales.
3. Se promedian todas las predicciones para cada valor de XjX_jXj​, obteniendo la dependencia parcial.

### Ejemplo práctico

Si un modelo predice el precio de una casa en función del tamaño, ubicación y antigüedad, un PDP podría mostrar cómo varía el precio promedio según el tamaño, manteniendo las otras variables constantes.

⚠ **Limitación:** No reflejan interacciones complejas entre variables si estas están correlacionadas.