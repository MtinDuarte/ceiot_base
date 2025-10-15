
# Alumno: Martin Duarte

## Enunciado

Desarrollar la defensa en función del ataque planteado en orden inverso, mencionar una medida de detección y una de mitigación, sólo lo más importante, considerar recursos limitados. No es una respuesta a un incidente, hay que detectar el ataque independientemente de la etapa.


## Resolución

- Actions on Objectives
    > **Resumen**: Incorporar formularios de envío de reportes de clientes. Desarrollar API de consultas de contraseña vulnerada. 

    - **Detección**: Se detectan emails de clientes con reportes de substracción de credenciales y usos de métodos de pago inusuales.
    
    - **Mitigación**: 
        - **Generar API de contraseñas vulneradas**: Generar API para aviso de contraseña vulnerada (por ejemplo *HaveIbeenpwned*), en la que la web pueda advertir al usuario que sus datos fueron víctima de vulneración / brecha.

        - **Pasarela de pagos**: Relegar seguridad sobre pasarelas de pagos de terceros como Mercado Pago, Paypal u otros. 


- Command & Control
    > Resumen: Romper la conexión del atacante e impedir la extracción de datos.  
        
    - **Detección**:
        - **Aviso de actividad (Beaconing)**: Detectar tráfico de red saliente pequeño y periódico desde el servidor Nginx a dominios IP o hosts desconocidos (el beacon o baliza). 

    - **Mitigación**:
        - **Reglas de Firewall (o Proxy)**: Implementar reglas de salida estrictas que solo permitan al servidor web comunicarse con una lista blanca de dominios conocidos (ej., pasarela de pago). Todo lo demás es bloqueado.

    - **Detección**: 
        - **Eventos inusuales para server Nginx**: Se detecta que el servidor Nginx estaría borrando archivos.

        - **Mitigación**: Configurar el mínimo Privilegio para Nginx para que corra con un usuario que solo tenga permisos de lectura y escritura limitada (solo en logs y cache), negándole el permiso de borrado en el resto de directorios sensibles.
        
- Instalación:
    
    > Resumen: Se detectan diferencias respecto a la versión "release" de Nginx.  
    
    - **Detección**: Se compara la lista de archivos binarios (.so) en el directorio de módulos del servidor comprometido con la lista de módulos esperados para esa versión de release.

    - **Mitigación**: No permitir que Nginx pueda realizar modificaciones sobre archivos de configuración (como la ruta: /etc/nginx/nginx.conf)


- Explotación:
    > Resumen: Detectar inicios de sesión sospechosos. 

    - **Detección**: Por medio de monitoreo, se detectan inicios de sesiones de lugares no frecuentes de dispositivos no registrados en la plataforma asociados a un usuario.

    - **Mitigación**: 
        - **Implementar multifactor**: En caso de detectar inicio de sesiones de dispositivos no frecuentes, pedir multifactor de autenticación.

- Delivery:
    > Resumen: Instalación de antivirus, filtro de phishing y denuncias. 

    - **Detección**: Se detectan varios e-mails de correos no deseados en los administradores del servidor. 

    - **Mitigación**: Verificar últimas versiones del navegador web. Denunciar como *phishing* en caso de poder detectar al menos un correo.
    Instalar un Antivirus en las máquinas de trabajo de los administradores para detectar y bloquear la ejecución de malware (actualizado). 


- Weaponization:
    - Sin interacción.


- Reconnaissance:
    > Resumen: Asegurarse de contar con versiones de Nginx actualizadas y que no tengan vulnerabilidades de día cero. Ser precavido con la información que se publica.

    - **Detección**: Se detectó que la versión de Nginx que se encuentra en el servidor tiene una release con parches de seguridad.

    - **Mitigación**:
        - Minimizar al máximo la posibilidad de que un atacante pueda entrar por esa brecha y planificar una migración de versiones de Nginx.
    
        - Reducir al mínimo el número de puertos expuestos a internet.

        - En lo posible, evitar publicar en redes sociales lugares de trabajo o información sensible que pueda ser útil para el contacto del atacante a la víctima. 




