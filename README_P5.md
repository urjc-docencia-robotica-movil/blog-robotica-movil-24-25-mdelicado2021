# **Monte Carlo Laser Localization**

This project implements a Monte Carlo localization algorithm to estimate the position of a robot using laser sensor data and a predefined map. The algorithm uses particle filtering to approximate the robot's position probabilistically.

---

## **Functions and Usefulness**

### Particle Initialization: `initialize_particles`
- **Description**: 
  Generates particles randomly within the free space of the entire map.
- **Utility**: 
  Ensures that particles are initialized in valid locations, avoiding obstacles while spreading evenly across the map.
- **Code Snippet**:
  ```python
  x = np.random.uniform(0, map_data.shape[1])  # Entire width of the map
  y = np.random.uniform(0, map_data.shape[0])  # Entire height of the map
This ensures the particles are spread across the whole map for unbiased initialization.


### Free Space Validation: `is_free_space`

- **Description**: 
    Validates if a given coordinate is within the free space of the map.
- **Utility**: 
    Prevents particles from being initialized in obstacles or invalid areas.
- **Code Snippet**:
  ```python
  return map_data[map_y, map_x] > 200
  ```
White pixels in the map represent free space, ensuring valid particle placement.

### Laser Data Parsing: `parse_laser_data`

- **Description**: 
    Processes laser sensor data from the robot and reduces the number of beams to LASER_NUM_BEAMS.
- **Utility**: 
    Reduces computational load without losing significant sensor information.
- **Code Snippet**:
  ```python
  indices = np.linspace(0, len(distances) - 1, LASER_NUM_BEAMS, dtype=int)
  return distances[indices]
  ```
This balances computational efficiency and precision by evenly sampling laser beams.


### Ray Tracing: `ray_trace`

- **Description**: 
    Simulates theoretical laser readings from the pose of a particle using ray tracing.
- **Utility**: 
    Provides predicted laser distances for each particle, enabling comparison with actual sensor data.
- **Code Snippet**:
  ```python
  while total_distance < LASER_MAX_DISTANCE:
      test_x = int(start_x + total_distance * math.cos(angle))
      test_y = int(start_y + total_distance * math.sin(angle))
      if map_data[test_y, test_x] == 0:
          distances.append(total_distance)
          break
  ```
Simulates laser beams until they hit an obstacle or reach the maximum distance.


### Weight Computation: `compute_weights`

- **Description**: 
    Calculates the importance weights for particles by comparing theoretical and real laser data.
- **Utility**: 
    Focuses the particle filter on areas with a higher probability of containing the robot.
- **Code Snippet**:
  ```python
    error = np.sum((sensor_data_trimmed - laser_theoretical_trimmed) ** 2) / min_len
    weight = np.exp(-error / 0.2)
  ```
Particles with lower error between predicted and actual data receive higher weights.


### Particle Resampling: `resample_particles`

- **Description**: 
    Resamples particles based on their weights, redistributing them to areas with higher probability.
- **Utility**: 
    Maintains the particle count while focusing on regions more likely to contain the robot.
- **Code Snippet**:
  ```python
    indices = np.random.choice(len(particles), size=N_PARTICLES, p=weights)
    resampled = particles[indices]
  ```
This ensures that the particle filter efficiently converges towards the robot's actual position.


### Particle Update: `update_particle_poses`

- **Description**: 
    Updates particle positions to simulate robot motion while adding random noise.
- **Utility**: 
    Reflects uncertainties in motion while ensuring particles stay in valid positions.
- **Code Snippet**:
  ```python
    dx = LINEAR_VEL * dt * np.cos(theta) + np.random.normal(0, 0.1)
    dy = LINEAR_VEL * dt * np.sin(theta) + np.random.normal(0, 0.1)
  ```
This adds motion dynamics and noise for realism in particle movement.


### Pose Estimation: `gui.showPosition`

- **Description**: 
    Estimates the robot's position by computing the weighted average of particle positions.
- **Utility**: 
    Provides a probabilistic estimate of the robot's location.
- **Code Snippet**:
  ```python
    estimated_pose = np.average(particles[:, :3], axis=0, weights=particles[:, 3])
    gui.showPosition(estimated_pose[0], estimated_pose[1], estimated_pose[2])
  ```
Displays the estimated position in the GUI alongside the actual robot pose.


## Challenges

### 1. Particle Initialization

- **Issue**: Particles initialized in obstacles caused poor localization.
- **Solution**: Used is_free_space to validate initialization and ensure particles were placed only in valid areas.

### 2. Discrepancies in Laser Data

- **Issue**: Misalignment between simulated and actual laser readings caused localization inaccuracies.
- **Solution**: Refined the ray tracing algorithm to better simulate real laser data, aligning it with the robot's sensor specifications.

### 3. Balancing Noise and Accuracy

- **Issue**: Excessive noise in motion updates caused particles to diverge, reducing accuracy.
- **Solution**: Tuned noise parameters to balance robustness and localization precision.


## Results

The Monte Carlo localization system achieves:

- Convergence of particles towards the robot's true position over time.
- Accurate tracking of the robot's trajectory using weighted pose estimation.

First of all, one of the versions of the code that did not have such fine-tuned parameters caused this behaviour: https://youtu.be/8c7Tw98B6KI

Finally, with the final code, the behaviour is relatively close to the expected result: https://youtu.be/KXeKTTzfL-E


## Update for proper operation

The key differences between the functional implementation and the previous version highlight how the algorithm's efficiency and accuracy have evolved. Here’s a summary of the contrasts:

- **Parallel Processing**:
        The functional implementation leverages the multiprocessing module to parallelize weight calculations for the particles, enabling faster computations and better scalability with increased particle counts.
        The earlier iteration processes weights sequentially, making it significantly slower and unsuitable for real-time applications.

- **Error Metrics and Weight Calculation**:
        The finalized implementation adopts RMSE (Root Mean Square Error) for comparing theoretical and real laser data. This ensures a precise error representation, contributing to accurate particle weighting.
        The prior approach employs simpler, less reliable metrics, potentially leading to incorrect weight distribution and suboptimal performance.

- **Particle Motion Model**:
        The refined algorithm uses ground truth pose data for particle propagation when odometry is unavailable, while introducing noise to account for real-world uncertainties.
        The previous design relies solely on commanded velocities, disregarding external factors like friction or motor variability, leading to accumulated inaccuracies over time.

- **Optimization and Performance**:
        The optimized version uses numpy's vectorized operations for many computational tasks, enhancing speed and resource efficiency.
        The preceding code relies heavily on Python loops, resulting in slower execution and scalability issues.

- **Ray Tracing and Laser Simulation**:
        The functional code improves laser simulation by refining the ray-tracing logic and ensuring compatibility with the map's resolution and boundaries.
        The earlier prototype contains inconsistencies in the ray-tracing logic, which could lead to erroneous distance calculations and map interactions.

- **Robustness and Edge Case Handling**:
        The current implementation incorporates mechanisms to handle edge cases, such as ensuring particles remain within valid map areas and normalizing weights to avoid computational errors like division by zero.
        The initial attempt lacks such safeguards, making it prone to crashes or undefined behaviors under certain scenarios.

- **Clarity and Modularity**:
        The functional approach emphasizes modularity with clearly defined, reusable functions and detailed comments, improving maintainability and readability.
        The earlier iteration suffers from redundant code and unclear logic in certain areas, hindering comprehension and debugging.

- **Behavior evolution**:
    - https://youtu.be/wYz2zhzX5pY
    - https://youtu.be/kb93XIp--BI
    - https://youtu.be/JBQUeTZiZMw
    - https://youtu.be/l9aoBvpvxxg
    - https://youtu.be/UdE1zaROdJk
    - https://youtu.be/E--K2iRMYN4
- **Final behavior**: 
    - https://youtu.be/zTc87NWTZAA

