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
1. **Clase TablaPaginas**:  
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
1. **Clase LRUCache**:  
   - La lista cache mantiene las páginas en memoria en orden de uso reciente.  
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
### 1. Administración de Memoria Virtual en un Sistema Operativo Moderno: Linux

En **Linux**, la administración de memoria virtual es realizada principalmente por el **MMU** (Unidad de Gestión de Memoria) y el kernel, utilizando una combinación de **paginación** y **swapping**.  

#### **Funcionamiento básico**:
- **Paginación**: La memoria virtual está dividida en bloques llamados **páginas**, típicamente de 4 KB. Estas páginas se asignan a **marcos de memoria física**, permitiendo que un proceso acceda a memoria no contigua.  
- **Tabla de Páginas**: Cada proceso tiene su propia tabla de páginas, que mapea páginas virtuales a marcos físicos. Cuando un proceso intenta acceder a una dirección virtual, el sistema consulta la tabla para traducirla en una dirección física.  
- **Page Fault**: Si una página no está presente en memoria física, se genera un **page fault**. El kernel trae la página requerida desde el **swap** (espacio en disco) a la memoria física.  
- **Swapping**: Cuando no hay suficiente memoria física, el sistema operativo selecciona páginas menos usadas (a través de algoritmos como LRU) y las escribe al archivo de **swap** en disco.  
- **Cache y Buffers**: Linux también administra cachés y buffers en memoria para optimizar accesos frecuentes.  

#### **Ventajas**:  
- Permite que los procesos utilicen más memoria que la disponible físicamente.  
- Optimiza el uso de la RAM y evita fragmentación externa.  

#### **Desventajas**:  
- El acceso a la memoria swap es mucho más lento que a la memoria RAM.  
- Un uso excesivo del swap puede provocar **thrashing**, donde el sistema pasa más tiempo intercambiando páginas que ejecutando procesos.

### 2. Simulación de Swapping de Procesos en Memoria Virtual

#### **Código:**
```python
import time
import random

class Proceso:
    def __init__(self, id, tamaño):
        self.id = id
        self.tamaño = tamaño  # Tamaño de memoria requerido por el proceso

class SistemaMemoria:
    def __init__(self, memoria_total, tamaño_swap):
        self.memoria_total = memoria_total  # Capacidad de memoria física
        self.memoria_disponible = memoria_total
        self.swap = []  # Swap en disco
        self.tamaño_swap = tamaño_swap
        self.procesos_en_memoria = []  # Procesos activos en memoria

    def cargar_proceso(self, proceso):
        if proceso.tamaño <= self.memoria_disponible:
            self.procesos_en_memoria.append(proceso)
            self.memoria_disponible -= proceso.tamaño
            print(f"Proceso {proceso.id} cargado en memoria. Memoria disponible: {self.memoria_disponible}")
        else:
            print(f"Memoria insuficiente para el proceso {proceso.id}. Realizando swapping...")
            self.realizar_swapping(proceso)

    def realizar_swapping(self, nuevo_proceso):
        # Simular intercambio del proceso menos usado (FIFO en este ejemplo)
        if not self.procesos_en_memoria:
            print("No hay procesos en memoria para hacer swapping.")
            return

        proceso_swap = self.procesos_en_memoria.pop(0)  # Eliminar el primer proceso
        self.swap.append(proceso_swap)
        self.memoria_disponible += proceso_swap.tamaño
        print(f"Proceso {proceso_swap.id} enviado a swap. Memoria liberada: {proceso_swap.tamaño}")
        print(f"Memoria disponible: {self.memoria_disponible}")

        # Cargar el nuevo proceso en memoria
        self.cargar_proceso(nuevo_proceso)

    def mostrar_estado(self):
        print("\n--- Estado del Sistema ---")
        print("Procesos en Memoria:")
        for p in self.procesos_en_memoria:
            print(f"Proceso {p.id} - Tamaño: {p.tamaño}")
        print("\nSwap:")
        for p in self.swap:
            print(f"Proceso {p.id} - Tamaño: {p.tamaño}")
        print(f"\nMemoria Disponible: {self.memoria_disponible}")
        print("--------------------------")

# Configuración inicial
memoria_total = 200  # Capacidad total de memoria física
tamaño_swap = 300    # Tamaño del swap en disco
sistema = SistemaMemoria(memoria_total, tamaño_swap)

# Simulación de procesos
procesos = [Proceso(i, random.randint(50, 100)) for i in range(1, 6)]

# Cargar procesos en memoria
for proceso in procesos:
    print(f"\nIntentando cargar Proceso {proceso.id} (Tamaño: {proceso.tamaño})")
    sistema.cargar_proceso(proceso)
    sistema.mostrar_estado()
    time.sleep(1)  # Simulación de tiempo de procesamiento
```


