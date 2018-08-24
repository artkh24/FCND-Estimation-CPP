# Udacity Flying Car Nanodegree Project 4: Estimation of a Quadrotor #
## Derek Lukacs ##

should include a statement and supporting figures / images that explain how each rubric item was addressed, and specifically where in the code each step was handled.


Supporting figures:
- table of gains and tuned values
- images from each scenario plot
- table of scenario performance
- overview diagram of code
- gifs of the simulations

## Understanding the Sensor Data ##

In order to determine the standard deviation of the measurement noise of both GPS X data and Accelerometer X data data was collected and processed. This was done by importing the data into matlab and calculating the standard deviation of the measured data is the std(data) function.

The GPS data sample had a standard deviation of __ and the Accelerometer data sample had a standard deviation of __ . These values were put into the simulation to confirm that approximately 68% of the data fell within 1 standard deviation. This is shown in the simulation below.  

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen6_after.gif?raw=true"
     alt="Scene 6 Simulation" />



## Improving the Complementary Filter ##

### What is the Complementary Filter ###

**Implement a better rate gyro attitude integration scheme in the UpdateFromIMU() function.**

The integration scheme was improved by performing the integration in the nonlinear quaternion space. 

1. The current heading was converted to a quaternion $(qt)$
2. The body rates were integrated to get a change in angle (dq) and then converted to quaternion space.
3. The new attitude estimate came from rotating qt by dq by multiplying them together. $dq*dt$


??show performance plot?? Show gif of simulation

   This satisfied that requirements of 0.1 radian accuracy for 3 seconds in the simulation.

## Prediction Step of EKF ##
**Implement all of the elements of the prediction step for the estimator.**
	?? overview figure of predicton step??
	The Rgb' matrix was constructed by taking the partial derivative of each element with respect to $\psi$

	The state covariance was updated by

	??Predict() and PredictState() discussion??


## Update Step of EKF ##
**Implement the magnetometer update.**
	Measured the short way around by constraining the value of the error

	Used the ??following?? measurement model and implemented in code.

	??gif of before and after??


**Implement the GPS update.**

	??GPS measurement model??

	?? GPS before and after??

**Detuning controller**



