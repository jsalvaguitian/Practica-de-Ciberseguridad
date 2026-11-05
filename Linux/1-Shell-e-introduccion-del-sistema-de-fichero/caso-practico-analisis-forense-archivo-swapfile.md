# CASO PRÁCTICO: Análisis forense del archivo swapfile en Linux

## Descripción:

En esta practica realice un analisis forense del archivo de intercambio (*swapfile*) en un entorno de Linux, con el objetivo de comprender que sucede cuando un proceso necesita mas memoria RAM de la que tiene disponible y como parte de su informacion puede terminar almacenada en el disco.

Para realizar este ejercicio practico, utilce el editor de texto *Emacs* y abri el archivo `/etc/passwd`. Luego provoque una situacion de presion de memoria sobre el proceso Emacs, con el fin de obtener una copia del archivo de intercambio swapfile.
Finalmente, copie el archivo swapfile de la maquina virtual a mi sistema anfitrion y utilice Autopsy para examinar su contenido y buscar rastros de informacion.

## Objetivos:
- Comprender qué es la memoria swap y qué función cumple en Linux.

- Observar el comportamiento de un proceso cuando tiene limitada la memoria disponible.

- Comprender cómo puede producirse el intercambio de páginas de memoria entre la RAM y el disco.

- Practicar la copia de archivos desde una máquina virtual hacia el sistema anfitrión.

- Comprender como pueden quedar rastros de informacion en el archivo de intercambio.

- Utilizar Autopsy para buscar cadenas de texto y posibles eivedencias digitales.

- Familiarizarme con un procedimiento básico de análisis forense digital.

## Entorno y herramientas:
- **Sistema operativo** : Ubuntu Linux
- **Virtualizacion** : Oracle VirtualBox / VM Ware
- **Editor de texto** : Emacs
- **Herramienta forense** : Autopsy
- **Evidencia analizada**: archivo de intercambio swapfile.

## Conceptos fundamentales a tener en cuenta:

### Que es la memoria swap?
La memoria swap es una porción del disco duro o unidad de almacenamiento (HDD o SSD) que el sistema operativo utiliza como memoria virtual para ampliar la memoria RAM física. Dependiendo la comfiguracion del sistema, Linux puede trasladar paginas de memoria RAM al area de intercambio para gestionar la memoria disponible.
La memoria swap se utiliza para aliviar la memoria, evitar caidas del sistema y para casos de hibernacion.

### Que es el archivo `/etc/passwd`?
Es un archivo de linux que contiene informacion sobre cuentas de usuario locales, como sus nombres, identificadores y otros datos relacionados con las cuentas.

*NOTA*: `/etc/passwd` normalmente no almacena las contraseñas en texto plano. Los campos de sus registros contienen información de las cuentas y, generalmente, una referencia al archivo donde se guardan los hashes de contraseñas.

## Por que es importante el swapfile desde el punto de vista forense?
Desde el punto de vista de la informática forense, considero que el archivo de intercambio swapfile es importante, ya que puede conservar rastros de datos que estuvieron previamente almacenados en la memoria RAM.

Estos rastros pueden resultar útiles durante una investigación, debido a que ciertos fragmentos de información pueden permanecer almacenados en disco incluso después de cerrar una aplicación. Por este motivo, el archivo de intercambio puede conformar una fuente complementaria de evidencias durante un análisis forense digital.

Ahora bien, la recuperación de información no te la garantizo, ya que dependera de diversos factores, como el comportamiento del sistema, la presión de memoria, la configuración y otros aspectos. Además, la presencia de determinados datos en el archivo no demuestra por sí sola que un usuario haya realizado una acción específica.

### ¿Qué datos sensibles puede contener?

El archivo swapfile puede contener fragmentos de información sensible que estuvieron previamente en la memoria RAM, por ejemplo:

| Tipo de información | Ejemplos |
|---|---|
| **Credenciales** | Contraseñas ingresadas, fragmentos de credenciales y datos de autenticación. |
| **Información de usuarios** | Nombres de usuario, direcciones de correo y datos de cuentas. |
| **Navegación web** | URLs, fragmentos de páginas visitadas, búsquedas y contenido de formularios. |
| **Mensajes privados** | Fragmentos de correos electrónicos, chats y mensajes. |
| **Documentos** | Texto de archivos abiertos con editores como Emacs, LibreOffice o lectores PDF. |
| **Información de red** | Direcciones IP, nombres de dominio y otros datos de conexiones. |
| **Claves criptográficas** | Claves privadas, claves SSH o material criptográfico que estuvo en memoria. |
| **Datos personales** | Números de teléfono, información de contacto y otros datos ingresados en aplicaciones. |


Estos tipos de información representan posibles artefactos de interés en investigaciones de informática forense y análisis de memoria.

### A considerar:

*Es importante tener en cuenta que el archivo de intercambio no siempre contiene todos estos tipos de información, ya que esto depende de los datos que hayan utilizado las aplicaciones y de la gestión de memoria realizada por el sistema.*

*Por eso, es necesario **evaluar la información encontrada** teniendo en cuenta **el contexto** en el que se obtuvo y considerar **otras evidencias** para respaldar los resultados antes de **establecer conclusiones**.*

## Procedimiento:

### 1. Identificación del archivo de intercambio.
Primero, verificamos la ubicación y el tamaño del archivo swapfile.
Utilizando los siguientes comandos:

    tree / -L 1

    ls -lh /swapfile

Podemos observar que el swapfile se encuentra en la raiz del sistema y su tamaño o capacidad disponible es de 948 MB.

| <img src="screenshots/1-ubicacion-swapfile.png" height="480"> |
|:------------------------------------------------------:|
| *Captura 1 — Identificación del swap*           |