### **Explicación del código**:
1. **Clase Proceso**: Representa un proceso con un ID y un tamaño de memoria requerido.
2. **Clase SistemaMemoria**:  
   - Administra la memoria física y el swap.  
   - Si no hay suficiente memoria para un nuevo proceso, realiza swapping eliminando un proceso menos reciente (FIFO).  
3. **Simulación**:  
   - Se generan procesos con tamaños aleatorios.  
   - Cada proceso se intenta cargar en memoria; si no hay espacio, el sistema realiza swapping.  
   - Se imprime el estado actual de la memoria y el swap después de cada acción.


### **Salida esperada** (Ejemplo):
```
Intentando cargar Proceso 1 (Tamaño: 70)
Proceso 1 cargado en memoria. Memoria disponible: 130

--- Estado del Sistema ---
Procesos en Memoria:
Proceso 1 - Tamaño: 70

Swap:

Memoria Disponible: 130
--------------------------

Intentando cargar Proceso 2 (Tamaño: 60)
Proceso 2 cargado en memoria. Memoria disponible: 70

--- Estado del Sistema ---
Procesos en Memoria:
Proceso 1 - Tamaño: 70
Proceso 2 - Tamaño: 60

Swap:

Memoria Disponible: 70
--------------------------

Intentando cargar Proceso 3 (Tamaño: 90)
Memoria insuficiente para el proceso 3. Realizando swapping...
Proceso 1 enviado a swap. Memoria liberada: 70
Proceso 3 cargado en memoria. Memoria disponible: 10

--- Estado del Sistema ---
Procesos en Memoria:
Proceso 2 - Tamaño: 60
Proceso 3 - Tamaño: 90

Swap:
Proceso 1 - Tamaño: 70

Memoria Disponible: 10
--------------------------
```

# Administración de Entrada/Salida
## 4.1 Dispositivos y manejadores de dispositivos
### 1. Diferencia entre dispositivos de bloque y dispositivos de carácter

Los dispositivos de bloque y dispositivos de carácter son dos tipos fundamentales de dispositivos manejados por el sistema operativo.  

- **Dispositivos de bloque:**  
  - Acceden a los datos en bloques de tamaño fijo (típicamente de 512 bytes o múltiplos).  
  - Soportan operaciones de acceso aleatorio, lo que significa que es posible leer/escribir en cualquier posición del dispositivo directamente.  
  - Son ideales para almacenamiento masivo.  
  - **Ejemplo:** Discos duros, SSDs, memorias USB.  

- **Dispositivos de carácter:**  
  - Transfieren datos un carácter o byte a la vez.  
  - Son dispositivos orientados a flujo, lo que significa que no admiten acceso aleatorio.  
  - Se utilizan comúnmente para dispositivos de entrada y salida secuencial.  
  - **Ejemplo:** Teclados, ratones, terminales de consola.  

### 2. Diseño de un programa para un manejador de dispositivos sencillo  

