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
- **Virtualizacion** : Oracle VirtualBox.
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

## Lista de comandos utilizados: 


## Conclusiones:
