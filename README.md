# VACUUM CLEANER P1 FORUM
The task is to program a basic vacuum cleaner that covers the largest surface area in the shortest possible time. In this exercise, a robust and reactive automaton was required.

## Code Structure
To ensure the vacuum cleaner adopts the appropriate behavior, I used a state machine that transitions based on sensory input. In other words, it changes state due to certain sensory data being received (in this case, the bumper).

To decide which states occur in each iteration, it is determined randomly by assigning certain probabilities to how the vacuum will act (moving forward, moving backward, and spirals).

The vacuum cleaner would cover this area in just a few minutes:
![image](https://github.com/user-attachments/assets/98e0cade-f048-44a0-bbd3-fa411566c6e4)

A video of the operation: https://youtu.be/WPY0k75JNCk _*due to the length of the video it is recommended to use x2_

## Other Ideas
Another way to organize the state machine is, instead of semi-randomly deciding the action to take, to follow a logical pattern. For example, I make spirals until it bumps into something, then reverse, turn, and move forward.

Additionally, a tool that can be used when employing the logic method is a laser. The laser can be used to detect disturbances.

Finally, timestamps can be used to change states so that we only remain in a state for the desired amount of time.

These are some tools that can be used to support the implementation of reactive and robust code. However, in this case, I considered it unnecessary to use them.

## Previous Blog Link
Here, you can see the code's gradual development: https://mariofburjc.blogspot.com/2023/09/p1aspiradora.html

