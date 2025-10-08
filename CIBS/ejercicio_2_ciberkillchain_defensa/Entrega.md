
# Alumno: Martin Duarte

## Enunciado

Desarrollar la defensa en función del ataque planteado en orden inverso, mencionar una medida de detección y una de mitigación, sólo lo más importante, considerar recursos limitados. No es una respuesta a un incidente, hay que detectar el ataque independientemente de la etapa.


## Resolución

* Actions on Objectives
    > Resumen: cifrar los datos o utilizar pasarela de compras. Implementar doble factor al finalizar la compra. 

    - **Detección**: Se detecta inicio de sesiones de lugares no frecuentes o dispositivos no registrados en la plataforma.

    - **Cifrar datos**: Al momento de ejecutar la transacción, en lugar de utilizar un formulario de pago, usar un token de un solo uso para hacer la compra. 

    - **Implementar multifactor**: En caso de que el skimmer haya robado datos, implementar en la web doble factor con el objetivo que al finalizar la compra solicite un verificación en otro dispositivo. 


* Command & Control
    > Resumen: -

