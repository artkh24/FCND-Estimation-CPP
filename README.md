should include a statement and supporting figures / images that explain how each rubric item was addressed, and specifically where in the code each step was handled.


Supporting figures:
	 - table of gains and tuned values
	 - images from each scenario plot
	 - table of scenario performance
	 - overview diagram of code
	 - gifs of the simulations


**Determine the standard deviation of the measurement noise of both GPS X data and Accelerometer X data.**

	This was completed by importing the data into matlab and calculating the standard deviation of the measured data is the std(data) function.

**Implement a better rate gyro attitude integration scheme in the UpdateFromIMU() function.**

	The integration scheme was improved by performing the integration in the nonlinear quaternion space. 

	1. The current heading was converted to a quaternion $(qt)$
	2. The body rates were integrated to get a change in angle (dq) and then converted to quaternion space.
	3. The new attitude estimate came from rotating qt by dq by multiplying them together. $dq*dt$

   ??show performance plot?? Show gif of simulation

   This satisfied that requirements of 0.1 radian accuracy for 3 seconds in the simulation.


**Implement all of the elements of the prediction step for the estimator.**
	?? overview figure of predicton step??
	The Rgb' matrix was constructed by taking the partial derivative of each element with respect to $\psi$

	The state covariance was updated by

	??Predict() and PredictState() discussion??


**Implement the magnetometer update.**
	Measured the short way around by constraining the value of the error

	Used the ??following?? measurement model and implemented in code.

	??gif of before and after??


**Implement the GPS update.**

	??GPS measurement model??

	?? GPS before and after??

**Detuning controller**