```python
import logging
import random
import time

# Configuración del registro de actividad
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s",
    filename="device_log.txt",
    filemode="w",
)


class VirtualDevice:
    def __init__(self, buffer_size=5):
        self.buffer = []  # Almacén para los datos recibidos
        self.buffer_size = buffer_size  # Tamaño máximo del búfer
        self.interrupt_enabled = False  # Bandera para simular interrupciones

    def write_to_device(self, data):
        """Simula la entrada de datos al dispositivo."""
        if len(self.buffer) >= self.buffer_size:
            logging.warning("Error: El búfer está lleno. No se pueden escribir más datos.")
            raise BufferError("El búfer está lleno.")
        self.buffer.append(data)
        logging.info(f"Escribiendo datos al dispositivo: {data}")
        print(f"Escribiendo datos al dispositivo: {data}")

        if self.interrupt_enabled:
            self._trigger_interrupt("Datos escritos exitosamente")

    def read_from_device(self):
        """Lee datos del búfer del dispositivo."""
        if not self.buffer:
            logging.warning("Error: No hay datos para leer.")
            print("No hay datos para leer.")
            return None
        data = self.buffer.pop(0)
        logging.info(f"Leyendo datos del dispositivo: {data}")
        print(f"Leyendo datos del dispositivo: {data}")
        return data

    def buffer_status(self):
        """Muestra el estado actual del búfer."""
        status = f"Estado del búfer: {self.buffer}"
        logging.info(status)
        print(status)
        return status

    def enable_interrupts(self):
        """Habilita las interrupciones simuladas."""
        self.interrupt_enabled = True
        logging.info("Interrupciones habilitadas.")
        print("Interrupciones habilitadas.")

    def disable_interrupts(self):
        """Deshabilita las interrupciones simuladas."""
        self.interrupt_enabled = False
        logging.info("Interrupciones deshabilitadas.")
        print("Interrupciones deshabilitadas.")

    def _trigger_interrupt(self, message):
        """Simula el manejo de una interrupción."""
        logging.info(f"Interrupción: {message}")
        print(f"Interrupción manejada: {message}")

    def simulate_error(self):
        """Simula un error aleatorio del dispositivo."""
        error_types = ["Error de conexión", "Error de hardware", "Error desconocido"]
        error = random.choice(error_types)
        logging.error(f"Error simulado: {error}")
        print(f"Error simulado: {error}")
        return error


# Simulación de uso
if __name__ == "__main__":
    device = VirtualDevice(buffer_size=3)

    try:
        # Habilitar interrupciones
        device.enable_interrupts()

        # Escribir datos al dispositivo
        device.write_to_device("Dato1")
        device.write_to_device("Dato2")
        device.write_to_device("Dato3")

        # Intentar escribir cuando el búfer está lleno
        try:
            device.write_to_device("Dato4")
        except BufferError as e:
            print(f"Excepción capturada: {e}")

        # Leer datos del dispositivo
        device.read_from_device()
        device.read_from_device()
        device.read_from_device()
        device.read_from_device()  # Intento de leer más allá de los datos disponibles

        # Simular error del dispositivo
        device.simulate_error()

        # Mostrar el estado del búfer
        device.buffer_status()

    except Exception as e:
        logging.critical(f"Excepción no manejada: {e}")
        print(f"Excepción no manejada: {e}")
```

### Nuevas Funcionalidades Incluidas  

1. **Manejo de Errores:**
   - El programa controla errores como intentar escribir en un búfer lleno mediante la excepción BufferError.
   - Maneja intentos de leer cuando el búfer está vacío con mensajes de advertencia y registros en el log.

2. **Interrupciones Simuladas:**
   - Se añade la capacidad de habilitar/deshabilitar interrupciones.
   - Cada vez que se escribe un dato al dispositivo, si las interrupciones están habilitadas, se genera un mensaje de interrupción.

3. **Registro de Actividad (Logging):**
   - Cada acción (lectura, escritura, error) se registra en un archivo device_log.txt con detalles como fecha y hora.
   - Se incluyen niveles de registro: INFO para eventos normales, WARNING para advertencias y ERROR/CRITICAL para errores.

4. **Simulación de Errores del Dispositivo:**
   - Se incluye un método para simular errores aleatorios que podrían ocurrir en un dispositivo real, como problemas de conexión o fallos de hardware.

5. **Limitación del Búfer:**
   - El tamaño del búfer es configurable, simulando la capacidad limitada de un dispositivo real.

### Ejemplo de Salida  

