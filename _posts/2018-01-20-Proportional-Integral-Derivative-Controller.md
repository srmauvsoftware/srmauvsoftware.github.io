---
title: Proportional-Integral-Derivative Controller
date: 2018-01-20 23:29:08
categories:
- Control Systems
tags:
- SRMAUV
- Control Systems
---

Proportional-Integral-Derivative is a control loop feedback mechanism used in industrial control systems. I implemented it in Autonomous Underwater Vehicle, here's my intutition -  

---

## Proportional Term:

Mr. Proportional : He looks at where the output is and compares to what you asked for. Hence it says:
- There is large error : Take Big Action
- There is small error : Take Small Action
- You have is what you asked for : Take No Action

So basically what is this error?  
Take a scenario of depth controller in an AUV, Pressure sensor returns the measured pressure value which is the value you have and now you want to reach a particular pressure value say from 520 to 530. So you'll probablly have to initiate depth thrusters. The change in PWM of thruster servos must be controlled through a controller, here's where the controller part comes in to reduce the error form 10 to 0.

```
pError = Setpoint - SensorValue;
```

P<sub>out</sub> = K<sub>p</sub> * pError
K<sub>p</sub> - Proportional Gain Constant.  
Issue with P: Steady State Error.  

---

## Integral Term:

Mr. Integral : He looks at the same error value, but compares it to how long its been that way and states:
- You have a chronic/acute error (Small errors for long time/ Big error or small time) - Take Big Action
- You have mild error (Small error for short time) - Take Small Action
- Your error history is neutral - Take No Action

What is the use of Integral term?  
Actually when 'P' mode alone is used, we would face the offset (+ve or -ve deviation from setpoint) problem.
Consider the case of heading controller in an AUV, Inertial Measurment Sensor would provide the heading angles, now say a setpoint to turn 30 degree, Proportional would take action generating an offset, making the vehicle oscillate around +ive and  -ive directions of setpoint. In order to nullify steady state error, 'I' mode is introduced, Integral action would add up all the error with respect to time and it will track the system to its setpoint.  
```
iError = iError + pError * dt;  
```

I<sub>out</sub> = K<sub>i</sub> * $$\int_0^t pError(t) \,dt$$  
K<sub>i</sub> = Integral Gain Constant   

Issue with PI: Overshooting-Integral Windup i.e When large change in setpoint occurs, Integral term accumulates a significant error during the rise, thus overshooting, making PI controller ineffective in dynamic conditions.  
Amazing example of Integral Windup - [ClickMe](https://instrumentationtools.com/what-is-integral-wind-up/)


---


## Derivative Term:

Mr. Derivative: He also looks at the same error, but compares it to how its changing. He says:
- Error is getting bigger: Take bigger action
- Error is getting smaller: Take negative action
- Error is not changing: Take no action

What does Derivative Term work on?  
While driving a car behind someone who is already at stop. As you get closer to them, not only you want to leave the accelerator, but also want to apply brake. This braking action is given by Derivative in PID Controller. It tells to slowdown more if you are getting closer to the target. Derivative control adds another dimension of complexity to control loops. It does have its benefits, but only in special cases.
```
dError = (pError - previousError);
```

D<sub>out</sub> = K<sub>d</sub> * $$\frac{d pError(t)}{dt}$$  
K<sub>d</sub> = Derivative Gain Constant

---
Summing up the above three controllers:  
```
ControlSignal = kp*pError + ki*iError + kd*dError;
```
P<sub>out</sub> + I<sub>out</sub> + D<sub>out</sub> =  K<sub>p</sub> * pError + K<sub>i</sub> * $$\int_0^t pError(t) \,dt$$ + K<sub>d</sub> * $$\frac{d pError(t)}{dt}$$  

<br /> PID Constant Tuning Method:
- Set K<sub>i</sub> and K<sub>d</sub> = 0. Increase K<sub>p</sub> until system oscillates.
- Adjust K<sub>i</sub> so oscillations stop.
- Finally adjust K<sub>d</sub> for fast response.
