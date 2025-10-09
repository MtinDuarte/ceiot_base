
# Alumno: Martin Duarte

## Enunciado

Desarrollar la defensa en función del ataque planteado en orden inverso, mencionar una medida de detección y una de mitigación, sólo lo más importante, considerar recursos limitados. No es una respuesta a un incidente, hay que detectar el ataque independientemente de la etapa.


## Resolución

- Actions on Objectives
    > Resumen: Detectar que el atacante usa los datos robados. Mitigación: cifrar los datos o utilizar pasarela de compras. Implementar doble factor al finalizar la compra. 

    - **Detección**: Se detecta inicio de sesiones de lugares no frecuentes o dispositivos no registrados en la plataforma asociados a un usuario.

    - **Mitigación**: 
        - **Cifrar datos**: Al momento de ejecutar la transacción, en lugar de utilizar un formulario de pago, usar un token de un solo uso para hacer la compra. 

        - **Implementar multifactor**: En caso de que el skimmer haya robado datos, implementar en la web doble factor con el objetivo que al finalizar la compra solicite un verificación en otro dispositivo. 


- Command & Control
    > Resumen: Detección de un tráfico inusual bajo un usuario desconocido. Mitigación: limitar el acceso del usuario y gestionar reglas de tráfico para restringir la comunicación.
    
    - **Detección**: Se detecta solicitudes que no corresponden a un usuario de la organización/empresa.

    - **Mitigación**:
        - Se quitan permisos del usuario desconocido. 
        - Se añaden reglas de firewall para bloqueo de tráfico entrante y saliente a la IP destino donde se verificó tráfico inusual.

- Instalación:
    - **Detección**: Se verifican los logs de actividad del sistema, y se encuentran inicios de sesión bajo ssh de un usuario que no se tiene información.

    - **Mitigación**: Deshabilitar momentaneamente el tráfico SSH. 
    
    Luego verificar y posteriormente elimnar las claves públicas generadas por el usuario desconocido.


