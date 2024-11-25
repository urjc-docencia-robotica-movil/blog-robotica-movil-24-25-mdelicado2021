# **GLOBAL NAVIGATION P4**

Esta práctica requeria implementar un sistema de navegación basado en mapas de costos y gradientes para guiar a un robot hacia un objetivo.

## **Funciones y Utilidad**

### **Seguridad: add_margin_to_map**
- **Descripción**: 
  Amplía los obstáculos en el mapa al agregar un margen de seguridad alrededor de ellos, aumentando el costo de las celdas cercanas. Esto ayuda a evitar que el robot pase demasiado cerca de los obstáculos.
- **Utilidad**: 
  Mejora la seguridad en la navegación al garantizar una separación mínima entre el robot y los obstáculos.

### **Creación del grid: build_cost_map**
- **Descripción**: 
  Crea un mapa de costos a partir de un objetivo utilizando el algoritmo de propagación de costos. La propagación se limita a un área alrededor del robot y el objetivo, con un margen configurable.
- **Utilidad**: 
  Proporciona un mapa de costos optimizado que guía al robot desde su posición actual hacia el objetivo.
- **Aclaraciones**:
  ```python
    # Define the bounding box to limit the region of propagation
    min_x = min(x_target, x_robot) - margin
    max_x = max(x_target, x_robot) + margin
    min_y = min(y_target, y_robot) - margin
    max_y = max(y_target, y_robot) + margin
    
    # Ensure the boundaries are within the map
    min_x = max(min_x, 0)
    max_x = min(max_x, map_array.shape[1] - 1)
    min_y = max(min_y, 0)
    max_y = min(max_y, map_array.shape[0] - 1)
  ```
  Esta parte de la función crea la región sobre la que se hará el grid.

### **Cálculo del gradiente: compute_gradient**
- **Descripción**: 
  Calcula el gradiente en una celda del mapa de costos usando diferencias centrales. Este gradiente indica la dirección de menor costo.
- **Utilidad**: 
  Determina la dirección más eficiente para que el robot avance hacia el objetivo.
- **Aclaraciones**:
  ```python
    grad_x = (cost_grid[y, x + 1] - cost_grid[y, x - 1]) / 2.0 if 0 < x < cost_grid.shape[1] - 1 else 0
    grad_y = (cost_grid[y + 1, x] - cost_grid[y - 1, x]) / 2.0 if 0 < y < cost_grid.shape[0] - 1 else 0
  ```
  Aquí es donde se calcula el gradiente de cada celdilla.

  
### **Navegación: go_to_target**
- **Descripción**: 
  Utiliza el mapa de costos y el gradiente para mover al robot hacia el objetivo. Ajusta las velocidades lineal y angular del robot en función del gradiente y la posición actual del objetivo.
- **Utilidad**: 
  Implementa el control del movimiento del robot, asegurándose de que siga el camino óptimo hacia el objetivo.
- **Aclaraciones**:
  ```python
    # Calculate direction to the selected cell
    target_x, target_y = min_cost_pos
    delta_x = target_x - y_robot
    delta_y = target_y - x_robot

    # Calculate target angle and angular speed
    target_angle = math.atan2(delta_y, delta_x)
    current_angle = pose.yaw
    angle_diff = (target_angle - current_angle) % (2 * math.pi)
    if angle_diff > math.pi:
        angle_diff -= 2 * math.pi

    angular_speed = angle_diff * 2.0  # Angular correction gain
    linear_speed = max(2.5, 5.0 - abs(angle_diff))  # Adaptive linear speed
  ```
  Esta es la forma de calcular la velocidad lineal y angular en función de la celdilla objetivo.


## **Desafios**
Durante esta práctica he tenido diferentes dificultades. En primer lugar el grid básico que implementé unicamente se propagaba en cuatro direcciones (arriba, abajo, derecha, izaquierda), lo que se solucionó añdiendo las direcciones restantes (diagonales):
```python
directions = [(-1, 0), (1, 0), (0, -1), (0, 1), (-1, -1), (-1, 1), (1, -1), (1, 1)]
```
Además, el grid básico tampoco tenía en cuenta que las celdillas cercanas a los obstáculos por lo que el taxi circulaba pegado a las paredes como se puede ver en el vídeo: https://youtu.be/ful4NNGGG4Y.
Esto se solucionó mediante la función "add_margin_to_map", que posibilita una circulación menos ajustada a los márgenes reales de las paredes del mapa: https://youtu.be/Be37JCWNttQ.

Por otro lado, el grid también tenía el problema de que se extendia por todo el mapa, lo que provocaba una pérdida de eficiencia en cuanto al coste computacional ya que se tenían en cuenta partes del mapa sobre las que el taxi no pasaría para llegar al objetivo.
Por ello se limitó la propagación del grid hasta la posición del taxi más un pequeño margen modificable.

La parte de navegación está basada en un algoritmo que determina, en un rango modificable, qué celdilla es la de menor coste (la más cercanas al objetivo) obteniendo el siguiente comportamiento: https://youtu.be/vpbyIQq1eoE.
La parte de navegación supuso también ciertos desafios. Por ejemplo, la función "go_to_target", que es la encargada de la navegación, en su primer diseño, generaba un comportamiento miedoso del taxi ya que se le daba mucho valor a las celdillas cercanas a las paredes con, además, mucho rango.
El código final provoca un comportamiento robusto y eficiente que se puede comprobar en el siguiente vídeo: https://youtu.be/Og3FECglrX4.
