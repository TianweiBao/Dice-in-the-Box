# ME314 Machine Dynamics Final Project - Jack in the Box
This is the repository of the final project of ME314 Machine Dynamics at Northwestern University

## Video Demo
<p align = "center">
    <img align="center" src="Animation\dice_in_the_box.gif">
</p>

## Overview
This project aims to model the multi-body Lagrangian dynamics of a dice colliding within a rotating box from scratch. The simulation is implemented in Python using SymPy for symbolic computation and NumPy for numerical computation. The simulation involves formulating impact updates and applying constrained Euler-Lagrange equations to account for the collision and physical constraints between the dice and the box. The animation is created using plotly package and the simulation results are visualized using Matplotlib. The simulation results are validated by comparing the numerical results with the theoretical predictions obtained from the Lagrangian dynamics equations. As shown by the video demo, the dice and the box both follow the expected trajectories, demonstrating the accuracy of the simulation model.
## Dynamics Model Design
<img src="Design\design.jpg"/>
There are **11** frames in total:
1. W : World frame (reference frame) 
2. b0 ~ b4 : Box frames 
3. j0 ~ j4 : Dice(Jack) frames

**Note:** 
1. b0 and j0 are defined to be the center mass of the box and dice respectively.
2. b1 ~ b4 are 4 vertices of the box in clockwise order.
3. j1 ~ j4 are 4 vertices of the dice in clockwise order.

## Configurations
1. Position(x, y, &theta;): <br>
    - Box = [0,&nbsp; 0,&nbsp; &pi;/2] <br>
    - Dice = [2.5,&nbsp; 2.5,&nbsp; 0] <br>
2. Mass: <br>
    - Box = 10 <br>
    - Dice = 1 <br>
3. Length(distance from center mass to 4 edges): 
    - Box = 5 <br>
    - Dice = 2 <br>

