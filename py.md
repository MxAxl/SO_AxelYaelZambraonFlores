# Sistema de Archivos
## Ejercicio 1: Concepto y Noción de Archivo Real y Virtual

### Concepto de Archivo Real
Un archivo real es una entidad tangible en un sistema de almacenamiento físico, como un disco duro, SSD o unidad externa. Representa datos organizados en bloques o sectores dentro del medio de almacenamiento. Los sistemas operativos interactúan con estos archivos a través de sistemas de archivos (como NTFS, FAT32, EXT4) que permiten su lectura, escritura y administración.

#### Características del Archivo Real:
- **Ubicación física:** Se almacena en una dirección específica dentro del medio de almacenamiento.
- **Gestión directa:** Requiere recursos físicos como espacio en el disco y tiempo de acceso.
- **Ejemplo:** Un documento de texto guardado como reporte.docx en una carpeta de tu computadora.

### Concepto de Archivo Virtual
Un archivo virtual es una representación lógica de datos que no necesariamente están asociados con un medio físico específico en un momento dado. Este tipo de archivo se genera dinámicamente en memoria o se presenta como una abstracción, a menudo gestionada por el sistema operativo o una aplicación.

#### Características del Archivo Virtual:
- **Ubicación lógica:** No reside físicamente en un medio de almacenamiento; su contenido puede ser generado en tiempo real.
- **Gestión flexible:** Puede basarse en recursos temporales, como la RAM o datos de otros sistemas.
- **Ejemplo:** Un archivo que representa datos en la nube o un archivo temporal en memoria, como un archivo de paginación.

### Diferencias entre Archivo Real y Archivo Virtual
| Aspecto              | Archivo Real                       | Archivo Virtual                    |
|----------------------|------------------------------------|------------------------------------|
| **Ubicación**        | Medio físico específico            | Representación lógica              |
| **Acceso**           | A través del sistema de archivos   | Generalmente gestionado en memoria |
| **Persistencia**     | Permanece hasta que se elimina     | Temporal, puede desaparecer tras su uso |
| **Ejemplo**          | Documento en disco                | Archivo temporal en RAM           |

### Ejemplos Prácticos en Sistemas Operativos

#### Manejo de Archivos Reales
- **Windows:** Los documentos almacenados en la carpeta "Documentos" o los archivos del sistema guardados en el disco C:.
- **Linux:** Archivos como /etc/passwd, que contiene información sobre los usuarios del sistema.
- **macOS:** Archivos multimedia en la carpeta "Descargas".

