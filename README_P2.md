# FOLLOW LINE P2
The aim of this practice is to make a controller for a racing car to follow a red line to complete the circuit.

## Code structure
The code is divided into several parts. Firstly, I have given it a fixed speed in order to test the mask for detecting the red line.
In addition, I have created the velocity control functions (linear and angular) but, for the moment, with constant velocities in order to, for the moment, not to take into account the error with respect to the red line.
I have subsequently implemented linear and angular speed PID controllers (one for each type of speed to make the control more efficient). However, the car is running very slowly. There are several solutions to this. Firstly, it could be due to a parameter setting issue such as proportional, derivative and integral constants for the control. However, I have detected that the real problem is a matter of my computer's CPU. Since the real time factor is about 0.2 and one greater than or equal to 0.5 is recommended, I started using the farm instead of launching the docker from my computer. Now the real time factor is about 0.6 which is not the best but it is possible to work with it.

## Behavior
This is a demonstration of the behaviour with a real time factor of about 0.5 with the holonomic vehicle in the simple circuit: https://youtu.be/3MdAjSnm2Iw


## Previous Blog Link
Here, you can see the code's gradual development: https://mariofburjc.blogspot.com/2023/10/p2f1siguelineas.html
