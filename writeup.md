# CarND-MPC-Project Solution Writeup

---

### Model: 

The state of the vehicle is defined by (x, y, v, psi), where x and y describe the location of the vehicle, and v and psi describes its velocity and orientation or steering angle, respectively.

Delta and a are the actuators of the model (they determine the changes in the vehicle's state).

The model for the vehicle path is defined with the following set of equations:

![model]{img/model.png}

The first two equations describe the location of the car in the next time step, given its current location, velocity, and change in steering angle.

The third equation describes the steering angle at the next time step, given the change in steering angle and the distance between the front of the vehicle and its center of gravity, a constant denoted by L_f.

The fourth equation describes the velocity at the next time step given the current acceleration and velocity.i

Finally, the last two equations describe the change in the following two errors: 
1. cross-track-error (CTE): This describes how far the car is away from its desired location.
2. orientation error: This describes how far the car's steering angle is away from its desired sterring angle.

--- 

### Parameter selection: N and dt

I tried several different combinations of N and dt parameters before settling on values that worked well(N=10 and dt=0.5).

First, I tried larger values of dt, i.e. dt=1.0; given the same objective function, this caused more frequent changes in the actuator values, which made sense given that a less fine-grained approximation would lead to less accuracy in the update functions. 

I also found that smaller N worked better; a higher N (between 20 30) causes the model to optimize over a much larger time horizon, and if the future path is a high-order polynomial, the resulting planned path will fit poorly, leading to larger cte and orientation errors and a more uneven path for the vehicle (or the vehicle leaving the track altogether, if the regularzation on actuator updates is not high enough).

--- 

### Fitting and preprocessing

The only preprocessing done on waypoints was to transform them to the vehicle's perspective in lines 110-115 of main.cpp.
The fitting was done using the given polyfit() function to fit a degree-3 polynomial.

---

### Latency

The latency between the calculation of the actuation value and their actual application to the vehicle was not done explicitly, but rather, implicitly, by limiting the speed of the vehicle and penalizing large updates to the actuation values. 

