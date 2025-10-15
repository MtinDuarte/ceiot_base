# Alumno: Martin Duarte


# Sistema víctima

Los sistemas víctimas son servidores Web, principalmente Nginx, que gestionan la infraestructura de páginas de comercio electrónico.

- El foco está en los servidores que procesan pagos en sus propias plataformas.
- Nginx actúa como front-end, manejando el tráfico HTTP de datos de los clientes (como credenciales de acceso y formularios de pago).
- Se apunta a las plataformas que solicitan información financiera sensible (números de tarjeta de crédito, códigos de seguridad) y credenciales de acceso.

# Objetivo del ataque

Obtención de **datos/información sensible de personas** (clientes), como cuentas bancarias o datos de tarjetas de crédito que pueden ser utilizadas en otros sitios por el atacante como por ejemplo:
    
- Solicitando prestamos. 
- Solicitudes de servicios como infraestructura de sistemas.

## Reconnaissance

> Resumen: Se hace un **escaneo de puertos**, se intentar **determinar la versión de Nginx** o del software de la aplicación para buscar una vulnerabilidad de día cero o CVE. Reconocimiento sobre **redes sociales** y **formularios de pago**.

Comienzo haciendo las siguientes acciones:

- (**T1595**) - Active Scanning: con el propósito de identificar puertos comunes de comunicación (21:FTP, 22:SSH, 3389 Remote Desktop) en los que pueda encontrar debilidades, encontré un aplicativo web:

    https://dnschecker.org/port-scanner.php


- De manera de poder buscar las debilidades de nginx encontré esta página que resume los avisos de seguridad de Nginx

    https://nginx.org/en/security_advisories.html


- **(T1593.001)** Search Open Websites/Domains:
    - **(T1593.001) - Social Media**: Redes sociales que se encuentren en el sitio, búsqueda de seguidores, empleados o incluso dueños. 

- **(T1597) - Search Closed Sources**:
    - **(T1597.002)** Purchase Technical Data: con el objetivo de conocer el circuito de compra y poder obtener cuentas a las cuales se gira el dinero, CUIT, CUIL, entidades bancarias haría una compra de algún elemento de la tienda.

## Weaponization
> Resumen: Construcción de un Skimmer de software que se ubicará del lado del servidor.

**Construcción del Skimmer**: El arma es un módulo de código compilado en C, diseñado para inyectarse directamente en el servidor web Nginx. Este módulo no inyecta JavaScript, sino que intercepta los datos sensibles en el nivel del servidor, antes de que sean procesados por la aplicación backend. 


**Contenido del Skimmer:**

1. **Compilación**: Se compila el código fuente del skimmer (módulo de Nginx) para el sistema operativo y arquitectura del servidor víctima (ej., Linux x64), creando la biblioteca dinámica (.so) que Nginx cargará como legítima.

2. **Refinamiento de Expresiones Regulares (Regex)**: Se utilizan los datos obtenidos en el Reconocimiento (nombres de campos de formulario de login y formulario de pago) para refinar el skimmer. Esto asegura que solo se busquen y registren los patrones de datos de interés y sensibles en las peticiones POST (ej., números de tarjeta de crédito, CVV, contraseñas, tokens de sesión y direcciones físicas).

## Delivery

> Resumen: Delivery por **phishing** mediante links y **ads** con objetivo de capturar datos de ingreso.

- **[T1189]  - Drive-by Compromise:** 
    - **[T1583.008]**: El objetivo sería infectar el software de un desarrollador o administrador (mediante una vulnerabilidad de día cero en su navegador), logrando acceso remoto a su máquina de trabajo, la cual puede tener credenciales de SSH almacenadas.

- **[T1566] - Phishing:** Se envían varios correos electrónicos con la intención de que los empleados/clientes/conocidos del local hagan click simulando el origen ser la página atacada, ofreciendo acciones como nuevas funcionalidades o actualizaciones disponibles.

    El objetivo no es que el cliente compre algo, sino que un administrador o desarrollador haga clic en un enlace falso para robar sus credenciales del panel de control o de SSH.


## Exploitation

> Resumen: Se **ASUME** que se robaron datos/credenciales administrador-root satisfactoriamente de la fase *delivery* y puertos abiertos *ssh* de la fase de reconocimiento. 

1. **Acceso Remoto**: Se inicia sesión en el servidor web utilizando las credenciales comprometidas.
2. **Carga del Arma**: Una vez dentro del sistema operativo del servidor, el atacante ejecuta la orden para cargar el archivo del skimmer al directorio de destino (ej., /usr/lib/nginx/modules/).


## Installation

> Resumen: **Incorporación del skimmer** en ejecución ordinaria de Nginx. Creación de nuevas credenciales *ssh* en caso de que cambien las robadas.

1. **Modificación de la Configuración de Nginx**: Se localiza el archivo de configuración principal (/etc/nginx/nginx.conf) y añade la directiva *"load_module"* para que Nginx cargue el skimmer al inicio.

```nginx
load_module modules/skimmer_module.so;
```

2. **Validación y Reinicio**: Se reinicia el servicio Nginx de manera de forzar al servidor a cargar el módulo del skimmer.

3. **Persistencia del Acceso**: Para asegurar que el acceso se mantenga incluso si las credenciales robadas cambian, se crea una clave SSH pública en el archivo authorized_keys de un usuario de bajo privilegio.


## Command & Control

> Resumen: Generar una forma de que el skimmer comunique que sigue activo, extraer datos robados, limpiar/borrar archivo donde se almacenaron los datos.

- **Aviso de actividad**: El módulo de Nginx instalado realiza una petición HTTP a un dominio externo del atacante. El objetivo no es robar datos, sino confirmar que el skimmer sigue activo y en funcionamiento.

- **Canal de Extracción de Datos**: Se utiliza un canal HTTP regular para enviar una petición con un encabezado de autenticación secreto y único:

    (ej., Authorization: Basic Z2ltbWU6ZGFsb290). 

    Esta clave actúa como una "llave" que el módulo de Nginx reconoce, lo que activa la función de enviar el archivo de log (/dev/shm/.secrets) a un servidor propio.

- **Limpieza Remota**: Una vez que el archivo de log es extraído por el C&C, el módulo de Nginx lo borra inmediatamente, cubriendo el rastro del robo de datos.


## Actions on Objetives

> Resumen: El atacante logra la monetización a largo plazo mediante el uso o la venta de la información sensible.

- **Robo de Datos Continuo**: El skimmer de Nginx registra de forma pasiva y continua todas las peticiones HTTP POST que contienen credenciales de login, números de tarjeta de crédito, CVV y otra información personal de todos los clientes.

- **Monetización (Venta o Uso)**: Los datos robados se clasifican y se utilizan para el objetivo final del ataque (Robo de Identidad Financiera):

    - **Fraude Directo**: Uso de las tarjetas de crédito robadas para realizar compras o solicitar préstamos en nombre de la víctima.

    - **Venta de datos**: Venta de los bloques de credenciales bancarias y tarjetas a otros actores maliciosos para obtener beneficios monetarios.

    - **Acceso a Cuentas**: Uso de las credenciales de login para acceder a cuentas de clientes y usar los métodos de pago almacenados.
