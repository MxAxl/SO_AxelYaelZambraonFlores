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

## 3.2 Memoria Real
### **1. Programa en Python para simular administración de memoria con particiones fijas**

```python
class ParticionFija:
    def __init__(self, tamaño):
        self.tamaño = tamaño
        self.proceso = None  # Proceso asignado a esta partición

    def asignar_proceso(self, proceso):
        if proceso.tamaño <= self.tamaño:
            self.proceso = proceso
            return True
        return False

    def liberar(self):
        self.proceso = None


class Proceso:
    def __init__(self, id, tamaño):
        self.id = id
        self.tamaño = tamaño


# Configuración inicial
particiones = [ParticionFija(100), ParticionFija(200), ParticionFija(300)]  # Particiones fijas
procesos = [Proceso(1, 120), Proceso(2, 90), Proceso(3, 300), Proceso(4, 50)]  # Procesos

# Simulación de asignación
for proceso in procesos:
    asignado = False
    for particion in particiones:
        if particion.proceso is None and particion.asignar_proceso(proceso):
            print(f"Proceso {proceso.id} (Tamaño: {proceso.tamaño}) asignado a partición de tamaño {particion.tamaño}.")
            asignado = True
            break
    if not asignado:
        print(f"Proceso {proceso.id} (Tamaño: {proceso.tamaño}) no pudo ser asignado.")

# Mostrar el estado final
print("\nEstado final de las particiones:")
for i, particion in enumerate(particiones):
    if particion.proceso:
        print(f"Partición {i+1} (Tamaño: {particion.tamaño}) -> Proceso {particion.proceso.id}")
    else:
        print(f"Partición {i+1} (Tamaño: {particion.tamaño}) -> Vacía")
```


### **2. Algoritmo para asignar procesos usando "Primera Cabida"**

El algoritmo "Primera Cabida" asigna cada proceso al primer bloque de memoria que sea lo suficientemente grande. Aquí está la implementación:

```python
class BloqueMemoria:
    def __init__(self, tamaño):
        self.tamaño = tamaño
        self.proceso = None

    def asignar_proceso(self, proceso):
        if proceso.tamaño <= self.tamaño:
            self.proceso = proceso
            self.tamaño -= proceso.tamaño  # Reducir el tamaño disponible
            return True
        return False


class Proceso:
    def __init__(self, id, tamaño):
        self.id = id
        self.tamaño = tamaño


# Configuración inicial
bloques = [BloqueMemoria(400), BloqueMemoria(200), BloqueMemoria(300)]  # Bloques de memoria disponibles
procesos = [Proceso(1, 150), Proceso(2, 100), Proceso(3, 250), Proceso(4, 300)]  # Procesos

# Algoritmo de primera cabida
for proceso in procesos:
    asignado = False
    for bloque in bloques:
        if bloque.asignar_proceso(proceso):
            print(f"Proceso {proceso.id} (Tamaño: {proceso.tamaño}) asignado al bloque de tamaño {bloque.tamaño + proceso.tamaño}.")
            asignado = True
            break
    if not asignado:
        print(f"Proceso {proceso.id} (Tamaño: {proceso.tamaño}) no pudo ser asignado.")

# Mostrar el estado final
print("\nEstado final de los bloques de memoria:")
for i, bloque in enumerate(bloques):
    if bloque.proceso:
        print(f"Bloque {i+1} (Espacio restante: {bloque.tamaño}) -> Proceso {bloque.proceso.id}")
    else:
        print(f"Bloque {i+1} (Espacio restante: {bloque.tamaño}) -> Vacío")
```


Ambos programas son fáciles de modificar dependiendo de los tamaños de particiones o procesos que desee simular.