```
Interrupciones habilitadas.
Escribiendo datos al dispositivo: Dato1
Interrupción manejada: Datos escritos exitosamente
Escribiendo datos al dispositivo: Dato2
Interrupción manejada: Datos escritos exitosamente
Escribiendo datos al dispositivo: Dato3
Interrupción manejada: Datos escritos exitosamente
Excepción capturada: El búfer está lleno.
Leyendo datos del dispositivo: Dato1
Leyendo datos del dispositivo: Dato2
Leyendo datos del dispositivo: Dato3
No hay datos para leer.
Error simulado: Error de hardware
Estado del búfer: []
```

## 4.2 Mecanismos y funciones de los manejadores de dispositivos
### 1. ¿Qué es la interrupción por E/S y cómo la administra el sistema operativo?

**Interrupción por E/S (Entrada/Salida):**  
Una interrupción por E/S es una señal enviada por un dispositivo (como un disco duro, teclado o impresora) al procesador para indicar que una operación de entrada o salida ha finalizado o necesita atención. Estas interrupciones son fundamentales en los sistemas operativos modernos, ya que permiten manejar múltiples tareas de forma eficiente, liberando al procesador de esperar por eventos o datos.  

**Proceso de manejo de una interrupción por E/S:**  

1. **Solicitud de interrupción:**  
   El dispositivo genera una señal de interrupción para notificar al procesador que requiere atención.  

2. **Cambio de contexto:**  
   El procesador pausa su tarea actual y guarda su estado (registro, contadores, etc.) para retomarla después de atender la interrupción.  

3. **Llamado al manejador de interrupción:**  
   El sistema operativo ejecuta un manejador de interrupciones específico (un programa que sabe cómo responder al evento).  

4. **Atención de la interrupción:**  
   Se realiza la acción requerida, como leer datos del dispositivo o enviarle una respuesta.  

5. **Retorno al contexto anterior:**  
   Una vez atendida la interrupción, el procesador restaura el estado previo y continúa ejecutando la tarea original.  

**Ejemplo en pseudocódigo:**  

```plaintext
Inicio del programa:
    Ejecutar proceso principal.

Mientras el programa esté corriendo:
    Si un dispositivo genera una interrupción:
        Pausar proceso actual.
        Guardar estado del proceso.
        Llamar al manejador de interrupciones.

Manejador de interrupciones:
    Identificar el dispositivo que generó la interrupción.
    Realizar la acción necesaria (lectura o escritura).
    Señalar al dispositivo que la solicitud fue atendida.
    Restaurar el estado del proceso anterior.
    Reanudar el proceso principal.

Fin del programa.
```

Este proceso asegura que el sistema operativo responda rápidamente a eventos críticos mientras sigue trabajando en otras tareas.


### 2. Programa que utilice el manejo de interrupciones en un sistema básico de simulación


A continuación, presentamos un programa en Python que simula un sistema básico con manejo de interrupciones, incluyendo dispositivos ficticios que generan interrupciones aleatorias.  


#### **Código en Python**

```python
import random
import time
from queue import Queue

class InterruptController:
    def __init__(self):
        self.interrupt_queue = Queue()  # Cola para almacenar las interrupciones

    def generate_interrupt(self, device_name):
        """Simula la generación de una interrupción."""
        print(f"[Interrupción generada] Dispositivo: {device_name}")
        self.interrupt_queue.put(device_name)

    def handle_interrupt(self):
        """Manejador de interrupciones."""
        if not self.interrupt_queue.empty():
            device_name = self.interrupt_queue.get()
            print(f"[Manejando interrupción] Dispositivo: {device_name}")
            # Simula la acción correspondiente al dispositivo
            if device_name == "teclado":
                self._handle_keyboard()
            elif device_name == "disco_duro":
                self._handle_hard_disk()
            elif device_name == "impresora":
                self._handle_printer()
            else:
                print("[Advertencia] Dispositivo desconocido.")
        else:
            print("No hay interrupciones pendientes.")

    def _handle_keyboard(self):
        print("[Teclado] Leyendo entrada del usuario...")
        time.sleep(1)  # Simula el tiempo necesario para manejar el evento

    def _handle_hard_disk(self):
        print("[Disco Duro] Realizando operación de E/S...")
        time.sleep(2)  # Simula tiempo de acceso al disco

    def _handle_printer(self):
        print("[Impresora] Imprimiendo documento...")
        time.sleep(3)  # Simula el proceso de impresión


class System:
    def __init__(self):
        self.interrupt_controller = InterruptController()
        self.running = True

    def simulate_device_activity(self):
        """Simula dispositivos generando interrupciones al azar."""
        devices = ["teclado", "disco_duro", "impresora"]
        while self.running:
            time.sleep(random.randint(1, 3))  # Intervalo aleatorio entre interrupciones
            device_name = random.choice(devices)
            self.interrupt_controller.generate_interrupt(device_name)

    def run(self):
        """Ejecuta el sistema y maneja interrupciones en paralelo."""
        print("Sistema iniciado. Presiona Ctrl+C para detener.")
        try:
            while self.running:
                self.interrupt_controller.handle_interrupt()
                time.sleep(1)  # Ciclo principal del sistema
        except KeyboardInterrupt:
            print("\nSistema detenido por el usuario.")
            self.running = False


# Simulación del sistema
if __name__ == "__main__":
    system = System()

    # Simular actividad de dispositivos en un hilo separado (si fuera necesario)
    import threading

    device_thread = threading.Thread(target=system.simulate_device_activity, daemon=True)
    device_thread.start()

    # Ejecutar el sistema
    system.run()
```

