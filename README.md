# Udacity Flying Car Nanodegree Project 4: Estimation of a Quadrotor #
###### Writeup by Derek Lukacs ######

## Overview ##
This project involved the creation and implementation of an Extended Kalman Filter (EKF) to predict a quadrotors state. This was done as a part of the Udacity Flying Car Nanodegree (FCND) program.

Instead of having a full 12 variable kalman filter to predict all 12 3D states of the vehicle, a 7 variable EKF was used instead. To simplify the system, first the body rates of the vehicle were used as prediction input instead of a state variable. These were used in a complementary filter that predicts the roll and pitch angles. 

Another simplification is the use of the IMU in the prediction step instead of in the update step. 

The results of this estimation was a quadrotor that could loosely follow the planned trajectory. More tuning will be needed to improve the effectiveness with which the vehicle follows the trajectory.

The success in the estimation is that it is capable of high accuracy state estimation compared to the actual values. 

Below is the pseudocode for the EKF algorithm as it was implemented. The primary work done was to define the transition and measurement models g and h, and their derivatives g' and h'. 

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


#### Predict() ####
Changes to the covariance were calculated using the algorithm outlined in the EKF pseudocode. The previous covariance is premultiplied by the derivative of the transition function and post multiplied by the transpose of the derivative of the transition function. Added to this is the transition model covariance, Q<sub>t</sub>.

Below are two simulations that test the performance of the prediction step. 

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen8_after.gif?raw=true"
     alt="Scene 8 Simulation" />

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen9_after.gif?raw=true"
     alt="Scene 9 Simulation" />

## Update Step of EKF ##

The main aspect of developing an EKF update step is to create an accurate measurement model h, and its derivative h'.

#### GPS Update ####

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Figures/GPS_update.png?raw=true"
     alt="GPS Update diagram" />


The GPS update step follows the EKF algorithm and corrects the prediction on position and velocity. 


<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen11_after.gif?raw=true"
     alt="Scene 11 Simulation" />

#### Magnetometer Update ####
The magnetometer update is similar to GPS update except there is a different measurement model. This measurement model was implemented and the state is updated. Below is the simulation that tests the effectiveness of the yaw estimate with the magnetometer sensor data used to update the prediction. 

<img src="https://github.com/dereklukacs/FCND-Estimation-CPP/blob/master/images/Gifs/Scen10_after.gif?raw=true"
     alt="Scene 10 Simulation" />


#### Detuning controller ####
In order for the quadrotor to function with the imperfect estimation data, the controller gain parameters were "detuned." This allows realistic performance. All of the simulations shown in this document were recorded when the controller had been detuned and with the flight controller built in the previous project. 

## Future Work ##

- Utilizing other estimation methods (UKF)
- Designing different controller schemes
- Improve the tuning of controller for estimated state to improve path following.


