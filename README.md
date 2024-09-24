# FORO P1 ASPIRADORA
Se trata de programar una aspiradora básica que cubra la mayor superficie en el menor tiempo posible. En esta práctica se requería un autómata robusto y reactivo.

## Estructura del código
Para que la aspiradora adopte un comportamiento adecuado he utilizado una máquina de estados en la que se transiciona de forma senorial. Es decir, cambia de estado debido a ciertos datos sensoriales que se reciben (en este caso, el bumper).

Para elegir qué estados se dan en cada iteración se decide de forma aleatoria dando ciertos valores a la probabilidad de que se actúe de distintas formas (avanzando, retrocediendo y espirales).

La aspiradora cubriría esta zona en pocos minutos:
![image](https://github.com/user-attachments/assets/98e0cade-f048-44a0-bbd3-fa411566c6e4)


## Otras ideas
Otra forma de organizar la máquina de estados es, en vez de decidir la acción a tomar de forma semialeatoria, se puede seguir una lógica. Por ejemplo, hago espirales hasta que se choque, retrocedo y, después, giro y avanzo.

Por otro lado, una herramienta que se puede utilizar, empleando el método por lógica, es el láser. Se puede utilizar para detectar perturbaciones en el láser.

Por último, se pueden utilizar marcas de tiempo para cambiar de estado de forma que unicamente nos encontramos en un estado durante el tiempo deseado.

## Link blog anterior
Aquí se puede comprobar el crecimiento del código poco a poco: https://mariofburjc.blogspot.com/2023/09/p1aspiradora.html
