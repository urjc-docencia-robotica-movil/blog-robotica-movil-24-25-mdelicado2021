# FORO P1 ASPIRADORA
Se trata de programar una aspiradora básica que cubra la mayor superficie en el menor tiempo posible. En esta práctica se requería un autómata robusto y reactivo.

## Estructura del código
Para que la aspiradora adopte un comportamiento adecuado he utilizado una máquina de estados en la que se transiciona de forma senorial. Es decir, cambia de estado debido a ciertos datos sensoriales que se reciben (en este caso, el bumper).

Para elegir qué estados se dan en cada iteración se decide de forma aleatoria dando ciertos valores a la probabilidad de que se actúe de distintas formas (avanzando, retrocediendo y espirales).

## Otras ideas
Otra forma de organizar la máquina de estados es, en vez de decidir la acción a tomar de forma semialeatoria, se puede seguir una lógica. Por ejemplo, hago espirales hasta que se choque, retrocedo y, después, giro y avanzo.
