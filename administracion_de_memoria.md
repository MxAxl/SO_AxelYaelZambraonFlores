# Administracion de memoria
## 3.1 Política y filosofía
### Diferencia entre fragmentación interna y externa

La fragmentación interna y externa son problemas relacionados con la gestión de memoria en los sistemas operativos, pero tienen orígenes y efectos distintos.

1. **Fragmentación interna**:  
   Este tipo de fragmentación ocurre cuando el sistema asigna más memoria de la necesaria a un proceso. Por ejemplo, si un bloque de memoria es de 4 KB pero el proceso solo necesita 2.5 KB, el espacio restante (1.5 KB) queda inutilizado.  
   **Impacto en el rendimiento**: La memoria no utilizada dentro de los bloques asignados puede acumularse, reduciendo la memoria disponible para otros procesos. Esto puede llevar a un uso ineficiente de la memoria y ralentizar el sistema.

2. **Fragmentación externa**:  
   Aquí el problema surge cuando la memoria libre está dispersa en pequeños bloques no contiguos. Esto sucede porque los procesos se cargan y descargan dinámicamente, dejando “huecos” en la memoria que no son lo suficientemente grandes para acomodar nuevos procesos.  
   **Impacto en el rendimiento**: Aunque puede haber suficiente memoria libre en total, si no hay un bloque contiguo del tamaño requerido, el sistema puede fallar al asignar memoria. Esto provoca una disminución de la eficiencia y posibles bloqueos de procesos.

### Políticas de reemplazo de páginas en sistemas operativos

Las políticas de reemplazo de páginas se usan para decidir qué página de memoria se debe reemplazar cuando no hay suficiente espacio en la memoria principal. Estas son las más comunes:

1. **FIFO (First In, First Out)**:  
   Reemplaza la página que llegó primero a la memoria, sin importar si se está utilizando frecuentemente o no.  
   **Ventaja**: Es simple de implementar.  
   **Desventaja**: Puede llevar al problema de "anomalía de Belady", donde agregar más memoria aumenta los fallos de página.

2. **LRU (Least Recently Used)**:  
   Reemplaza la página que no se ha usado en el mayor tiempo.  
   **Ventaja**: Es más eficiente que FIFO porque utiliza el historial de uso reciente.  
   **Desventaja**: Es costoso en términos de tiempo y recursos para mantener el registro del uso.

3. **LFU (Least Frequently Used)**:  
   Reemplaza la página que se ha usado menos veces en total.  
   **Ventaja**: Prioriza páginas más útiles.  
   **Desventaja**: Si una página fue usada mucho tiempo atrás pero ya no es relevante, no la reemplaza rápidamente.

4. **Algoritmo Óptimo**:  
   Reemplaza la página que no se necesitará en el futuro más próximo.  
   **Ventaja**: Es el mejor en términos de eficiencia.  
   **Desventaja**: Es impráctico porque requiere conocer el futuro.


**¿Cuál considero más eficiente y por qué?**  
LRU es probablemente el más eficiente en escenarios reales porque prioriza páginas con más uso reciente, optimizando el rendimiento. Aunque es más complejo, los sistemas modernos pueden manejarlo mejor con hardware y software avanzados.

## Memoria Real