4. Velocity(&#x1E8B;, &#7823;, θ&#729;): <br>
    - Box = [-5,&nbsp; 0,&nbsp; 0] <br>
    - Dice = [0,&nbsp; 0,&nbsp; 0] <br>

## Simulation Steps 
1. *Rigid Body Transformations:* <br>

    - The combinations of **g_Wb0, g_b0b1, g_b0b2, g_b0b3, g_b0b4** are used to quantify the movement of the 5 frames of the box relative to the World Frame
    - The combinations of **g_Wj0, g_j0j1, g_j0j2, g_j0j3, g_j0j4** are used to quantify the movement of the 5 frames of the dice relative to the World Frame
    - The combinations of **g_b1b0, g_b2b0, g_b3b0, g_b4b0, g_b0j0, g_j0j1, g_j0j2, g_j0j3, g_j0j4** are used to quantify the movement of vertices of the dice relative to the 4 vertices of the box in order to set up the constraints
    - The combinations of **g_b1j1, g_b1j2, g_b1j3, g_b1j4** are used to measure the position of 4 vertices of the dice relative to the b1 frame of the box
    - The combinations of **g_b2j1, g_b2j2, g_b2j3, g_b2j4** are used to measure the position of 4 vertices of the dice relative to the b2 frame of the box
    - The combinations of **g_b3j1, g_b3j2, g_b3j3, g_b3j4** are used to measure the position of 4 vertices of the dice relative to the b3 frame of the box
    - The combinations of **g_b4j1, g_b4j2, g_b4j3, g_b4j4** are used to measure the position of 4 vertices of the dice relative to the b4 frame of the box

2. *Euler–Lagrange equations:* <br>

    - Step&nbsp;1: &nbsp;Based on the formula shown below, the body velocities for both the box and the dice are calculated. Specifically, the body velocities for the box and the dice are calculated by applying the unhat function to the **inv(g_wb0)\*dot(g_wb0)** and **inv(g_wj0)\*dot(g_wj0)** respectively. Since gravity is acting in a direction(z-direction in this case) perpendicular to the plane of my interest, there is no change of gravitational potential energy for motion within the plane.
    <img src="Formulas\Body_Velocity.jpg"/>

    - Step&nbsp;2: &nbsp;The spatial inertia matrices for the box and the dice are calculated using the formula shown below, where m is the total body mass, I<sub>3</sub> is a 3x3 identity matrix,[r] is the 3x3 skew-symmetric matrix formed from the center of mass position vector r, and J is the 3x3 rotational inertia matrix about the center of mass.
    <img src="Formulas\Inertia_Matrix.jpg"/>

    - Step&nbsp;3: &nbsp;The rotational kinetic energy and potential energy for the box and the dice are calculated. Given my assumptions made in the step 1, the rotational potential energy are zeros for both the box and the dice. Since the body velocities and inertia matrices for the box and the dice are already calculated in step 1 and step 2, we can easily get the rotaional kinetic energy by using the formula shown below. 
    <img src="Formulas\Rotational_KE.jpg"/>

    - Step&nbsp;4: &nbsp;After getting the rotational kinetic energy and potential energy, we can construct the Lagrangian of the system using L = KE - PE.

    - Step&nbsp;5: &nbsp;We then find the Euler Lagrange equations based on the Lagrangian constructed in step 4. The formulation of Euler Lagrange equations are shown below for reference, where Q is the external force acting on the system. Regarding the external force, since I want to simulate the movement of shaking the dice back and forth horizontally on the table, I apply the external force sin(t\*&pi;)\*K on the system, where the specific value of K is chosen through trial and error for the best visual effect and t is the time variable. Thus, by constructing the Euler Lagrange equations, we can easily solve q&#x0308;
    <img src="Formulas\EL_Eqns.jpg"/>

3. *Impact Updates:* <br>

    - Step&nbsp;1: &nbsp;We first need to take the constraint equations into account. Since the dice should be physically constrained inside the box, the movement of the dice in both x and y direction relative to the 4 vertices of the box cannot transgress the the real-time locotions of the 4 vertices of the box. For the details of the constraint equations, please refer to variable "phi_sym" in the code.

    - Step&nbsp;2: &nbsp;The general formula for the impact update is shown below. Since L (Lagrangian of the system), q, and q&#x0307; are already calculated in the previous steps, we first calculate dLdqd, dphidq, H (the Hamiltonian which is dLdqd\*qd - L(q, qd)). Then, we need to create dummy variables for pre-impact(&#964;-) and post-impact(&#964;+) states. Thus, the left-hand side of the equations are the differences of dLdqd and H at &#964;- and &#964;+. The right-hand side of the equations are &lambda;\*dphidq and 0. By solving the equations, we can get and solve the impact update equations.
    <img src="Formulas\Impact_Update_Eqns.jpg"/>

## Results Visualization

<html>
<head>
    <title>Images Side by Side</title>
    <style>
        .image-container {
            display: flex;
        }
        .image-container img {
            margin-right: 0px; /* Optional: space between images */
        }
    </style>
</head>
<body>
    <div class="image-container">
        <img src="Trajectories\box_positions.jpg" width="468">
        <img src="Trajectories\dice_positions.jpg" width="468">
    </div>
    <div class="image-container">
        <img src="Trajectories\box_velocities.jpg" width="468">
        <img src="Trajectories\dice_velocities.jpg" width="468">
    </div>
</body>
</html>

## Summary

The project highlights the application of multi-body Lagrangian dynamics, rigid body transformations, and impact updates in a 6-DOF system. This simulation model can be used to study the dynamics of the dice in the box and analyze the effect of different initial conditions on the system's behavior. The simulation can be further extended to include additional features such as frictional forces, external torques, and complex geometries to enhance the realism of the model. Overall, this project provides a hands-on experience in developing a physics-based simulation model and analyzing the dynamic behavior of a complex mechanical system.

