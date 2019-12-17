---
layout:      mypage

className:   RotateControl
includeFile: TeensyStep.h

chapters:
- name: Construction
  anchor: constructor
  methods: 

  - name: RotateControl
    shortDesc: Constructs a RotateControl object with selectable values for the step pulse width (µs) and the speed update period (µs). During acceleration and deceleration, the controller updates the motor speed periodically. The period of this updates is set by the speedUpdatePeriod parameter (500µs to 20000µs). Smaller values increase the processor load but lead to a smoother acceleration. A value of 5000µs is sufficient for most of real life applications. 
    parameter:
    - name: stepPulseWidth
      type: unsigned   
    - name: speedUpdatePeriod
      type: unsigned  

  - name: RotateControl
    shortDesc: Constructs a RotateControl object with default parameters for the step pulse width (5µs) and speed update period (5ms).

#----------------------------------------------------------------------------------------------------------------------------------------------------------
- name: Rotating Motors
  anchor: rotating
  methods:
  - name: rotateAsync
    shortDesc: >
      Accepts up to N=10 references to stepper objects and starts rotating them. The rotation speeds are defined by the Stepper::setMaxSpeed function.

      ```c++                        
         RotateControl controller;
         Stepper s1(1,2), s2(3,4);
         controller.rotateAsync(s1,s2);        
      ```
    returnType: void   
    parameter:
    - name: "s1 ["
      type: Stepper&
    - name: "sN]"
      type: "...Stepper&"

  - name: rotateAsync
    shortDesc: >-
      Accepts an array of up to N=10 pointers to stepper objects and starts rotating them. The rotation speeds are defined by the Stepper::setMaxSpeed function.  The function returns immediately after starting the motors (non blocking).
  
      ```c++                                         
         // allocated on the stack / globally:
         Stepper m1(0,1);
         Stepper m2(2,3);
         Stepper m3(4,5);
         Stepeper* stArray[] = {&m1, &m2, &m3};         
         controller.rotateAsync(stArray);
         
         // or, allocated dynamically:
         Stepper* stArray[] = { new Stepper(1,2), new Stepper(3,4), newStepper(5,6)};         
         controller.rotateAsync(stArray);
      ```
        

    returnType: void   
    parameter:
    - name: "stepperArray[N]"
      type: Stepper*

  - name: overrideSpeed
    shortDesc: >-
        This function is used to override the speed of all controlled motors on the fly. E.g. assume that the controller controls two motors m1, m2 with speeds of 20000 stp/s and 30000 
        respectively. A call to overrideSpeed(0.5f) would then decelerate m1 to 10000 stp/s and m2 to 15000 stp/s while keeping both motors in sync. Negative values will reverse the direction of the motors. 
    
        ```c++                        
           controller.rotateAsync(s1,s2,s3);
           delay(1000);         
           controller.overrideSpeed(0.5)  // reduce speed of all motors (s1, s2, s3) to 50% 
        ```
    returnType: void   
    parameter:
    - name: "factor"
      type: float

  - name: getCurrentSpeed 
    shortDesc: >-
        Returns the current speed of the controlled motor. If more than one motor is controlled by the controller it returns the current speed of the leading (fastest) motor. The speed of the other motors is always a fixed ratio to the speed of the leading motor.     

        ```c++                        
           controller.rotateAsync(s1);           
           while(controller.getCurrentSpeed() < 5000)
           {
              Serial.printf("pos: %d, speed: %d\n", s1.getPosition(), controller.getCurrentSpeed());
              delay(50);
           }
           controller.stop();           
        ```
    returnType: int32_t       

#---------------------------------------------------------------------------------------------------------------------------------------------------------- 
- name: Stopping Motors
  anchor: stopping
  methods:
  - name: stopAsync
    shortDesc: Decelerates the controlled motors to a stop. This function returns immediately after starting the stop sequence (non blocking). 
    returnType: void   

  - name: stop
    shortDesc: Decelerates the controlled motors to a stop and waits until the motors are stopped (blocking function).
    returnType: void   

  - name: emergencyStop
    shortDesc: Immediately stops the controlled motors. Use this only in emergency since, depending on motor speed this will probably lead to step losses. A homing sequence is highly recommended after a call to emergencyStop().
    returnType: void   
---

Objects of the RotateControl class are used to rotate up to 10 motors in sync. The synchronization is kept during acceleration and deceleration phases. The current rotation speed of the controlled motors can be overridden on the fly. 

  