#### Manejo de Archivos Virtuales
- **Windows:** Archivos de paginación (swap) usados para expandir virtualmente la memoria RAM.
- **Linux:** Dispositivos virtuales montados en /dev, como `/dev/null (archivo especial que descarta datos escritos en él).
- **macOS:** La representación de unidades externas antes de que se conecten físicamente.

### Caso Práctico: Utilidad del Archivo Virtual
Un archivo virtual resulta más útil que un archivo real en sistemas que requieren alta velocidad y flexibilidad, como en:

**Sistema Operativo: Linux**
Caso práctico: /proc/cpuinfo
- **Contexto:** /proc/cpuinfo es un archivo virtual que proporciona información del procesador, como el modelo y la frecuencia.
- **Ventaja:** No ocupa espacio físico y siempre muestra datos actualizados del hardware. Esto es fundamental para tareas de monitoreo del sistema en tiempo real, algo que no podría lograrse de manera eficiente con un archivo real.

## Ejercicio 2: Componentes de un Sistema de Archivos

### Introducción
Un sistema de archivos es una estructura lógica que organiza y gestiona cómo se almacenan y recuperan los datos en un medio de almacenamiento. Los componentes principales del sistema de archivos garantizan que los datos sean accesibles de manera eficiente y segura, proporcionando funcionalidad y estructura para las operaciones del sistema operativo.

### Componentes Principales de un Sistema de Archivos

#### 1. **Estructura Jerárquica de Directorios**
La estructura de directorios define cómo se organizan los archivos en el sistema, permitiendo al usuario ubicarlos mediante rutas (absolutas o relativas).
- **Linux:** Utiliza una estructura jerárquica unificada que comienza en el directorio raíz /.
- **Windows:** Cada unidad de almacenamiento tiene su propio sistema de directorios, identificado con una letra, como C:\.

#### 2. **Metadatos**
Son los datos sobre los datos, describiendo atributos como nombre, tamaño, permisos, fecha de creación/modificación, etc.
- **EXT4 (Linux):** Los metadatos están optimizados mediante estructuras como "extents" para minimizar la fragmentación y mejorar el rendimiento.
- **NTFS (Windows):** Usa un archivo especial llamado Master File Table (MFT) que almacena metadatos, permitiendo acceso rápido.

#### 3. **Bloques de Datos**
Los bloques son las unidades mínimas de almacenamiento físico en el disco. Los archivos se dividen en bloques para ser escritos en el disco.
- **Linux (EXT4):** Los bloques pueden tener tamaños configurables (1 KB, 2 KB, 4 KB), mejorando la compatibilidad según el uso del sistema.
- **Windows (NTFS):** Maneja clústeres (agrupaciones de bloques) para mejorar la gestión del espacio.

#### 4. **Tablas de Asignación de Archivos**
Estas tablas rastrean qué bloques están ocupados y qué bloques están disponibles en el sistema de almacenamiento.
- **EXT4:** Usa un sistema de asignación basado en mapas de bits y bloques de grupo, optimizando la velocidad de localización.
- **NTFS:** Implementa una estructura más avanzada con índices B+ que reducen la latencia de búsqueda.

#### 5. **Journaling (Registro de Cambios)**
Es una característica de los sistemas de archivos modernos que asegura la consistencia del sistema tras errores o fallos, registrando operaciones antes de realizarlas.
- **EXT4:** Implementa journaling para metadatos y, opcionalmente, para datos completos.
- **NTFS:** Usa journaling a través del archivo $LogFile, proporcionando robustez ante fallos.

#### 6. **Permisos y Seguridad**
Los sistemas de archivos deben gestionar los accesos según permisos asignados a usuarios y grupos.
- **EXT4:** Basado en el modelo de permisos POSIX (lectura, escritura, ejecución).
- **NTFS:** Incluye un sistema avanzado de control de acceso mediante listas de control de acceso (ACLs).

---

### Cuadro Comparativo: EXT4 vs. NTFS

| Componente               | **EXT4 (Linux)**                                                              | **NTFS (Windows)**                                                             |
|--------------------------|------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **Estructura Jerárquica** | Directorio unificado desde /                                              | Directorios separados para cada unidad (e.g., C:\, D:\)                  |
| **Metadatos**            | Guardados en estructuras eficientes (como extents).                        | Manejados por la `MFT, almacenando todos los metadatos en un solo lugar.    |
| **Tamaño de Bloque**      | Configurable (1 KB, 2 KB, 4 KB).                                            | Usa clústeres dinámicos dependiendo del tamaño del disco.                    |
| **Asignación de Archivos**| Mapas de bits y bloques de grupo.                                           | Índices B+ y MFT para búsquedas más rápidas.                                 |
| **Journaling**            | Opcional, optimizado para metadatos.                                        | Siempre activo, garantizando alta integridad mediante $LogFile.            |
| **Permisos**              | Basados en POSIX; simples y efectivos.                                     | Basados en ACLs, ofreciendo controles más detallados.                        |
| **Compatibilidad**        | Limitado a sistemas Linux y algunos controladores en Windows.              | Compatible con casi todas las versiones de Windows.                          |
| **Fragmentación**         | Baja debido a extents.                                                     | Puede fragmentarse más fácilmente, aunque incluye un desfragmentador.        |

### Ventajas y Desventajas

#### Ventajas de EXT4
1. **Rendimiento Optimizado:** Ideal para sistemas Linux con uso intensivo de lectura/escritura.
2. **Fragmentación Baja:** El uso de extents minimiza la fragmentación, mejorando el rendimiento general.
3. **Journaling Opcional:** Permite mayor personalización según las necesidades del usuario.
4. **Eficiencia en Almacenamiento:** Los bloques configurables permiten optimizar el espacio según el tamaño del archivo.

#### Desventajas de EXT4
1. **Compatibilidad Limitada:** No es nativamente compatible con Windows.
2. **Falta de Soporte Avanzado:** Funciones como ACLs avanzadas son más limitadas.

