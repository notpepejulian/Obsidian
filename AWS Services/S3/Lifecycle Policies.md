###  Explicación de las [[ISL-2.7b. Security Directives_EN-130125-152437 1.pdf |Lifecycle Policies]]

1. daily-housekeeping
    **Objetivo**: Mantener el bucket limpio y optimizado eliminando objetos no necesarios.
    
    - **Eliminar cargas incompletas de Multipart Uploads**: Si se inicia una carga de un objeto en varias partes (multipart upload) y no se completa, las partes incompletas se mantienen en el bucket, lo que genera costos adicionales. Esta regla elimina esas cargas incompletas después de 3 días.
        
    - **Eliminar delete markers expirados**: Cuando un objeto es eliminado en un bucket con versionado habilitado, S3 marca esa eliminación con un **delete marker**, pero este marker puede quedar en el bucket sin ser necesario si no existen versiones anteriores. Esta regla elimina esos markers después de 3 días.
        
>![[Pasted image 20250401203542.png]]
>![[Pasted image 20250401203600.png]]
>
        
2. expire-noncurrent-objects  
    **Objetivo**: Gestionar el almacenamiento de versiones anteriores de objetos para reducir costos.
    
    - **Eliminar versiones no actuales**: Cuando habilitas el versionado en un bucket de S3, las versiones antiguas de un objeto no se eliminan automáticamente. Esta regla elimina versiones no actuales después de un número determinado de días:
        
        - **7 días para producción**
            
        - **3 días para entornos de desarrollo/pruebas**
            
    - **Limitar el número de versiones de un objeto**: Esta parte de la regla asegura que no se almacenen más de 5 versiones de un mismo objeto, eliminando versiones más antiguas que superen el límite.
        
>![[Pasted image 20250401203644.png]]
>![[Pasted image 20250401203658.png]]
>
        
3. transition-current-objects-to-intelligent-tiering  
    **Objetivo**: Optimizar el costo del almacenamiento de objetos mediante el uso de diferentes clases de almacenamiento.
    
    - Esta regla **mueve objetos más grandes de 128 KiB a la clase de almacenamiento Intelligent-Tiering** de manera automática después de su carga (0 días).
        
    - **Intelligent-Tiering** es útil para objetos que tienen patrones de acceso impredecibles. Mueve automáticamente los objetos entre las clases de almacenamiento más económicas (Archive y Frequent Access) según la frecuencia de acceso.
        
>![[Pasted image 20250401203749.png]]
>![[Pasted image 20250401203803.png]]
>![[Pasted image 20250401203812.png]]
>
---

### Otras Reglas de Lifecycle que Puedes Usar en AWS S3

1. Transition Objects to a Different Storage Class
    
    - **Regla de transición de almacenamiento**: Mueve los objetos a una clase de almacenamiento más económica, como **Glacier** o **Deep Archive**, después de un determinado período de tiempo. Esto es útil para datos que no se acceden frecuentemente pero que necesitan ser retenidos por mucho tiempo. Ejemplo:
        
        - Mover objetos a **Glacier** después de 30 días.
            
        - Mover objetos a **Deep Archive** después de 365 días.
            
2. Expire Objects (Delete Objects)
    
    - **Eliminación de objetos**: Esta regla elimina objetos automáticamente después de un período de tiempo. Es útil para los objetos que ya no son necesarios después de un tiempo, como registros de auditoría o datos temporales. Ejemplo:
        
        - Eliminar objetos **después de 30 días** de creación.
            
3. Expiration of Object Versions
    
    - **Expiración de versiones**: Similar a la regla `expire-noncurrent-objects`, pero se aplica específicamente a las versiones de los objetos. Puedes eliminar versiones antiguas automáticamente después de un número determinado de días para reducir costos.
        
4. Abort Incomplete Multipart Uploads
    
    - **Abortar cargas incompletas**: Similar a la regla **daily-housekeeping**, pero con un enfoque específico en cargas incompletas. Esta regla **cierra automáticamente las cargas incompletas** de multipart upload después de un determinado número de días. Ejemplo:
        
        - Abortarlas después de **7 días** si la carga no ha terminado.
            

---
### ¿Por qué usar Lifecycle Policies?

- **Reducción de costos**: El almacenamiento en AWS S3 es **por uso**, por lo que optimizar el ciclo de vida de los objetos puede reducir significativamente los costos.
    
- **Automatización**: Las políticas de ciclo de vida te permiten **automatizar** la gestión del almacenamiento, sin intervención manual.
    
- **Mejor administración de datos**: Te ayuda a mantener el bucket limpio y eficiente, gestionando datos obsoletos o no utilizados.
    