### Examen 1
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Comandos de Linux, Virtualización  
**Correo:** daniel.barragan at correo.icesi.edu.co  

**Estudiente:** Ruben Dario Ceballos  
**Código:** A00054636  
**Correo:** rubendcm9708@gmail.com  

### Objetivos
* Conocer y emplear comandos de Linux para la realización de tareas administrativas
* Virtualizar un sistema operativo
* Conocer y emplear capacidades de CentOS7 para la vitualización

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7

### Descripción
El primer parcial del curso sistemas operativos trata sobre el manejo de los comandos de Linux, virtualización y el uso de las características de CentOS7

### Actividades
1. Incluir nombre, código (5%)
2. Ortografía y redacción cuando sea necesario (5%)
3. Resuelva los siguienes retos de la página https://cmdchallenge.com y presente la solución a cada uno de ellos a través de un ejemplo práctico en CentOS7. Presente capturas de pantalla relevantes como evidencias de lo realizado (20%)

  * sum_all_numbers: Para sumar todos los números de un archivo suma.txt, lo que hacemos es usar el comando **jq**,al cuál podemos pasarle como parámetros operaciones arítmeticas, que realizará sobre los números del archivo. Este comando lo obtenemos ejectuando las siguientes líneas: 
wget -O jq https://github.com/stedolan/jq/releases/download/jq-1.5/jq-linux64; hacemos una copia de los recursos en github.
chmod +x ./jq; damos permisos de ejecución.
cp jq /usr/bin; copiamos en bin, que contiene todos los comandos ejecutables del sistema.  
Con esto ya podremos hacer el reto.

    
  ![][1]  
    
  * replace_spaces_in_filenames: Para reemplazar los espacios con puntos, usamos el comando **tr**, y pasamos como parámetros ' ', que representa los espacios en los nombres de los archivos del directorio que estamos, seguido de '.' , que será el caracter que reemplazará los espacios.
    
  ![][2]
    
  * reverse_readme: Para editar un archivo txt, haciendo que las líneas se inviertan de abajo hacia arriba, usamos el comando **tac** y le pasamos como parámetro el archivo.  
    
  ![][3] 
    
  * remove_duplicated_lines: **awk** es un editor y lector de archivos como lo es cat y vi. Al comando para llamar este editor se le pueden pasar como parámetros diferentes métodos, como lo es 'unique[$0]', que elimina todas las líneas repetidas de un archivo.  
    
  ![][4]  
    
  * disp_table: Podemos convertir un archivo de texto en una tabla, para ello especificamos las columnas de nuestra tabla serárandolas por comas, y haciendo saltos de línea para especificar una siguiente fila. Todo esto con el comando **column**.  
    
  ![][5]  
  