#### **Explicación del Programa**

1. **Controlador de Interrupciones (InterruptController):**
   - Se encarga de manejar las interrupciones generadas por diferentes dispositivos.  
   - Usa una cola para almacenar las solicitudes de interrupción.  

2. **Manejadores Específicos:**
   - Métodos como _handle_keyboard, _handle_hard_disk, y _handle_printer simulan las acciones requeridas para manejar eventos generados por cada tipo de dispositivo.  

3. **Sistema Principal (System):**
   - Tiene un ciclo principal que constantemente verifica y maneja interrupciones pendientes.  
   - Simula la actividad de dispositivos que generan interrupciones al azar.  

4. **Interrupciones Concurrentes:**
   - Se usa un hilo para simular que los dispositivos generan interrupciones mientras el sistema principal las maneja.  

#### **Ejemplo de Salida**

```plaintext
Sistema iniciado. Presiona Ctrl+C para detener.
No hay interrupciones pendientes.
[Interrupción generada] Dispositivo: teclado
[Manejando interrupción] Dispositivo: teclado
[Teclado] Leyendo entrada del usuario...
[Interrupción generada] Dispositivo: impresora
[Manejando interrupción] Dispositivo: impresora
[Impresora] Imprimiendo documento...
[Interrupción generada] Dispositivo: disco_duro
[Manejando interrupción] Dispositivo: disco_duro
[Disco Duro] Realizando operación de E/S...
```
## 4.3 Estructuras de datos para manejo de dispositivos

### 1. ¿Qué es una cola de E/S?**  

Una **cola de E/S** es una estructura de datos que se utiliza en sistemas operativos para gestionar las solicitudes de entrada/salida (E/S) de los dispositivos. Las solicitudes son encoladas y atendidas de acuerdo con un criterio, como el orden de llegada (FIFO) o basado en prioridades.  

**Características principales:**  
- **Eficiencia:** Organiza múltiples solicitudes para un dispositivo, minimizando tiempos de espera.  
- **Sincronización:** Permite a los dispositivos procesar una solicitud a la vez, manejando otras en espera.  
- **Priorización:** En algunos casos, solicitudes más urgentes (como un acceso al disco para datos críticos) tienen mayor prioridad.  

**Ejemplo de uso:**  
Un disco duro con múltiples solicitudes de lectura/escritura usa una cola de E/S para procesarlas de manera eficiente, optimizando la posición del cabezal (algoritmos como SCAN o SSTF).  

#### **Simulación de una cola con prioridad**  

