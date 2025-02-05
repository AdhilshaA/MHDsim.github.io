P464 - MHD Project
---
- Implemented by [Adhilsha A](https://github.com/AdhilshaA)
- Last updated on *14-03-2024*

This website follows the updates on my project on MHD. The project is about the `study of the Galactic Dynamos`. Each task in the project gets one step closer to the final goal. The project is divided into 3 tasks.
________
## TABLE OF CONTENTS

- [P464 - MHD Project](#p464---mhd-project)
- [TABLE OF CONTENTS](#table-of-contents)
- [TASK 1: Solving the diffusion equation](#task-1-solving-the-diffusion-equation)
  - [1.1 Introduction](#11-introduction)
  - [1.2 Equation forms](#12-equation-forms)
  - [1.3 Boundary conditions](#13-boundary-conditions)
  - [1.4 Implementation](#14-implementation)
    - [1.4.1 Spatial derivative](#141-spatial-derivative)
    - [1.4.2 RK4 time stepping](#142-rk4-time-stepping)
  - [1.5 Constants and parameters for simulation](#15-constants-and-parameters-for-simulation)
    - [1.5.1 Study of evolution of Magnetic field strength](#151-study-of-evolution-of-magnetic-field-strength)
    - [1.5.2 Study of evolution of pitch angle.](#152-study-of-evolution-of-pitch-angle)
    - [1.5.3 Study of Magnetic field evolution with different seed fields and different boundary conditions](#153-study-of-magnetic-field-evolution-with-different-seed-fields-and-different-boundary-conditions)
- [TASK 2: (TBD)](#task-2-tbd)
________

## TASK 1: Solving the diffusion equation

### 1.1 Introduction

The first task is to solve the diffusion equation in 1D. The diffusion equation is given by:
\[ \frac{\partial \overline B_r}{\partial t} = - \frac{\overline V}{r} \frac{\partial}{\partial r}(r\overline B_r) -  \frac{\partial}{\partial z}(\overline V_z \overline B_r) -  \frac{\partial}{\partial z}(\alpha \overline B_{\phi}) + \eta_t \left[  \frac{\partial}{\partial r} \left( \frac{1}{r}  \frac{\partial}{\partial r}(r \overline B_r)\right) +  \frac{\partial ^2 \overline B_r}{\partial z^2} \right] \]

\[
\frac{\partial \overline B_{\phi}}{\partial t} = - q\Omega \overline B_{r} -  \frac{\partial}{\partial r}(\overline V_r \overline B_{\phi}) -  \frac{\partial}{\partial z}(\overline V_z \overline B_{\phi}) +  \frac{\partial}{\partial z}(\alpha \overline B_{r}) + \eta_t \left[  \frac{\partial}{\partial r} \left( \frac{1}{r}  \frac{\partial}{\partial r}(r \overline B_{\phi})\right) +  \frac{\partial ^2 \overline B_{\phi}}{\partial z^2} \right]
\]

where \(\overline B_r\), \(\overline B_{\phi}\) and \(\overline B_z\) are the components of the magnetic field, \(\overline V_r\), \(\overline V_{\phi}\) and \(\overline V_z\) are the components of the velocity field.

For this this task, we solve the diffusion equation omitting the \(\nabla \times (\overline V \times \overline B)\) and \(\alpha\) terms. The equation we have to solve becomes:
\[
\frac{\partial \overline B_r}{\partial t} = \eta_t \left[  \frac{\partial}{\partial r} \left( \frac{1}{r}  \frac{\partial}{\partial r}(r \overline B_r)\right) +  \frac{\partial ^2 \overline B_r}{\partial z^2} \right] \]

\[
\frac{\partial \overline B_{\phi}}{\partial t} = \eta_t \left[  \frac{\partial}{\partial r} \left( \frac{1}{r}  \frac{\partial}{\partial r}(r \overline B_{\phi})\right) +  \frac{\partial ^2 \overline B_{\phi}}{\partial z^2} \right]
 \]

Since both \(\overline B_r\) and \(\overline B_{\phi}\) have same form of equation, we generalize the form of the equation to:
\[
\frac{\partial \overline B}{\partial t} = \eta_t \left[  \frac{\partial}{\partial r} \left( \frac{1}{r}  \frac{\partial}{\partial r}(r \overline B)\right) +  \frac{\partial ^2 \overline B}{\partial z^2} \right]
\]

### 1.2 Equation forms

In case of \(\overline B_r(z)\) and \(\overline B_{\phi}(z)\), the equations become:
\[
\frac{\partial \overline B}{\partial t} = \eta_t \frac{\partial ^2 \overline B}{\partial z^2}
\]

In case of \(\overline B_r(r)\) and \(\overline B_{\phi}(r)\) under no-\(z\) appproximation, the equations become:
\[
\frac{\partial \overline B}{\partial t} = \eta_t \left[  \frac{\partial}{\partial r} \left( \frac{1}{r}  \frac{\partial}{\partial r}(r \overline B)\right) -  \frac{\pi ^2}{4 h^2}\overline B \right] \]

On expanding the equation, we get:
\[
\frac{\partial \overline B}{\partial t} = \eta_t \left[ \frac{\partial ^2 \overline B}{\partial r^2} +  \frac{1}{r}  \frac{\partial \overline B}{\partial r} - \frac{\overline B}{r^2}  -  \frac{\pi ^2}{4 h^2}\overline B \right]
\]

These are the equations to be solved in this task.

### 1.3 Boundary conditions

We will be using the simplest boundary conditions for this task. The boundary conditions are:
\[
\overline B_r = \overline B_{\phi} = 0 \quad \text{at} \quad (z = -h, h)\text{ or }(r = 0,r_{max}) \]

We will be taking dimensionless units with \(h = 1\), \(\eta_t = 1\).

In \(r\) implementation, we will be starting with \(r\) = 0.01 since the value \(r = 0\) is can possibly cause division by zero errors.

### 1.4 Implementation

For our implementation we will be using finite difference method for defining the spatial derivatives and RK4 for the time stepping / evolution. The code can be observed at [task1_code](task1_code.html).

#### 1.4.1 Spatial derivative

The spatial derivatives are calculated using the finite difference method. here, we are using 6th order finite difference method for the spatial derivatives. The 6th order finite difference method is given by:
\[
\frac{d f}{d x} = \frac{1}{60\delta x}\left( - f_{i-3} + 9f_{i-2} - 45f_{i-1} + 45f_{i+1} - 9f_{i+2} + f_{i+3} \right) \]
\[
\frac{d^2 f}{d x^2} = \frac{1}{180\delta x^2}\left( 2f_{i-3} - 27f_{i-2} + 270f_{i-1} - 490f_{i} + 270f_{i+1} - 27f_{i+2} + 2f_{i+3} \right) \]

Other orders can be used and is available for use in the code.

For the sake of obtaining smooth derivatives as well for the sake of maintaining the boundary conditions, we use `ghost zones`. By default, 'relative anti-symmetric' ghost zones are used where the ghost cell at \(i\) distance away from boundary \(b\) is given by:
\[ f_{b-i} = 2f_{b} - f_{b+i} \quad \text{at the start of the spatial domain} \]
\[ f_{b+i} = 2f_{b} - f_{b-i} \quad \text{at the end of the spatial domain} \]

where \(i = 1, 2, 3, ..., n\) (\(n\) is the half the order of the finite difference method).

Other ghost zones types, such as 'symmetric' and 'anti-symmetric' can also be used and is available for use in the code.

Using the 6th order finite difference method and 'relative anti-symmetric' ghost zones, a following magnetic field and the spatial derivatives are obtained (test case):

<div style="text-align:center;">
    <img src="figures\initial_condition_B.png" alt="Sample Field" />
</div>

<div style="text-align:center;">
    <div style="display:inline-block;">
        <img src="figures\first_spatial_derivative.png" alt="First Spatial Derivative" />
    </div>
    <div style="display:inline-block;">
        <img src="figures\second_spatial_derivative.png" alt="Second Spatial Derivative" />
    </div>
</div>


As you can see the spatial derivatives are smooth and the second derivative is zero at the boundaries as expected from the 'relative anti-symmetric' ghost zones. This works exceptionally well for the \(z\) implementation. For the \(r\) implementation, the ghost zones cannot help much in keeping the boundary conditions constant as the equation contains first and second derivatives as well as the function itself. Hence, the boundary conditions are not maintained well in the \(r\) implementation. This is a known issue and is being worked on.

#### 1.4.2 RK4 time stepping

The time stepping is done using the RK4 method. Our evolution function can be given of form:
\[
\frac{\partial \overline B}{\partial t} = f(\overline B, t) \]
Then, the RK4 method evolution for a single time step can be given by:
\[
\kappa_1 = \delta t f(\overline B, t) \]
\[
\kappa_2 = \delta t f(\overline B + \frac{\kappa_1}{2}, t + \frac{\delta t}{2}) \]
\[
\kappa_3 = \delta t f(\overline B + \frac{\kappa_2}{2}, t + \frac{\delta t}{2}) \]
\[
\kappa_4 = \delta t f(\overline B + k_3, t + \delta t) \]

\[
\overline B(t + \delta t) = \overline B(t) + \frac{1}{6}(\kappa_1 + 2\kappa_2 + 2\kappa_3 + \kappa_4) \]


The thing to note here about the generalized form, with regards to the RK4 method, is that:
- The spatial derivatives do not change values with the change (\(\overline B \rightarrow \overline B + \kappa/2\)) in the RK4 method. This is due to the symmetry of the spatial derivative equations. Hence, the spatial derivatives are calculated only once at the start of the time step. Thus, it is not mentioned in the equation form.

Our method, as mentioned above, is to find the spatial derivatives using finite difference method at a certain time step and then use the RK4 method to evolve the system to the next time step. This is done repetitively to get the solution at all time steps.

### 1.5 Constants and parameters for simulation

For \(z\) implementation, we will be using the following parameters:
- \(z\ \epsilon\ (-1, 1)\) `# range of z`
- \(t\ \epsilon\ [0, 1]\) `# range of time`
- \(N_z = 80\) `# number of spatial steps`
- \(N_t = 5000\) `# number of time steps`
- \(order = 6\) `# order of the finite difference method`
- *ghostzone_type* = 'relative anti-symmetric' `# type of ghostzone`

For \(r\) implementation, we will be using the following parameters:
- \(r\ \epsilon\ (0.01, 8.01)\) `# range of r`
- \(t\ \epsilon\ [0, 1]\) `# range of time`
- \(N_r = 100\) `# number of spatial steps`
- \(N_t = 4000\) `# number of time steps`
- \(order = 6\) `# order of the finite difference method`
- *ghostzone_type* = 'relative anti-symmetric' `# type of ghostzone`

The seed field used for the z-implementation is:

\[ B_{\phi}(z) = 
3 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 1 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 2 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]

whereas the seed field used for the r-implementation is:

\[ B_{\phi}(r) =
3 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 1 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 2 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]

The results are:

<!-- ![image](figures\evolution_B_phi_z.png)
![image](figures\evolution_B_phi_r.png) -->
<div style="text-align:center;">
    <img src="figures\evolution_B_phi_z.png" alt="Evolution of B_phi_z" />
</div>

<div style="text-align:center;">
    <img src="figures\evolution_B_phi_r.png" alt="Evolution of B_phi_r" />
</div>


#### 1.5.1 Study of evolution of Magnetic field strength

For this part, I am setting \(\overline B_r = 0\) and evolving \(\overline B_{\phi}\) over time. The magnetic field strength, \(|B_{\phi}|\), is given at different points in the spatial domain as a function of time. The results are (N.B. The decay rate is given in the legend off the plots):

<div style="text-align:center;">
    <img src="figures\B_phi_z_strength__timesteps.png" alt="Evolution of B_phi_z Strength Over Timesteps" />
</div>

<div style="text-align:center;">
    <img src="figures\B_phi_r_strength__timesteps.png" alt="Evolution of B_phi_r Strength Over Timesteps" />
</div>


from these results, taking the middle point in the spatial domain, the magnetic field strength, \(|B_{\phi}|\), is given as a function of time in *log*-scale. Then it can be fitted to find the `decay rate` as well. The results are:

<!-- ![B_phi_z_decay](figures\B_phi_z_decay.png)
![B_phi_r_decay](figures\B_phi_r_decay.png) -->
<div style="text-align:center;">
    <img src="figures\B_phi_z_decay.png" alt="B_phi_z Decay" />
</div>

<div style="text-align:center;">
    <img src="figures\B_phi_r_decay.png" alt="B_phi_r Decay" />
</div>

#### 1.5.2 Study of evolution of pitch angle.

For this part, I am setting the seed \(\overline B_r\) and \(\overline B_{\phi}\) to non-zero values and evolving them over time. The pitch angle is given by:
\[p = \tan^{-1}\left(\frac{\overline B_{r}}{\overline B_{\phi}}\right)\]

The seed fields taken for this simulation are, for the z-implementation:
\[
\text{{B}}_{\phi}(z) = 3 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 1 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 2 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]

\[
\text{{B}}_{r}(z) = 3 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 2.5 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 1 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]
and for the r-implementation:
\[
\text{{B}}_{\phi}(r) = 3 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 1 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 2 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]

\[
\text{{B}}_{r}(r) = 3 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 2.5 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 1 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]

The time evolution of the fields are given below for reference:

<!-- ![Bphi_Br_z_r_evolution](figures\Bphi_Br_z_r_evolution.png) -->

<div style="text-align:center;">
    <img src="figures\Bphi_Br_z_r_evolution.png" alt="Bphi_Br_z_r_evolution" />
</div>


The results of the pitch angle evolution at certain specific spatial steps were obtained as well. The results:

<!-- ![pitch_angle_z_timeevolution_at_diff_spatialsteps](figures\pitch_angle_z_timeevolution_at_diff_spatialsteps.png)
![pitch_angle_r_timeevolution_at_diff_spatialsteps](figures\pitch_angle_r_timeevolution_at_diff_spatialsteps.png) -->

<div style="text-align:center;">
    <img src="figures\pitch_angle_z_timeevolution_at_diff_spatialsteps.png" alt="pitch_angle_z_timeevolution_at_diff_spatialsteps" />
</div>

<div style="text-align:center;">
    <img src="figures\pitch_angle_r_timeevolution_at_diff_spatialsteps.png" alt="pitch_angle_r_timeevolution_at_diff_spatialsteps" />
</div>

To get another perspective, the pitch angle variation over spatial domain at different time steps is also found. The results are:

<!-- ![pitch_angle_z_spatialevolution_at_diff_timesteps.png](figures\pitch_angle_z_spatialevolution_at_diff_timesteps.png)
![pitch_angle_r_spatialvariation_at_diff_timesteps](figures\pitch_angle_r_spatialevolution_at_diff_timesteps.png) -->

<div style="text-align:center;">
    <img src="figures\pitch_angle_z_spatialevolution_at_diff_timesteps.png" alt="pitch_angle_z_spatialevolution_at_diff_timesteps" />
</div>

<div style="text-align:center;">
    <img src="figures\pitch_angle_r_spatialevolution_at_diff_timesteps.png" alt="pitch_angle_r_spatialevolution_at_diff_timesteps" />
</div>

#### 1.5.3 Study of Magnetic field evolution with different seed fields and different boundary conditions

Here, I did two trials, first one with zero boundary conditions. Then, the second one with non-zero boundary conditions and different seed fields from the previous trial. 

The trial 1 seed field with zero boundary conditions is, for the z-implementation:
\[
B_{\phi}^z(z) = 3 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 1 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 2 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]

\[
B_{r}^z(z) = 3 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 2.5 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 1 \sin \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]
and for the r-implementation:
\[
B_{\phi}^r(r) = 3 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 1 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 2 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]

\[
B_{r}^r(r) = 3 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 2.5 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 1 \sin \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]
The results are:

<!-- ![Bphi_Br_z_r_evolution_trial1](figures\Bphi_Br_z_r_evolution_trial1.png) -->

<div style="text-align:center;">
    <img src="figures\Bphi_Br_z_r_evolution_trial1.png" alt="Bphi_Br_z_r_evolution_trial1" />
</div>

Here, with zero boundary conditions, the boundaries are maintained well in the z-implementation. THe reason for this is 'relative anti-symmetric' ghost zones which keeps the \(\frac{\partial^2 \overline B}{\partial z^@}\) zero at the boundaries.

However, the boundaries are not maintained well in the r-implementation, even though the it is hardly noticeable in the plots. This is because the magnetic field is decaying and the boundary condition is already zero. Hence, the boundary condition is 'seen' to be maintained well. But, the difference is noticeable in the next trial.

With the different seed fields used in the implementations, all magnetic field are seen to decay with time, as expected.

The trial 2 seed field with non-zero boundary conditions is, for the z-implementation:
\[
B_{\phi0}^z(r, \phi, z) = 3 \cos \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 1 \cos \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 2 \cos \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]

\[
B_{r0}^z(r, \phi, z) = 3 \cos \left( \frac{{z - z_i}}{{z_f - z_i}} \pi \right) + 2.5 \cos \left( \frac{{z - z_i}}{{z_f - z_i}} 2\pi \right) + 1 \cos \left( \frac{{z - z_i}}{{z_f - z_i}} 5\pi \right)
\]
and for the r-implementation:
\[
B_{\phi0}^r(r, \phi, z) = 3 \cos \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 1 \cos \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 2 \cos \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]

\[
B_{r0}^r(r, \phi, z) = 3 \cos \left( \frac{{r - r_i}}{{r_f - r_i}} \pi \right) + 2.5 \cos \left( \frac{{r - r_i}}{{r_f - r_i}} 2\pi \right) + 1 \cos \left( \frac{{r - r_i}}{{r_f - r_i}} 5\pi \right)
\]
The results are:

<!-- ![Bphi_Br_z_r_evolution_trial2](figures\Bphi_Br_z_r_evolution_trial2.png) -->

<div style="text-align:center;">
    <img src="figures\Bphi_Br_z_r_evolution_trial2.png" alt="Bphi_Br_z_r_evolution_trial2" />
</div>

Here in \(z\) implementation, even the non-zero boundaries are maintained well as expected. 

As you can see in the \(r\) implementation, the non-zero boundary conditions are not maintained at all. This is because the ghost zones cannot help much in keeping the boundary conditions constant as the equation contains first and second derivatives as well as the function itself. The 'relative anti-symmetric' ghost zones can only keep the second derivative zero at the boundaries. 

THe decay with of magentic field with time is evident here, even with different seed fields used in the implementations.

________

## TASK 2: (TBD)