#### Ventajas de NTFS
1. **Amplia Compatibilidad:** Usado en todos los sistemas Windows, con soporte también en Linux mediante controladores.
2. **Seguridad Robusta:** ACLs permiten un control detallado sobre quién puede acceder a qué.
3. **Journaling Permanente:** Protege los datos contra fallos inesperados del sistema.

#### Desventajas de NTFS
1. **Mayor Fragmentación:** Aunque incluye un desfragmentador, su diseño puede llevar a más fragmentación que EXT4.
2. **Sobrecarga de Recursos:** El sistema de journaling constante consume más recursos.

### Ejemplo de Casos de Uso

#### Cuando Usar EXT4:
- **Servidores Linux:** Su baja fragmentación y alto rendimiento lo hacen ideal para servidores que manejan grandes volúmenes de datos.
- **Dispositivos Integrados:** Debido a su configuración personalizable, es adecuado para sistemas con almacenamiento limitado.

#### Cuando Usar NTFS:
- **Sistemas Windows:** Su integración nativa garantiza un rendimiento óptimo en dispositivos Windows.
- **Entornos Mixtos:** Es ideal si necesitas compartir datos entre Windows y Linux.

## Ejercicio 3: Organización Lógica y Física de Archivos

### Organización Lógica: Árbol Jerárquico de Directorios
En la mayoría de los sistemas de archivos, los archivos y directorios están organizados de forma lógica en una estructura jerárquica similar a un árbol. 

#### Ejemplo de Árbol Jerárquico
```plaintext
/
├── home/
│   ├── usuario/
│   │   ├── documentos/
│   │   │   ├── proyecto.md
│   │   │   ├── informe.pdf
│   │   └── imágenes/
│   │       ├── foto1.jpg
│   │       ├── foto2.png
│   └── compartido/
├── var/
│   ├── log/
│   │   ├── system.log
│   │   ├── auth.log
├── etc/
│   ├── config.conf
└── dev/
    ├── sda
    ├── sdb
```

#### Explicación:
1. **Raíz (/):** Es el directorio base del sistema.
2. **Directorios Secundarios:** Contienen subdirectorios y archivos. Por ejemplo, /home/usuario/documentos/ contiene archivos como proyecto.md y informe.pdf.
3. **Archivos:** Son las hojas del árbol, mientras que los directorios son los nodos.

### Organización Física: Traducción de Dirección Lógica a Dirección Física
En el nivel físico, los datos no están organizados jerárquicamente como en el nivel lógico. En su lugar, están almacenados como bloques distribuidos en el disco. La traducción entre la organización lógica y física se realiza mediante el sistema de archivos.

#### Proceso de Traducción
1. **Nombre del Archivo (Lógico):** Cuando un usuario solicita abrir un archivo, proporciona su ruta lógica, como /home/usuario/documentos/proyecto.md.
2. **Sistema de Archivos:** El sistema convierte esta ruta en un identificador único llamado **inode** (en sistemas Linux) o una entrada en la **MFT** (en sistemas NTFS).
3. **Bloques Físicos:** El inode o la MFT contiene información sobre los bloques físicos donde está almacenado el archivo.
4. **Lectura Física:** El sistema accede a esos bloques físicos, los carga en memoria y los presenta al usuario.

#### Relación entre Estructuras
| **Estructura Lógica** | **Estructura Física** |
|-----------------------|-----------------------|
| Directorios           | Entradas en tablas de asignación. |
| Archivos              | Bloques de datos distribuidos en el disco. |
| Metadatos             | Inodes, MFT o estructuras similares. |

### Ejemplo Práctico de Almacenamiento Físico
Supongamos que tenemos el archivo proyecto.md de 8 KB almacenado en un disco con bloques de 4 KB.

#### Detalles:
- **Archivo Lógico:** /home/usuario/documentos/proyecto.md.
- **Bloques Físicos:** El archivo ocupa dos bloques (Bloque 5 y Bloque 10).

#### Paso a Paso:
1. **Ubicación Lógica:** El archivo está en la ruta /home/usuario/documentos/.
2. **Sistema de Archivos:**
   - Crea una entrada en el inode o la MFT que asocia el archivo con los bloques físicos.
   - Guarda los metadatos, como permisos, tamaño, y ubicaciones de bloques.
3. **Ubicación Física:**
   - Bloque 5: Contiene los primeros 4 KB del archivo.
   - Bloque 10: Contiene los últimos 4 KB del archivo.