```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.queue = []  # Usamos una lista como base para la cola de prioridad

    def enqueue(self, priority, request):
        """Agrega una solicitud con prioridad a la cola."""
        heapq.heappush(self.queue, (priority, request))
        print(f"Solicitud '{request}' agregada con prioridad {priority}.")

    def dequeue(self):
        """Procesa la solicitud con mayor prioridad."""
        if self.is_empty():
            print("La cola de E/S está vacía. No hay solicitudes para procesar.")
            return None
        priority, request = heapq.heappop(self.queue)
        print(f"Procesando solicitud '{request}' con prioridad {priority}.")
        return request

    def peek(self):
        """Muestra la solicitud con mayor prioridad sin eliminarla."""
        if self.is_empty():
            print("La cola de E/S está vacía.")
            return None
        return self.queue[0]

    def is_empty(self):
        """Verifica si la cola está vacía."""
        return len(self.queue) == 0

    def display_queue(self):
        """Muestra el estado actual de la cola."""
        print("Estado actual de la cola de E/S:")
        for priority, request in sorted(self.queue):
            print(f"  - Solicitud '{request}' (Prioridad: {priority})")


# Simulación
if __name__ == "__main__":
    priority_queue = PriorityQueue()

    # Agregar solicitudes
    priority_queue.enqueue(2, "Leer archivo A")
    priority_queue.enqueue(1, "Escribir en disco B")
    priority_queue.enqueue(3, "Imprimir documento C")

    # Mostrar la cola
    priority_queue.display_queue()

    # Procesar solicitudes
    priority_queue.dequeue()
    priority_queue.dequeue()

    # Mostrar el estado después de procesar
    priority_queue.display_queue()

    # Agregar nuevas solicitudes
    priority_queue.enqueue(1, "Actualizar índice del disco D")

    # Procesar otra solicitud
    priority_queue.dequeue()

    # Mostrar la cola final
    priority_queue.display_queue()
```

#### **Explicación del código**  

- **enqueue(priority, request)**: Agrega solicitudes con prioridad a la cola.  
- **dequeue()**: Procesa y elimina la solicitud con mayor prioridad (valor numérico más bajo).  
- **peek()**: Permite ver la siguiente solicitud a procesar sin eliminarla.  
- **Uso de heapq:** Implementa una cola de prioridad de manera eficiente.  

### 2. Manejador de dispositivos utilizando una tabla de estructuras  

En esta sección, diseñaremos un manejador que utiliza una **tabla de estructuras** para administrar dispositivos. Cada dispositivo tiene un conjunto de propiedades (ID, tipo, estado, solicitudes pendientes, etc.).  

##### **Código en Python**

```python
class DeviceManager:
    def __init__(self):
        self.device_table = {}  # Tabla para almacenar los dispositivos

    def add_device(self, device_id, device_type):
        """Registra un nuevo dispositivo en la tabla."""
        if device_id in self.device_table:
            print(f"Error: El dispositivo {device_id} ya está registrado.")
            return
        self.device_table[device_id] = {
            "type": device_type,
            "status": "idle",  # Estado inicial
            "request_queue": []  # Cola de solicitudes asociada al dispositivo
        }
        print(f"Dispositivo '{device_id}' de tipo '{device_type}' registrado.")

    def remove_device(self, device_id):
        """Elimina un dispositivo de la tabla."""
        if device_id not in self.device_table:
            print(f"Error: El dispositivo {device_id} no existe.")
            return
        del self.device_table[device_id]
        print(f"Dispositivo '{device_id}' eliminado.")

    def add_request(self, device_id, request):
        """Agrega una solicitud a la cola del dispositivo."""
        if device_id not in self.device_table:
            print(f"Error: El dispositivo {device_id} no existe.")
            return
        self.device_table[device_id]["request_queue"].append(request)
        print(f"Solicitud '{request}' agregada al dispositivo '{device_id}'.")

    def process_request(self, device_id):
        """Procesa la siguiente solicitud en la cola del dispositivo."""
        if device_id not in self.device_table:
            print(f"Error: El dispositivo {device_id} no existe.")
            return
        if not self.device_table[device_id]["request_queue"]:
            print(f"El dispositivo '{device_id}' no tiene solicitudes pendientes.")
            return
        request = self.device_table[device_id]["request_queue"].pop(0)
        self.device_table[device_id]["status"] = "busy"
        print(f"Procesando solicitud '{request}' en el dispositivo '{device_id}'.")
        # Simular que el dispositivo vuelve a estar disponible
        self.device_table[device_id]["status"] = "idle"

    def display_devices(self):
        """Muestra la tabla de dispositivos."""
        print("Tabla de dispositivos:")
        for device_id, details in self.device_table.items():
            print(f"  - {device_id}: {details}")


# Simulación
if __name__ == "__main__":
    manager = DeviceManager()

    # Registrar dispositivos
    manager.add_device("dev1", "teclado")
    manager.add_device("dev2", "impresora")
    manager.add_device("dev3", "disco_duro")

    # Agregar solicitudes
    manager.add_request("dev1", "Escribir carácter A")
    manager.add_request("dev2", "Imprimir página 1")
    manager.add_request("dev3", "Leer sector 5")

    # Mostrar la tabla de dispositivos
    manager.display_devices()

    # Procesar solicitudes
    manager.process_request("dev1")
    manager.process_request("dev2")

    # Mostrar el estado después de procesar
    manager.display_devices()

    # Eliminar un dispositivo
    manager.remove_device("dev1")
    manager.display_devices()
```

