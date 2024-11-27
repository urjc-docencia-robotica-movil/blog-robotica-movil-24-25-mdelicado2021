# **GLOBAL NAVIGATION P4**.

This practice required implementing a navigation system based on cost and gradient maps to guide a robot to a target.

## **Functions and Usefulness**

### **Safety: `add_margin_to_map`**
- **Description**: 
  Widens obstacles on the map by adding a safety margin around them, increasing the cost of nearby cells. This helps prevent the robot from passing too close to obstacles.
- **Utility**: 
  Improves navigational safety by ensuring a minimum separation between the robot and obstacles.

### **Grid creation: `build_cost_map`**
- **Description**: 
  Creates a cost map from a target using the cost propagation algorithm. Propagation is limited to an area around the robot and the target, with a configurable margin.
- **Utility**: 
  Provides an optimised cost map that guides the robot from its current position to the target.
- **Clarifications**:
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
  This part of the function creates the region over which the grid will be made.

### **Compute gradient: `compute_gradient`**
- **Description**: 
  Computes the gradient in a cost map cell using central differences. This gradient indicates the direction of least cost.
- **Utility**: 
  Determines the most efficient direction for the robot to move towards the target.
- **Clarifications**:
  ```python
    grad_x = (cost_grid[y, x + 1] - cost_grid[y, x - 1]) / 2.0 if 0 < x < cost_grid.shape[1] - 1 else 0
    grad_y = (cost_grid[y + 1, x] - cost_grid[y - 1, x]) / 2.0 if 0 < y < cost_grid.shape[0] - 1 else 0
  ```
Here is where the gradient of every map cell is calculated.

  
### Navigation: `go_to_target`
- **Description**: 
  Uses the cost map and gradient to move the robot towards the target. Adjusts the linear and angular velocities of the robot based on the gradient and the current position of the target.
- **Utility**: 
  Implements motion control of the robot, ensuring that it follows the optimal path to the target.
- **Clarifications**:
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
This is the way to calculate linear and angular velocities as a function of the target cell.


## **Challenges**
During this practice I had different difficulties. First of all the basic grid I implemented only propagated in four directions (top, bottom, right, left), which was solved by adding the remaining directions (diagonals):
```python
directions = [(-1, 0), (1, 0), (0, -1), (0, 1), (-1, -1), (-1, 1), (1, -1), (1, 1)]
```
In addition, the basic grid also did not take into account the cells near the obstacles, so the taxi was driving close to the walls, as can be seen in the video: https://youtu.be/ful4NNGGG4Y.
This was solved by the function ‘add_margin_to_map’, which allows a circulation less adjusted to the real margins of the walls of the map: https://youtu.be/Be37JCWNttQ.

On the other hand, the grid also had the problem of being spread over the entire map, which caused a loss of efficiency in terms of computational cost as it took into account parts of the map over which the taxi would not pass in order to reach the target.
Therefore, the grid propagation was limited to the position of the taxi plus a small modifiable margin.

The navigation part is based on an algorithm that determines, in a modifiable range, which cell has the lowest cost (the closest to the target) obtaining the following behaviour: https://youtu.be/vpbyIQq1eoE.
The navigation part also posed some challenges. For example, the function ‘go_to_target’, which is in charge of navigation, in its first design, generated a scary behaviour of the taxi as it gave a lot of value to the cells near the walls with, in addition, a lot of range.
The final code results in a robust and efficient behaviour that can be seen in the following video: https://youtu.be/Og3FECglrX4.