#### Representación Gráfica
```plaintext
[Archivo Lógico]
proyecto.md --> Inode #25 --> [Bloque 5] [Bloque 10]

[Disco Físico]
Bloque 1  | Metadata de /home
Bloque 2  | Metadata de /usuario
Bloque 3  | Metadata de /documentos
Bloque 4  | Metadata de proyecto.md (Inode #25)
Bloque 5  | Contenido de proyecto.md (Primera parte)
Bloque 10 | Contenido de proyecto.md (Segunda parte)
```

## Ejercicio 4: Mecanismos de Acceso a los Archivos

### Definición de los Mecanismos de Acceso

1. **Acceso Secuencial:**
   - Los datos se leen o escriben de forma ordenada, uno tras otro, desde el inicio del archivo hasta el final.
   - Es ideal para procesar grandes volúmenes de datos lineales, como registros en un log.
   - **Ejemplo:** Leer todas las líneas de un archivo de texto en orden.

2. **Acceso Directo (o Aleatorio):**
   - Permite acceder directamente a una posición específica en el archivo sin necesidad de leer desde el principio.
   - Se utiliza cuando se conoce la posición exacta de los datos dentro del archivo.
   - **Ejemplo:** Buscar un registro específico en un archivo binario.

3. **Acceso Indexado:**
   - Utiliza una estructura adicional (índice) para localizar rápidamente las posiciones de los datos en el archivo.
   - Es útil en sistemas donde se realizan búsquedas frecuentes de datos específicos.
   - **Ejemplo:** Una base de datos con índices para acceder a registros por clave primaria.

### Pseudocódigos para los Mecanismos de Acceso

#### 1. Acceso Secuencial
```plaintext
# Pseudocódigo para leer un archivo secuencialmente
Abrir archivo en modo lectura
Mientras no se alcance el final del archivo:
    Leer línea actual
    Procesar línea
Cerrar archivo
```

**Ejemplo Práctico:**
```python
# Python - Leer un archivo secuencialmente
with open("datos.txt", "r") as archivo:
    for linea in archivo:
        print(linea.strip())
```

#### 2. Acceso Directo
```plaintext
# Pseudocódigo para acceso directo por posición
Abrir archivo en modo lectura
Ir a la posición deseada en el archivo
Leer datos desde esa posición
Procesar los datos
Cerrar archivo
```

**Ejemplo Práctico:**
```python
with open("datos.bin", "rb") as archivo:
    archivo.seek(20)  # Ir a la posición 20 (offset)
    datos = archivo.read(10)  # Leer 10 bytes desde esa posición
    print(datos)
```

#### 3. Acceso Indexado
```plaintext
# Pseudocódigo para acceso indexado
Cargar índice en memoria
Buscar en el índice la posición asociada al dato deseado
Abrir archivo en modo lectura
Ir a la posición indicada por el índice
Leer y procesar los datos
Cerrar archivo
```

**Ejemplo Práctico:**
```python
# Simular acceso indexado
indice = {"registro1": 0, "registro2": 50, "registro3": 100}  # Índice en memoria
registro_buscado = "registro2"

with open("datos.bin", "rb") as archivo:
    posicion = indice[registro_buscado]
    archivo.seek(posicion)  # Ir a la posición indicada
    datos = archivo.read(10)  # Leer 10 bytes
    print(datos)
```

### Comparación de los Mecanismos de Acceso

| **Aspecto**          | **Acceso Secuencial**                     | **Acceso Directo**                       | **Acceso Indexado**                     |
|----------------------|------------------------------------------|-----------------------------------------|-----------------------------------------|
| **Velocidad**        | Lento para registros específicos         | Rápido si se conoce la posición         | Muy rápido con un índice bien diseñado  |
| **Facilidad de Uso** | Simple de implementar                   | Requiere conocimiento del offset        | Más complejo por la gestión del índice  |
| **Espacio Adicional**| No requiere espacio extra               | No requiere espacio extra               | Requiere almacenamiento para el índice  |
| **Caso de Uso**      | Procesamiento completo de datos (logs)  | Lectura de datos específicos (binarios) | Bases de datos o búsquedas frecuentes   |

### Ejemplos de Casos de Uso

1. **Acceso Secuencial:**
   - Procesar registros en un archivo de log para análisis de eventos.
   - Leer un archivo CSV línea por línea.