##### **Explicación del código**

1. **Tabla de dispositivos (device_table):**
   - Es un diccionario que asocia cada dispositivo con su tipo, estado y cola de solicitudes.  

2. **Métodos principales:**
   - add_device: Registra nuevos dispositivos.  
   - remove_device: Elimina dispositivos de la tabla.  
   - add_request: Agrega solicitudes a la cola de un dispositivo.  
   - process_request: Procesa la primera solicitud en la cola del dispositivo y actualiza su estado.  

3. **Interacción:**
   - Puedes registrar dispositivos, agregarles solicitudes y procesarlas de manera ordenada.  

#### **Ejemplo de salida**

```plaintext
Dispositivo 'dev1' de tipo 'teclado' registrado.
Dispositivo 'dev2' de tipo 'impresora' registrado.
Dispositivo 'dev3' de tipo 'disco_duro' registrado.
Solicitud 'Escribir carácter A' agregada al dispositivo 'dev1'.
Solicitud 'Imprimir página 1' agregada al dispositivo 'dev2'.
Solicitud 'Leer sector 5' agregada al dispositivo 'dev3'.
Tabla de dispositivos:
  - dev1: {'type': 'teclado', 'status': 'idle', 'request_queue': ['Escribir carácter A']}
  - dev2: {'type': 'impresora', 'status': 'idle', 'request_queue': ['Imprimir página 1']}
  - dev3: {'type': 'disco_duro', 'status': 'idle', 'request_queue': ['Leer sector 5']}
Procesando solicitud 'Escribir carácter A' en el dispositivo 'dev1'.
Procesando solicitud 'Imprimir página 1' en el dispositivo 'dev2'.
Tabla de dispositivos:
  - dev1: {'type': 'teclado', 'status': 'idle', 'request_queue': []}
  - dev2: {'type': 'impresora', 'status': 'idle', 'request_queue': []}
  - dev3: {'type': 'disco_duro', 'status': 'idle', 'request_queue': ['Leer sector 5']}
Dispositivo 'dev1' eliminado.
Tabla de dispositivos:
  - dev2: {'type': 'impresora', 'status': 'idle', 'request_queue': []}
  - dev3: {'type': 'disco_duro', 'status': 'idle', 'request_queue': ['Leer sector 5']}
```
## 4.4 Operaciones de Entrada/Salida

### 1. Flujo del proceso de lectura de un archivo desde un disco magnético

#### **Flujo del proceso de lectura de un archivo**  

![alt text](<diagrama.png>)

#### **Simulación del proceso en código Python (básico)**  

