# Software

Software is an essential part in a robot's success. From autonomous to tele-op, to kinematics and vision, good software is often a major factor in whether your bot performes well or not.

## Swerve drive kinematics

It may seem complicated, but the workings of a swerve bot are relatively simple.
Here are the concepts that we're assuming you understand already. If you don't, then
please take some time to do some brief research.

* Vectors and vector math (addition + subtraction)
* Basics of forces (velocity and force)
* The premise behind a swerve bot
* Trigonometry (identities and formulas)
* How to take joystick values (direction and magnitude)

The basis behind our kinematics is creating a velocity vector for each wheel. The vector
points in the direction of the wheel and the size of the vector indicates the magnitude of
the velocity. There are two vectors for each wheel that need to be combined: the strafing vector and the rotation vector. Throughout the code, we keep the vectors in component form for simplicity.

### The strafing vector

When we want to strafe, note that each wheel points in the same direction:

***Add diagram of strafing wheels with their respective direction***

Thus, the direction of each wheel just becomes whatever direction the joystick is facing. 
It's so simple!

Unfortunately, it's not that simple. This only works in robot-centric mode, where "left" and "right" directions are considered from the perspective of the robot. However, with swerve, you often are facing a different direction than the robot and so this quickly becomes a greivance.

***Add diagram of the bot strafing left facing in two different directions (robot-centric)***

The alternative is to use field-centric driving, which makes it so that "your left" is always the "bot's left", no matter which direction the robot is facing. To adjust for this, we need to get the angle from our gyroscope and adjust the strafing direction.

***Add diagram of the bot strafing left facing in two different directions (field-centric)***

Thus, the vector of our strafing has an angle of our joystick minus the angle of our gyroscope. In component form, the strafing vector is (The strafing vector is S):

$$
    S_{x} = cos(joystickAngle - gyro)
$$

$$
    S_{y} = sin(joystickAngle - gyro)
$$

Using our subtraction identities, we can derive the following equations for our code.

$$
    S_{x} = (cos(joystickAngle) * cos(gryo)) - (sin(joystickAngle) * sin(gyro)) 
          = (joyStick_x * cos(gyro)) - (joyStick_y * sin(gyro))
$$

$$
    S_{y} = (cos(joystickAngle) * sin(gyro)) + (sin(joystickAngle) * cos(gyro))
          = (joyStick_x * sin(gyro)) +  (joyStick_y * cos(gyro))
$$

where `joystickX` and `joystickY` are the X and Y magnitudes of our joystick respectively.

Of course, this is all in units from 0-1 (or rather, unitless). This can be given directly to the motor speeds, or if you need the values in terms of m/s, you can multiply the vector by your maximum velocity.

### The Rotation Vector

A swerve drive can rotate, strafe, or do both at the same time! in order to handle rotation, we need one more vector. Thankfully, this one is takes much less work to compute than the previuos vector.

***Add diagram of roation vectors on each wheel***

The direction of the rotation vectors doesn't change, rather the but rather the magnitude. The magnitude is determined by the Right Joysticks y value (r_joy_y). We can say that for each wheel,

$$
R_{1} = [ rJoyy, π/4 ]
$$

$$
R_{2} = [ rJoyy, 7π/4 ]
$$

$$
R_{3} = [ rJoyy, 5π/4 ]
$$

$$
R_{4} = [ rJoyy, 3π/4 ]
$$

### Conclusion

We now have our rotation and strafing vectors. All that's left for us to do is convert each one to component form and add them together to form the final velocity vector for each wheel.

