---
hidden: true
---

# Spring box

A box for relation constraint ![This is an image](../../plugins/springbox.jpg)

Spring box math based on fundamental formule F = -kx

[A short demo video on Youtube](http://youtu.be/rAToQEmg\_LY)

[Video how to create a smooth follow camera](https://youtu.be/pmAZOUQBYms?si=OQ71FkckdX\_BL8kn)

[Sample scene - test\_springBox.fbx](../../../MB\_Scenes/test\_springBox.fbx)

| Parameter  | Description                                                                                                                                                                                                                                                                     | Default Value |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| Stiff      | stiffness of the springs (the higher the value, the more rigid) Hardness is the reaction and the effect of the spring from which she draws a point in the original position.                                                                                                    | 0.1           |
| Damp       | the size of fading fluctuations spring (the more, the less hesitation commit spring) The essence of this parameter under a big question, in practice, it gives some the wrong result, so sometimes fading fluctuations can be regarded as a constant value of 0.10 for example. | 0.0           |
| Length     | the length of the spring (should be set to 0) A value of zero reference position is the point of going, otherwise the spring will be busy trying to position a specified distance from moving                                                                                   | 0.0           |
| Mass       | the mass. The more weight the more difficult it is to move the spring with a space or vice versa to return it to its original position.                                                                                                                                         | 1.0           |
| Friction   | force of resistance that allows “stifle” the dynamics of the spring and return it to its original position. The higher the number the less the effect of spring ”                                                                                                               | 0.1           |
| EvalSteps  | number of spring evaluation substeps per second                                                                                                                                                                                                                                 | 30.0          |
| RealTime   | checkbox to specify spring evaluation time. When checkbox is on, spring uses system time and play in realtime. When it’s off, spring uses player control local time from time slider (this is for plotting and animation playback).                                             | on (1.0)      |
| Zero Frame | frame on which we reset our simulation (for non realtime evaluation). This is when we rewind our animation to the very begining and want to start simulation from that “zero” point                                                                                             | 0 (frame 0)   |
| Pos        | input position                                                                                                                                                                                                                                                                  |               |
| Result     | output position                                                                                                                                                                                                                                                                 |               |
