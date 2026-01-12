# Simulating the dynamics of a jack in a shaking box from scratch

<img src="Animation.gif" width="500" alt="Simulation of a Jack in a Shaking Box">


## Outer Box Frames

<img src="Box Frame.png" alt="Box Frames" >

The frame of the center of the outer box is obtained by translating the world frame to the center of mass and then rotating it by the box orientation.

## Jack Frames

<img src="Jack Frame.png" alt="Jack Frames" >

The frame of the center of the jack is found by translating the world frame to the jack’s center and then rotating it by the jack’s orientation. The jack frame is perpendicular to the jack arms.

The frames of the corners are first defined relative to the jack center, at a fixed distance along the diagonals. These corner frames are rotated by the jack orientation and then transformed into the world frame. Finally, they are expressed with respect to the box frame by applying the inverse of the box transform. This gives the corner positions relative to the box center.

## Kinetic Energy and Potential Energy

From the box and the jack center frames, the coordinates and their derivatives are calculated, which are then used to calculate the Kinetic and Potential Energy.

The inertia of both the box and the jack is calculated assuming they are rigid rectangular bodies (modeled as boxes).
and the mass is assumed to be concentrated at the centroid. 

Using these inertia and mass values, the kinetic energy of the box is
$$T_{\text{box}} = \frac{1}{2} M (\dot{x}^2 + \dot{y}^2) + \frac{1}{2} J \omega^2,$$
and the kinetic energy of the jack is
$$T_{\text{jack}} = \frac{1}{2} m (\dot{x}^2 + \dot{y}^2) + \frac{1}{2} j \omega^2.$$

The gravitational potential energies are
$$V_{\text{box}} = M g y, \qquad V_{\text{jack}} = m g y,$$
where $y$ is the vertical position of the corresponding center of mass.

## System Variables and Euler–Lagrange Equations

The system variables are defined as 
$[x_{\text{box}}, y_{\text{box}}, \theta_{\text{box}}, x_{\text{jack}}, y_{\text{jack}}, \theta_{\text{jack}}].$

The Lagrangian is defined as the difference between the total kinetic and total potential energy,
$$L = T_{\text{box}} + T_{\text{jack}} - (V_{\text{box}} + V_{\text{jack}}).$$

The Euler–Lagrange equation is written as
$$-\frac{\partial L}{\partial q} + \frac{d}{dt} \left( \frac{\partial L}{\partial \dot{q}} \right) = F,$$
where $q$ is the vector of system variables and $F$ is the force vector.

## Constraints

The motion is constrained so that all jack corners remain inside the outer box. The corner positions are first written in the outer box frame, and the following bounds are imposed:

$$x_{\text{box}} - \frac{L}{2} < x_{\text{corner}} < x_{\text{box}} + \frac{L}{2},$$
$$y_{\text{box}} - \frac{L}{2} < y_{\text{corner}} < y_{\text{box}} + \frac{L}{2}.$$

A ground constraint is also applied so that the box does not move below the floor:
$$y_{\text{box}} \geq \frac{L}{2}.$$

## External Force and Torque

To make the system move, the box is given a desired angle that changes with time,
$$\theta_{\text{desired}}(t) = \frac{\pi}{15} + \frac{\pi}{3} \sin^3\left(\frac{t}{2}\right).$$

A torque is applied to the box that tries to make its actual angle $\theta_{\text{box}}$ follow this desired angle,
$$\tau_{\text{box}} = -15\,\big(\theta_{\text{desired}} - \theta_{\text{box}}\big).$$

Because the jack is connected to the box, it feels a restoring torque,
$$\tau_{\text{jack}} = -\frac{j}{J} \, \tau_{\text{box}}.$$

In the vertical direction, the box is given a constant downward force $F_{\text{box}} = M a$, producing a vertical acceleration $a$. A corresponding constant force $F_{\text{jack}} = -m a$ is applied to the jack in the opposite direction, using the same acceleration $a$.

So, the resulting force vector is as follows:
$$F = [0, F_{\text{box}}, \tau_{\text{box}}, 0, F_{\text{jack}}, \tau_{\text{jack}}].$$

## Active Constraints and Impact

During the simulation, each constraint function $\phi_i(q)$ is evaluated at every time step. A constraint is treated as active when $|\phi_i(q)| < \varepsilon$, where $\varepsilon$ is a small threshold. All other (inactive) constraints have their associated Lagrange multipliers set to zero, $\lambda_i = 0$ for inactive constraints.

When an active constraint indicates a contact event, only the first such impact is resolved (multiple impacts occurring at exactly the same instant are not considered). The post-impact state is computed by enforcing momentum balance, geometric consistency of the constraint, and approximate energy conservation through the Hamiltonian.

Since physical systems typically experience a single dominant impact during a small time interval, processing one active constraint per time step is a reasonable simplification.

## Graph

<img width="1136" height="1936" alt="image" src="https://github.com/user-attachments/assets/c6136833-19f6-48ec-8874-cd456349d3ab" />

