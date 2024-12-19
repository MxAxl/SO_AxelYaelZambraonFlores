# Administracion de memoria
## 1.Memoria con particiones fijas
### Definición de Estructuras

```c
typedef struct {
    int tamanio;
    int id_particion;
    int id_proceso; // -1 si la partición está libre
} Particion;
```

Define una estructura para representar una partición, que incluye:
- tamanio: El tamaño de la partición.
- id_particion: Identificador único de la partición.
- id_proceso: ID del proceso asignado a la partición o -1 si está libre.

### Funciones Principales

#### Definir Particiones

```c
void definir_particiones(Particion *particiones, int *num_particiones, int tamanio_memoria)
```

Esta función permite definir las particiones en la memoria.
- **Parámetros**:
  - particiones: Array de particiones.
  - num_particiones: Puntero para almacenar el número de particiones definidas.
  - tamanio_memoria: Tamaño total de la memoria disponible.

#### Asignar Proceso

```c
void asignar_proceso(Particion *particiones, int num_particiones, int id_proceso, int tamanio_proceso)
```

Asigna un proceso a la primera partición disponible que cumpla los requisitos de tamaño.
- **Parámetros**:
  - particiones: Array de particiones.
  - num_particiones: Cantidad de particiones.
  - id_proceso: ID del proceso a asignar.
  - tamanio_proceso: Tamaño del proceso.
- **Salida**: Imprime un mensaje indicando si la asignación fue exitosa o fallida.

#### Liberar Proceso

```c
void liberar_proceso(Particion *particiones, int num_particiones, int id_proceso)
```

Libera un proceso de una partición si está asignado.
- **Parámetros**:
  - particiones: Array de particiones.
  - num_particiones: Cantidad de particiones.
  - id_proceso: ID del proceso a liberar.
- **Salida**: Imprime un mensaje confirmando la liberación o informando que el proceso no está asignado.

#### Mostrar Estado

```c
void mostrar_estado(Particion *particiones, int num_particiones)
```

Muestra el estado actual de la memoria.
- **Parámetros**:
  - particiones: Array de particiones.
  - num_particiones: Cantidad de particiones.
- **Salida**: Lista las particiones indicando si están libres o asignadas, junto con los detalles.

### Lógica del Programa Principal

#### Inicio

```c
printf("Simulación de administración de memoria con particiones fijas\n");
```

Imprime un mensaje de bienvenida al usuario indicando el propósito del programa.

```c
scanf("%d", &tamanio_memoria);
```

Solicita al usuario el tamaño total de la memoria y lo almacena en tamanio_memoria.

#### Bucle del Menú

```c
while (true)
```
Inicia un bucle infinito que presenta el menú de opciones al usuario.

```c
int opcion;
scanf("%d", &opcion);
```

Lee la opción seleccionada por el usuario.

#### Opciones del Menú

##### Asignar Proceso

```c
if (opcion == 1)
```

Comprueba si el usuario seleccionó la opción de asignar un proceso. Solicita el ID y tamaño del proceso e intenta asignarlo.

##### Liberar Proceso

```c
else if (opcion == 2)
```

Comprueba si el usuario seleccionó la opción de liberar un proceso. Solicita el ID del proceso e intenta liberarlo.

##### Mostrar Estado

```c
else if (opcion == 3)
```

Muestra el estado actual de la memoria.

##### Salir

```c
else if (opcion == 4)
```

Termina la ejecución del programa con un mensaje de despedida.

#### Finalizar

```c
return 0;
```
Indica el final del programa y devuelve 0 como código de salida exitoso.