2. **Acceso Directo:**
   - Recuperar un registro específico en un archivo binario grande.
   - Editar un archivo de configuración en una posición específica.

3. **Acceso Indexado:**
   - Consultar datos en una base de datos relacional.
   - Buscar una palabra en un índice invertido en motores de búsqueda.

Aquí tienes el desarrollo detallado del **Ejercicio 5: Modelo jerárquico y mecanismos de recuperación en caso de falla**.

## Ejercicio 5: Modelo Jerárquico y Mecanismos de Recuperación en Caso de Falla

### Modelo Jerárquico para un Sistema de Archivos

El sistema está diseñado con tres niveles principales:

![alt text](imagen-1.png)

#### Descripción:
1. **Raíz (/)**: Directorio principal que contiene subdirectorios importantes.
2. **Usuarios (/usuarios/)**: Contiene datos personales organizados por usuario.
3. **Sistema (/sistema/)**: Contiene logs y configuraciones del sistema operativo.
4. **Respaldos (/respaldos/)**: Almacena copias de seguridad de directorios críticos.

### Simulación de una Falla

#### Escenario:
El directorio /usuarios/axel/documentos/ es dañado debido a un fallo del sistema de archivos. Los archivos almacenados dentro (e.g., proyecto1.md e informe_final.pdf) ya no están accesibles.

#### Pasos para la Recuperación

1. **Verificación Inicial:**
   - Usar una herramienta como fsck (en Linux) o chkdsk (en Windows) para verificar y reparar errores en el sistema de archivos:
     ```bash
     fsck /dev/sdX
     ```
     Esto busca corregir errores menores como referencias corruptas.

2. **Recuperación de Directorios Perdidos:**
   - Si el sistema de archivos tiene un espacio reservado para archivos huérfanos, buscar allí:
     ```bash
     ls -l /lost+found/
     ```
   - Mover los archivos recuperados a su ubicación original.

3. **Uso de Copias de Seguridad:**
   - Restaurar el directorio desde la última copia de seguridad almacenada en /respaldos/usuarios_axel/:
     ```bash
     cp -r /respaldos/usuarios_axel/documentos/ /usuarios/axel/
     ```

4. **Recuperación con Herramientas Especializadas:**
   - Si los pasos anteriores fallan, emplear herramientas de recuperación de datos como:
     - **TestDisk**: Para reconstruir estructuras de partición.
     - **Photorec**: Para recuperar archivos específicos.

### Técnicas y Herramientas de Respaldo

#### 1. Copias de Seguridad Programadas
- **Herramientas:** 
  - **rsync (Linux):** Sincroniza directorios y realiza copias incrementales.
    ```bash
    rsync -av --delete /usuarios/ /respaldos/usuarios/
    ```
  - **Backup and Restore (Windows):** Proporciona copias programadas y restauración fácil.
- **Frecuencia:** 
  - Realizar respaldos diarios para directorios críticos como /usuarios/.

#### 2. Sistemas de Versionado
- Implementar sistemas como **Git** para archivos que requieren un historial de cambios, como documentos de texto o proyectos.
  ```bash
  git init
  git add proyecto1.md
  git commit -m "Primer respaldo"
  ```

#### 3. Snapshots
- Utilizar sistemas de archivos como ZFS o Btrfs que permiten tomar snapshots del sistema:
  ```bash
  zfs snapshot pool1@snapshot1
  ```

#### 4. Almacenamiento en la Nube
- **Servicios:** Google Drive, OneDrive, o soluciones empresariales como AWS S3.
- **Ventaja:** Ofrecen respaldo en ubicaciones geográficamente distintas, minimizando riesgos por desastres físicos.

### Comparación de Técnicas de Respaldo

| **Método**                | **Ventajas**                                      | **Desventajas**                                  |
|---------------------------|--------------------------------------------------|-------------------------------------------------|
| **Copias de Seguridad**   | Simples, automatizables, y económicas            | Requieren espacio físico para almacenamiento    |
| **Versionado**            | Control de cambios, útil para desarrollo         | No adecuado para grandes volúmenes de datos     |
| **Snapshots**             | Recuperación rápida de estados previos           | Compatible solo con sistemas específicos        |
| **Almacenamiento en la Nube** | Respaldo remoto, accesible desde cualquier lugar | Requiere conexión constante y suscripción       |