4. Realice un script que cumpla las condiciones que se describen a continuación. Presente capturas de pantalla relevantes como evidencias del funcionamiento (30%)
  * El usuario gutenberg debe existir en el sistema operativo
  * El script se debe ejecutar cada 5 minutos, consulte el manual de crontab
  * EL script debe descargar un libro del proyecto https://www.gutenberg.org/ en el directorio /home/gutenberg/mybooks
  * Si ya existe un libro en el directorio mybooks, debe ser reemplazado  
  
  Para cumpliar los requerimientos, primero creamos el usuario gutenberg, que en sus directorios contendrá la ruta /home/gutenberg/documentos/libreria, donde estarán almacenado el libro que se actualiza, y el script descarga.sh.
  
   ![][6]  
  
  El script se encarga de bajar el libro de www.gutenberg.org. Esta página almacena los libros en texto plano de la siguiente manera: 'http://www.gutenberg.org/cache/epub/1000/pg1000.txt', donde con editar el número 1000 podremos acceder a gran cantidad de libros. Para poder descargar un libro aleatorio descomponemos el URL en 4 partes, generamos un número random entre 450 y 1450. Finalmente, eliminamos el libro viejo para evitar problemas con sobreescrituras y descargamos el .txt usando el enlace generado.
   
   ![][7]  
 
 Para hacer que nuestro script se ejecute de manera periódica (cada 5 minutos), usaremos crontab, que es un archivo en el cuál podemos especificar que scripts o .exe queremos que se ejecuten, además, nos da la posibilidad de decidir cada cuanto tiempo, desde minutos hasta meses. Para abrir el archivo de crontab, ejecutamos **crontab -e**.
 
 Los parámetros de crontab se manejan de la siguiente manera, contiene 5 campos de tiempo, el primero son minutos de la hora y como queremos que se ejecute cada 5 minutos, ingresamos los tiempos separados por comas. Con esto, nos cercioramos que activará el script en los minutos 0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50 y 55 de cada hora. Los siguientes campos son: Hora del día, día del mes, mes del año y día de la semana. El último parámetro, es la ruta del script que queremos que se ejecute.
      
   ![][8]
   
 La forma de crontab de notificarnos que se ejecuto un archivo, es dejarnos correo en un directorio de correos ubicado en /var/spool/mail/root, el cual informa que se ejecuto el script descarga.sh.
   
   ![][9]
   
 Finalmente, podemos leer o trabajar sobre los libros que descargó el sistema.  
   
   ![][10]   
   
   
5. Describa el funcionamiento del código fuente rickroll.c del repositorio de github https://github.com/jvns/kernel-module-fun. Muestre el funcionamiento al compilar el código y cargarlo como un módulo del kernel a través de un video que deberá cargar en Youtube e incluir el enlace en el informe (30%). Se recomienda emplear el sistema operativo Ubuntu con interfaz gráfica para esta prueba.


Este código lo que hace es hacerle un rickroll al usuario, forzando a reproducir la canción Never gonna give you up del artista Rick Astley cada vez que se quiera reproducir un archivo con extensión .mp3 . A continuación, explicaremos a fondo como se logra esto.  

Primero, importamos todas las librerias necesarias para hacer las modificaciones en los llamados del sistema, las cuales son:  

#include <linux/module.h>  
#include <linux/kernel.h>  
#include <linux/init.h>  
#include <linux/syscalls.h>  
#include <linux/string.h>  

A continuación, se crean las variables que usaremos en el programa, que son: la ruta donde se encuentra almacenada la canción Never gonna give you up (rickroll_filename); las funciones de habilitar y deshabilitar la protección de escritura de la tabla de sys_call (DISABLE_WRITE_PROTECTION y ENABLE_WRITE_PROTECTION) y el método original de sys_open. Adicionalmente, se guardan módulos de parámetros.  

static char rickroll_filename = "/home/bork/media/music/Rick Astley - Never Gonna Give You Up.mp3";  
module_param(rickroll_filename, charp, S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH);  
MODULE_PARM_DESC(rickroll_filename, "The location of the rick roll file");  
#define DISABLE_WRITE_PROTECTION (write_cr0(read_cr0() & (~ 0x10000)))  
#define ENABLE_WRITE_PROTECTION (write_cr0(read_cr0() | 0x10000))  

Después, definimos el método '__init' 

![][11]  





6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam1 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)  

### Referencias
* https://cmdchallenge.com  
* https://www.gutenberg.org  
* https://github.com/jvns/kernel-module-fun/blob/master/rickroll.c  
* https://www.youtube.com/watch?v=efEZZZf_nTc 
* https://www.youtube.com/watch?v=APmoU74ZsG4  
* 


[1]: images/p3_suma.png
[2]: images/p3_spaces.png  
[3]: images/p3_reverse.png  
[4]: images/p3_repetidas.png  
[5]: images/p3_columnas.png  
[6]: images/p4_dir.png  
[7]: images/p4_script.png  
[8]: images/p4_crontab.png  
[9]: images/p4_correo.png  
[10]: images/p4_libro.png  
[11]: images/init.png  

