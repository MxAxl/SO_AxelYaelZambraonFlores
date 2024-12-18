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

## 3.2 Memoria real
### 1. Programa en Python para simular administración de memoria con particiones fijas.

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


### 2. Algoritmo para asignar procesos usando "Primera Cabida"

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

## 3.3 Organización de memoria virtual
### 1. Concepto de paginación y segmentación

#### **Paginación**  
La **paginación** es una técnica de administración de memoria donde se divide la memoria en bloques de tamaño fijo llamados "marcos" (frames) en la memoria física y "páginas" en la memoria lógica. Cuando un proceso necesita memoria, se le asignan páginas que pueden almacenarse en cualquier marco disponible, sin necesidad de ser contiguas.

**Ventajas de la paginación**:  
- Elimina la fragmentación externa porque los bloques son de tamaño fijo.  
- Permite usar memoria no contigua, lo que optimiza el espacio disponible.  
- Es más fácil de implementar con hardware dedicado, como una Unidad de Gestión de Memoria (MMU).  

**Desventajas de la paginación**:  
- Puede haber fragmentación interna si las páginas no se llenan completamente.  
- El tiempo de acceso a memoria aumenta debido a la traducción de direcciones lógicas a físicas.  

#### **Segmentación**  
La **segmentación** divide la memoria en segmentos lógicos que representan diferentes partes de un programa, como código, datos y pila. Cada segmento tiene un tamaño variable y se asigna de manera contigua en la memoria física.  

**Ventajas de la segmentación**:  
- Proporciona una visión lógica más natural para los desarrolladores.  
- Permite ajustar mejor la memoria a las necesidades del programa (sin fragmentación interna).  
- Facilita la protección y compartición de memoria entre procesos.  

**Desventajas de la segmentación**:  
- Puede generar fragmentación externa, ya que los segmentos tienen tamaños variables.  
- Requiere mecanismos más complejos para el manejo de memoria y protección.  

### 2. Programa para simular una tabla de páginas

```python
import random

class TablaPaginas:
    def __init__(self, num_paginas, num_marcos):
        self.num_paginas = num_paginas
        self.num_marcos = num_marcos
        self.tabla = [-1] * num_paginas  # -1 indica que la página no está en memoria
        self.marcos = [None] * num_marcos  # Marcos de memoria física

    def asignar_pagina(self, pagina):
        # Verificar si la página ya está en memoria
        if pagina in self.tabla:
            print(f"La página {pagina} ya está en memoria (marco {self.tabla.index(pagina)}).")
            return
        
        # Buscar un marco libre
        try:
            marco_libre = self.marcos.index(None)
        except ValueError:
            # No hay marcos libres; usar reemplazo FIFO
            marco_libre = random.randint(0, self.num_marcos - 1)
            pagina_reemplazada = self.marcos[marco_libre]
            self.tabla[self.tabla.index(pagina_reemplazada)] = -1
            print(f"Reemplazando página {pagina_reemplazada} del marco {marco_libre}.")
        
        # Asignar la página al marco
        self.marcos[marco_libre] = pagina
        self.tabla[pagina] = marco_libre
        print(f"Página {pagina} asignada al marco {marco_libre}.")

    def mostrar_tabla(self):
        print("\nTabla de Páginas:")
        for i, marco in enumerate(self.tabla):
            print(f"Página {i}: {'Marco ' + str(marco) if marco != -1 else 'No asignada'}")

        print("\nEstado de los marcos:")
        for i, pagina in enumerate(self.marcos):
            print(f"Marco {i}: {'Página ' + str(pagina) if pagina is not None else 'Vacío'}")


# Configuración inicial
num_paginas = 10  # Número total de páginas
num_marcos = 4    # Número total de marcos en memoria física
tabla = TablaPaginas(num_paginas, num_marcos)

# Simulación de acceso aleatorio
print("Simulación de accesos aleatorios a memoria:")
for _ in range(15):  # 15 accesos aleatorios
    pagina = random.randint(0, num_paginas - 1)
    print(f"\nAccediendo a la página {pagina}:")
    tabla.asignar_pagina(pagina)
    tabla.mostrar_tabla()
```