```python
import os
import time

class DiskSimulator:
    def __init__(self, file_system):
        self.file_system = file_system  # Simula una tabla de archivos (diccionario)

    def read_file(self, file_name):
        """Simula el proceso de lectura de un archivo desde un disco magnético."""
        print(f"Solicitud de lectura para el archivo '{file_name}'...")

        # Paso 1: Verificar si el archivo existe en el sistema
        if file_name not in self.file_system:
            print("[Error] El archivo no existe en el sistema de archivos.")
            return

        # Paso 2: Obtener la ubicación física del archivo
        file_data = self.file_system[file_name]
        print("Accediendo a la tabla de archivos para ubicar el archivo...")

        # Paso 3: Simular posicionamiento del cabezal
        print("Moviendo el cabezal del disco...")
        time.sleep(1)  # Simula el tiempo necesario para mover el cabezal

        # Paso 4: Leer los bloques del archivo
        print("Leyendo bloques de datos del archivo...")
        time.sleep(1)  # Simula el tiempo necesario para leer los bloques
        print(f"Contenido del archivo '{file_name}': {file_data}")

        # Paso 5: Finalización
        print("Lectura completada.\n")


# Simulación de un sistema de archivos
file_system = {
    "documento.txt": "Este es el contenido del archivo 'documento.txt'.",
    "reporte.csv": "ID,Nombre,Edad\n1,Juan,25\n2,Ana,30",
    "imagen.jpg": "[Datos binarios de la imagen...]"
}

# Simulación de lectura
if __name__ == "__main__":
    disk = DiskSimulator(file_system)

    # Leer archivos
    disk.read_file("documento.txt")
    disk.read_file("imagen.jpg")
    disk.read_file("archivo_inexistente.txt")
```

### 2. Programa que realiza operaciones de entrada/salida asíncronas con archivos  

Este programa utiliza las bibliotecas de Python como `asyncio` y `aiofiles` para realizar operaciones de entrada/salida asíncronas.  

#### **Código en Python**

```python
import asyncio
import aiofiles

async def read_file_async(file_path):
    """Lee un archivo de forma asíncrona y muestra su contenido."""
    try:
        async with aiofiles.open(file_path, mode='r') as file:
            print(f"Leyendo el archivo '{file_path}' de forma asíncrona...")
            content = await file.read()
            print(f"Contenido de '{file_path}':\n{content}")
    except FileNotFoundError:
        print(f"[Error] El archivo '{file_path}' no existe.")
    except Exception as e:
        print(f"[Error] Ocurrió un problema al leer el archivo: {e}")

async def write_file_async(file_path, content):
    """Escribe datos en un archivo de forma asíncrona."""
    try:
        async with aiofiles.open(file_path, mode='w') as file:
            print(f"Escribiendo datos en el archivo '{file_path}' de forma asíncrona...")
            await file.write(content)
            print(f"Datos escritos en '{file_path}'.")
    except Exception as e:
        print(f"[Error] Ocurrió un problema al escribir en el archivo: {e}")

async def main():
    """Función principal para ejecutar operaciones de entrada/salida asíncronas."""
    # Escribir archivos
    await write_file_async("archivo1.txt", "Este es el contenido del archivo 1.")
    await write_file_async("archivo2.txt", "Contenido del archivo 2.\nOtra línea de texto.")

    # Leer archivos
    await read_file_async("archivo1.txt")
    await read_file_async("archivo2.txt")
    await read_file_async("archivo_inexistente.txt")  # Intento de lectura de un archivo inexistente

# Ejecutar el programa principal
if __name__ == "__main__":
    asyncio.run(main())
```
#### **Explicación del código**

1. **Operaciones Asíncronas:**
   - Se utiliza `aiofiles` para abrir y manejar archivos de forma asíncrona.
   - `await` asegura que la lectura o escritura no bloquee el programa principal.  

2. **Lectura y Escritura:**
   - **`read_file_async`**: Lee un archivo línea por línea y muestra el contenido.  
   - **`write_file_async`**: Escribe datos en un archivo de manera eficiente.  

3. **Ejecución Concurrente:**
   - Con `asyncio.run(main())`, las tareas de lectura/escritura se ejecutan de manera no bloqueante.  

#### **Ejemplo de Salida**

```plaintext
Escribiendo datos en el archivo 'archivo1.txt' de forma asíncrona...
Datos escritos en 'archivo1.txt'.
Escribiendo datos en el archivo 'archivo2.txt' de forma asíncrona...
Datos escritos en 'archivo2.txt'.
Leyendo el archivo 'archivo1.txt' de forma asíncrona...
Contenido de 'archivo1.txt':
Este es el contenido del archivo 1.
Leyendo el archivo 'archivo2.txt' de forma asíncrona...
Contenido de 'archivo2.txt':
Contenido del archivo 2.
Otra línea de texto.
[Error] El archivo 'archivo_inexistente.txt' no existe.
```