***Aclaracion**: Para poder utilizar el comando `tree` se requiere instalarlo ejecutando el siguiente comando porque por defecto no viene instalado en el S.O:*

    sudo apt install tree

Tree es una herramienta que me permite visualizar la estructura de carpetas y archivos en forma de árbol.

### 2. Apertura del archivo /etc/passwd con Emacs.
Segundo, abrimos el archivo `/etc/passwd` mediante el editor *Emacs* (o en cualquier editor de texto).

Utilizando el siguiente comando:

    emacs /etc/passwd

Este archivo contiene informacion de todos los usuarios que se encuentran alojados en nuestra maquina linux. Con lo cual al abrirlo con Emacs, el programa usará la memoria RAM para trabajar con su contenido.
El proceso Emacs tiene una cantidad de ram asociada que puede consumir pero por el momento no llegó al limite y por lo tanto  podemos observar que carga este archivo
sin ningún problema.

| <img src="screenshots/2-archivo-passwd-emacs.png" height="450"> |
|:------------------------------------------------------:|
| *Captura 2 — Archivo /etc/passwd abierto en Emacs*          |

### 3. Identificación del proceso Emacs.
En este paso, temporalmente suspenderemos el proceso mediante Ctrl + Z, o sea lo enviamos a segundo plano y buscaremos el PID de Emacs en la lista de procesos en la terminal. Usando el siguiente comando:

    ps

Necesitamos la informacion del PID de Emacs, el identificador del proceso, para aplicar a Emacs un limite de recurso y así preparar nuestro escenario de prueba.

| <img src="screenshots/3-PID-emacs.png" height="300"> |
|:------------------------------------------------------:|
| *Captura 3 — Identificación del PID*        |


### 4. Limitación del espacio de direcciones virtuales.
Ahora copiamos el PID de Emacs y lo utilizaremos en el siguiente comando:

    prlimit --as=1000 --pid [PID]

En este comando, [PID] debe ser reemplazado por el identificador real del proceso Emacs.

La opción --as establece un límite para el espacio de direcciones virtuales del proceso. 
El objetivo del procedimiento es limitar los recursos disponibles para Emacs y provocar una situación en la que el programa no pueda continuar trabajando normalmente.

| <img src="screenshots/4-limitar-direcciones-virtuales-emacs.png" height="90"> |
|:------------------------------------------------------:|
| *Captura 4 — Configuración del límite*       |



### 5. Reanudación del proceso y observación de su comportamiento.

Luego ejecutaremos el siguiente comando:

    fg

Este comando hace que el proceso Emacs vuelva a primer plano.

A continuación, intentamos continuar trabajando con Emacs, por ejemplo creando un nuevo archivo en la interfaz grafica de Emacs, con el fin de observar cómo responde al límite aplicado.
Emacs deja de responder temporalmente y posteriormente presenta un error relacionado con la asignación de memoria.

El objetivo de este paso, es observar el comportamiento del proceso ante la restricción de recursos.

| <img src="screenshots/5-emacs-fallando.png" height="450"> |
|:------------------------------------------------------:|
| *Captura 5 — Comportamiento de Emacs*       |



### 6. Copia del archivo de intercambio.

Después del experimento, vamos a obtener una copia del archivo swapfile para analizarla fuera del sistema en ejecución.
Utilizando el siguiente comando:

sudo cp /swapfile /home/[usuario]/Desktop/

En este comando, el [usuario] debe ser reemplazado por el nombre real del usuario de Ubuntu.

**Consideraciones:**
- La copia puede requerir espacio de almacenamiento y tiempo de procesamiento, dependiendo del tamaño del archivo.
- Verificar que la ruta del archivo de intercambio sea correcta.
- Conservar el archivo original sin modificaciones.
- Documentar el origen de la evidencia y trabajar sobre una copia.


Luego ejecutaremos el siguiente comando con el fin de modificar ciertos permisos para copiar este archivo fuera de nuestra maquina virtual 

    sudo chmod 755 /home/[usuario]/Desktop/swapfile

| <img src="screenshots/6-copiar-swapfile.png" height="100"> |
|:------------------------------------------------------:|
| *Captura 6 — Copia de la evidencia*       |



### 7. Transferencia de la evidencia al sistema anfitrión o host.
En este paso, transferimos la copia del archivo de intercambio, swapfile, desde Ubuntu, al sistema operativo anfitrión y vamos a crear una carpeta llamada **ubuntu_swap** para almacenar la evidencia en nuestro host principal.

Y así disponer de una copia de la evidencia para su posterior análisis con Autopsy.

| <img src="screenshots/7-evidencia-en-sistema-anfitrion.png" height="200"> |
|:------------------------------------------------------:|
| *Captura 7 — Archivo de intercambio en el sistema anfitrión*       |


### 8. Verificación de los datos de referencia.
Antes de buscar información en la evidencia, podemos consultar nuevamente el archivo /etc/passwd en Ubuntu.
Utilizando el comando

emacs /etc/passwd

En este archivo identificaremos algunos nombres de usuario que después podemos utilizarlo como palabras clave durante el análisis.
Para buscar más rápido nuestras palabras claves podemos usar el shorcut  `ctrl + s`, en mi caso el nombre de usuario `jesi-ubuntu`

En este paso nuestro objetivo sera conocer los datos de referencia que se intentarán localizar en el archivo de intercambio.

| <img src="screenshots/8-verificacion-palabres-claves.png" height="400"> |
|:------------------------------------------------------:|
| *Captura 8 — Contenido de /etc/passwd*       |


## Lista de comandos utilizados: 


## Conclusiones:
