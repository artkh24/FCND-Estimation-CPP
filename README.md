# Udacity Flying Car Nanodegree Project 4: Estimation of a Quadrotor #
### Derek Lukacs ###

## Overview ##
This project involved the creation and implementation of an Extended Kalman Filter (EKF) to predict a quadrotors state. This was done as a part of the Udacity Flying Car Nanodegree (FCND) program.

The estimation scheme for this system is broken down into 

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Figures/EKF_algorithm_pseudocode.png?raw=true"
     alt="EKF Pseudocode" />


## Understanding the Sensor Data ##

In order to determine the standard deviation of the measurement noise of both GPS X data and Accelerometer X data data was collected and processed. This was done by importing the data into matlab and calculating the standard deviation of the measured data is the std(data) function.

The GPS data sample had a standard deviation of 0.7157 cm and the Accelerometer data sample had a standard deviation of 0.5121 cm/s/s. These values were put into the simulation to confirm that approximately 68% of the data fell within 1 standard deviation, these are the white lines shown in the plots of the simulation below. 

This test was passed with 70% of the accelerometer data falling within 1 standard deviation and 68% of GPS falling within 1 standard deviation. Both of these values are very close to Gaussian.
<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen6_after.gif?raw=true"
     alt="Scene 6 Simulation" />



## Improving the Complementary Filter ##

The Complementary filter used in this project fuses Inertial Measurement Unit (IMU) data from the accelerometer and Gyro to determine pitch and roll angles. This is done using a weighted average GPS attitude and integrated body rates to achieve a new estimate. This is diagrammed below.

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Figures/Complementary_Filter_Before.png?raw=true"
     alt="Complementary Filter Diagram" />

The implementation that was given assumed that the vehicle had small angular displacements from zero, which isn't always the case. In order to more accurately integrate the gyro sensor data, the current attitude of the vehicle should be taken into account. The integration scheme was improved by performing the integration in the nonlinear quaternion space. 

1. The current heading was converted to a quaternion _q<sub>t</sub>_
2. The body rates were integrated to get a change in angle _dq_ and then converted to quaternion space.
3. The new attitude estimate came from rotating _q<sub>t</sub>_ by _dq_ by multiplying them together. _dq_ x _q<sub>t</sub>_


This results in much more accurate estimations of the attitude of the vehicle. 

The modified flow chart looks like the following, where the integration step is done in the quaternion space before the estimates are filtered together. 

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Figures/Complementary_Filter_After.png?raw=true"
     alt="Complementary Filter Diagram 2" />

These changes were made inside `UpdateFromIMU` function in the `QuadEstimatorEKF.cpp` file.

The error in the simulation (below) after this change has been implemented can be seen to be safely below 0.01 radians and is close to zero for most of the simulation.

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen7_after.gif?raw=true"
     alt="Scene 7 Simulation" />


## Prediction Step of EKF ##

The state prediction is handled in `PredictState()` and the covariance prediction is handled in `Predict()`.

#### PredictState() ####

This function follows the pseudocode algorithm outlined below. The predicted state is essentially all of the state variables predicted with an integration step. This is defined by the transition function, _g_.

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Figures/transition_function.png?raw=true"
     alt="Transition Function" />

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Figures/Predict_State.png?raw=true"
     alt="Diagram of Predict State function" />


An important thing to note is that the yaw value is not altered but is instead passed through from the complementary filter step. 

Changes to the covariance were calculated using the algorithm outlined in the EKF pseudocode. The previous covariance is premultiplied by the derivative of the transition function and post multiplied by the transpose of the derivative of the transition function. Added to this is the process bias, Q<sub>t</sub>.

Below are two simulations that test the performance of the prediction step. 

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen8_after.gif?raw=true"
     alt="Scene 8 Simulation" />

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen9_after.gif?raw=true"
     alt="Scene 9 Simulation" />

## Update Step of EKF ##
**Implement the magnetometer update.**
	Measured the short way around by constraining the value of the error

	Used the ??following?? measurement model and implemented in code.

	??gif of before and after??


**Implement the GPS update.**

	??GPS measurement model??

	?? GPS before and after??

**Detuning controller**

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen10_after.gif?raw=true"
     alt="Scene 10 Simulation" />

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen11_after.gif?raw=true"
     alt="Scene 11 Simulation" />

## Future Work ##

- Utilizing other estimation methods (UKF)
- Designing different controller schemes
- Improve the tuning of controller for estimated state to improve path following.


