# OBSTACLE AVOIDANCE P3
In this practice we are asked to implement a software based on the virtual force navigation (VFF) algorithm to make the car avoid obstacles (walls and other cars) and drive around the track using a classical plan (sequence of sub-objectives) of targets.


## Code structure

### 1. PID controller
A **PID** controller is used to adjust the speed of the car as a function of the distance to obstacles:
- `Kp`, `Ki`, `Kd`: Constants for proportional, integral and derivative terms.
- Controls the speed smoothly by adjusting the error between the desired speed and the actual speed.

### 2. Laser data interpretation: `parse_laser_data`.
Converts the laser sensor data into a list of `(distance, angle)` pairs:
- Limits the distances to a maximum value (10 metres) to avoid overloading the calculations.
- The angles are adjusted considering that the front of the car is angle -90°.

### 3. Repulsive force: `calculate_obstacle_avoidance_force`.
Generates a vector of **repulsive force** that moves the car away from obstacles:
- The magnitude of the repulsive force is inversely proportional to the distance to the obstacle (`K_REPULSIVE / dist^2`).
- Increases repulsion when obstacles are very close (below `DIST_MIN`).
- Limits the force to avoid sudden movements.

### 4. Attractive force: `calculate_direction_to_target`.
Calculate the **attractive force** to the next target:
- Normalise the direction to the target (`targetx`, `targety`) to get a unit vector to guide the car to the next waypoint.

### 5. Combination of forces: `combine_forces`.
Combine repulsive and attractive forces:
- Use the `alpha` and `beta` coefficients to weight the influence of each force.
- This balance is key to avoid collisions without losing direction to the target.

### 6. Angular velocity: `angular_speed_based_on`.
Adjusts the angular speed (`W`) of the car based on the resultant force and the current speed:
- Reduces the steering angle at high speeds to avoid sharp turns, improving high speed stability.

### 7. Main loop control logic
The main loop manages the navigation flow:
1. Gets the current position of the car.
2. Calculate the attractive and repulsive forces.
3. It adjusts the speed by means of the PID controller.
4. Sets the direction to the next waypoint.
5. Checks if the target has been reached.

The car updates its speed (`V`) and turn (`W`) continuously until the course is completed.


## Previous Blog Link
Here, you can see the code's gradual development: https://mariofburjc.blogspot.com/2023/11/p3fuerzas.html