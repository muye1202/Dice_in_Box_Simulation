# Dice in Box Impact Simulation
Author: Muye Jia

Simulate the impact dynamics of a dice rolling around in a box using SymPy.

 
## Demo


https://user-images.githubusercontent.com/112987403/210473563-1d730af2-4fe6-4730-ac21-3abd55055b22.mp4


The simulation length, forcing function applied on the dice and the box can be changed.

## Behind the scene
The ”Dice” is consisted of two sticks and four spherical shaped mass (treated as point mass).
This project aims to simulate how would the jack and the box interact when
the box is spinning with the jack inside by numerically solving Euler-Lagrange
equation of the system and corresponding impact rules.

### Frames used in modelling
![image](https://user-images.githubusercontent.com/112987403/210476376-70c1133a-52b7-4202-b1be-8e437a458e14.png)

Figure 1: Frames used in simulation. bc represents box center frame; "dc" represents ”dice center”, "bc" represents "box center".

### Calculation of Euler-Lagrange equation
Euler-Lagrange equation with forcing term on the right-hand side is used:

![image](https://user-images.githubusercontent.com/112987403/210484844-0a330456-8aa2-4e3e-ae3f-54eadcdeecbf.png)

where q is the configuration variables of the system, and x y $\theta$ represents the x y positions of the box and dice related to the fixed frame, $\theta$ represents the angle between the box and dice's x axis and that of the fixed frame. Forcing F is user input external force. Only the equation of the corresponding configuration variable has forcing term on the right-hand side.

### Governing equation during impact
To solve for position and velocity after impact, the following equations are used:

![image](https://user-images.githubusercontent.com/112987403/210484695-381583a2-248d-4832-b45e-68db60fc6d01.png)

where $\tau^-\$ represents the instant before impact and $\tau^+\$ represents the instant after impact. $\phi$ on the right-hand side is the impact constraint describing the geometry of the system when impact happens.

### Use transformation matrices to calculate system energy
Tranformation matrices are used to calculate kinetic and potential energy of the system for efficiency (equation listed below).

![image](https://user-images.githubusercontent.com/112987403/210485381-3e1982db-01c1-4af1-9bf3-a36b40ad0051.png)

where $I_{nxn}$ is the system's inertia matrix, and $V^b$ is 6x1 vector called body velocity calculated using the following equation:

![image](https://user-images.githubusercontent.com/112987403/210485522-4499fcc4-4d98-4cfe-ab3a-8f2ffe542f91.png)

where $R^T$ is the transpose of the rotation matrices describing the system's rotation, and $p$ describes the translation of the system, $\omega$ is the system's spatial angular velocity; $g^-1$ is the inverse of the system's tranformation matrices, and $\dot{g}$ is its time derivative, whose product can be calculated this way:

![image](https://user-images.githubusercontent.com/112987403/210485923-cd5893cd-5cbd-49e1-882b-997e8e36d56c.png)


### Numeric integration
Runge-Kuta integration is used over Euler integration for better accuracy:

```
def integrate(f, xt, dt):
    k1 = dt * f(xt)
    k2 = dt * f(xt+k1/2.)
    k3 = dt * f(xt+k2/2.)
    k4 = dt * f(xt+k3)
    new_xt = xt + (1/6.) * (k1+2.0*k2+2.0*k3+k4)
    
    return new_xt
```

### Impact detection and update
Check the distance between the tips of the dice and each wall of the box, when the distance becomes zero, an impact happens.

When calculating the position of velocity of the system after impact, numerical values of all the terms in the symbolic solution of the governing equations are plugged in first instead of solving the symbolic solution directly, which can greatly reduce the time used to solve the impact equations

To check the implementation details, please refer to `impact_update` function to see how the impact equations are solved, and `simulate_with_impact` function to see how impact detection is incorporated into the simulation loop.


### Result animation
The solved trajectory of configuration variables q for the box and dice are then the input to the animation function, and the positions of each component of the system at each time step is calculated.

Please refer to `animate_boxndice` function to see the detailed implementation of the animation function.