### **Explicacion de funcionalidad**  
1. **Clase `TablaPaginas`**:  
   - Administra las páginas y los marcos.  
   - Mantiene una tabla que indica qué marco (si lo hay) contiene cada página.  
2. **Acceso aleatorio**:  
   - Simula accesos a páginas aleatorias, asignándolas a marcos libres o reemplazándolas si no hay marcos disponibles.  
3. **Reemplazo**:  
   - Implementado de manera sencilla usando un marco seleccionado al azar (puedes cambiarlo a FIFO o LRU si necesitas).  

## 3.4 Administración de memoria virtual
### 1. Código que implementa el algoritmo de reemplazo de página "Least Recently Used" (LRU)

Aquí tienes una implementación en Python del algoritmo LRU para el reemplazo de páginas:

```python
class LRUCache:
    def __init__(self, capacidad):
        self.capacidad = capacidad
        self.cache = []  # Lista para almacenar las páginas en orden de uso reciente

    def acceder_pagina(self, pagina):
        if pagina in self.cache:
            # Si la página ya está en la caché, se actualiza su posición
            self.cache.remove(pagina)
            self.cache.append(pagina)
            print(f"Página {pagina} accedida (ya estaba en memoria).")
        else:
            # Si la página no está en la caché
            if len(self.cache) == self.capacidad:
                pagina_reemplazada = self.cache.pop(0)  # Eliminar la página menos recientemente usada
                print(f"Reemplazando página {pagina_reemplazada} con la página {pagina}.")
            else:
                print(f"Asignando página {pagina} (hay espacio disponible).")
            self.cache.append(pagina)  # Añadir la nueva página a la caché

    def mostrar_cache(self):
        print(f"Estado actual de la caché: {self.cache}")


# Simulación
capacidad_cache = 3  # Número de marcos de memoria disponibles
lru = LRUCache(capacidad_cache)

# Secuencia de accesos a páginas
secuencia_paginas = [1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5]

print("Simulación del algoritmo LRU:")
for pagina in secuencia_paginas:
    print(f"\nAccediendo a la página {pagina}:")
    lru.acceder_pagina(pagina)
    lru.mostrar_cache()
```


### **Explicación del código**
1. **Clase `LRUCache`**:  
   - La lista `cache` mantiene las páginas en memoria en orden de uso reciente.  
   - Cuando se accede a una página:
     - Si está en memoria, se mueve al final (más reciente).  
     - Si no está y la memoria está llena, se elimina la página menos recientemente usada (la primera en la lista).  
   - Si hay espacio disponible, la página simplemente se agrega.  

2. **Secuencia de prueba**:  
   - Una lista de accesos a páginas simula el comportamiento del sistema.  
   - Cada acceso imprime el estado de la memoria.


### 2. Diagrama del proceso de traducción de direcciones virtuales a físicas

Aquí está una descripción del diagrama para representar el proceso. El esquema básico incluye los siguientes pasos:

1. **Dirección Virtual**:  
   Una dirección lógica generada por el programa.

2. **Tabla de Páginas**:  
   La dirección virtual se divide en:
   - **Número de página**: Identifica la página en memoria virtual.  
   - **Desplazamiento (offset)**: Posición dentro de la página.  
   La tabla de páginas traduce el número de página a un marco en la memoria física.

3. **Dirección Física**:  
   La dirección resultante se compone del número de marco (obtenido de la tabla) y el desplazamiento.

```
| Generación de Dirección Virtual       |
| ↓                                     |
| Consulta a la Tabla de Páginas        |
| ↓                                     |
| Verificación de la Validez            |
| ↓                                     |
| Manejo de Page Fault                  |
| ↓                                     |
| Actualización de la Tabla de Páginas  |
| ↓                                     |
| Acceso a la Memoria Física            |
| ↓                                     |
| Finalización del Proceso              |
```
## Integración


# Administración de Entrada/Salida
