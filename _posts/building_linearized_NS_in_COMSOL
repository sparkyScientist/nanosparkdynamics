---
title: "Building a Linearized Navier–Stokes Stability Solver in COMSOL Using the General PDE Interface"
date: 2025-01-15
tags: [CFD, COMSOL, Stability, Linearized Navier-Stokes, CNT-reactor]
---

# Building a Linearized Navier–Stokes Stability Solver in COMSOL Using the General PDE Interface

Understanding when a steady flow becomes unsteady is important for many fluid systems.  
In floating-catalyst CNT reactors, for example, oscillations in the velocity and temperature fields can influence mixing, catalyst exposure, and ultimately CNT growth. Traditional CFD workflows give the steady solution, but they rarely provide direct information about whether that solution is *stable*.  

Linear stability analysis addresses this question by examining how small perturbations behave around the steady flow. If a perturbation decays, the base flow is stable. If it grows, it indicates the onset of oscillations or symmetry breaking.  

Most commercial CFD software does not expose the linearized Navier–Stokes operator needed for this analysis. In this post, I describe how I constructed a Linearized Navier–Stokes (LNS) stability solver *entirely inside COMSOL* using the **General Form PDE** interface. This approach relies on defining velocity and pressure perturbations explicitly, assembling the linearized operator by hand, and using COMSOL’s eigenvalue solver to compute growth rates and mode shapes.

The goal is to explain the method clearly enough that a non-specialist can follow the reasoning, while retaining enough mathematical detail for readers familiar with stability theory.

---

# 1. Motivation: Why Study Flow Stability?

Steady CFD simulations are widely used to characterize the base flow in reactors. However, many experimental systems exhibit unsteady behavior that emerges only above certain operating conditions. In CNT reactors, oscillations are often reported near the injector region or in buoyancy-driven recirculation zones. These fluctuations may affect catalyst residence time or CNT morphology.

A direct transient simulation can sometimes capture these oscillations, but the results depend on simulation time, mesh resolution, and the solver’s ability to remain numerically stable. Linear stability analysis provides a more systematic way to explore these flow transitions.

Given a steady flow field $ \mathbf{U}(\mathbf{x}) $, linear stability asks:

- *If we introduce a small perturbation, does it grow or decay?*
- *If it grows, what is its spatial structure?*
- *At what Reynolds number does the behavior change?*

To answer this, we linearize the Navier–Stokes equations around the steady solution. The resulting system is an eigenvalue problem, whose solutions indicate whether the flow is stable or unstable.

---

# 2. Linearizing the Navier–Stokes Equations

Let the full velocity be:

$$
\mathbf{u}(\mathbf{x}, t) = \mathbf{U}(\mathbf{x}) + \mathbf{u}'(\mathbf{x}, t),
$$

where $\mathbf{U}$ is the steady base flow and $\mathbf{u}'$ is a small perturbation.  
Similarly, the pressure is:

$$
p(\mathbf{x}, t) = P(\mathbf{x}) + p'(\mathbf{x}, t).
$$

Substituting into the incompressible Navier–Stokes equations and neglecting nonlinear terms in $\mathbf{u}'$ yields the **linearized system**:

$$
\rho \frac{\partial \mathbf{u}'}{\partial t}
+ \rho (\mathbf{U} \cdot \nabla) \mathbf{u}'
+ \rho (\mathbf{u}' \cdot \nabla) \mathbf{U}
= -\nabla p' + \mu \nabla^2 \mathbf{u}',
$$

$$
\nabla \cdot \mathbf{u}' = 0.
$$

To study stability, we assume exponential time dependence:

$$
\mathbf{u}'(\mathbf{x},t) = \hat{\mathbf{u}}(\mathbf{x}) e^{\lambda t}, \quad
p'(\mathbf{x},t) = \hat{p}(\mathbf{x}) e^{\lambda t}.
$$

This converts the PDE into a generalized eigenvalue problem:

$$
\lambda M \hat{\mathbf{q}} = K \hat{\mathbf{q}},
$$

where $\hat{\mathbf{q}} = [\hat{u}, \hat{v}, \hat{w}, \hat{p}]^T$.  
The sign of $\operatorname{Re}(\lambda)$ determines stability.

This formulation is well established in hydrodynamic stability literature. The main challenge is implementing it inside a commercial package where this operator is not natively exposed.

---

# 3. Why the General PDE Interface?

COMSOL’s Laminar Flow interface solves the full nonlinear equations but does not provide the linearized operator for stability studies.

The General Form PDE interface, on the other hand, gives complete control over:

- The mass matrix
- Diffusion fluxes
- Pressure–velocity coupling
- First-derivative terms (advection and shear)
- Continuity constraints
- Boundary conditions

This flexibility is what enables a fully custom LNS implementation.

---

# 4. Setting Up the Perturbation Variables

We introduce the perturbation fields:

- `up`, `vp`, `wp` — velocity perturbations in $x$, $y$, $z$
- `pp` — pressure perturbation

Although these variables have different physical units, the General Form PDE treats them as mathematical fields, which is sufficient for constructing the stability operator.

In COMSOL:

1. Add **General Form PDE** to the same component as the Laminar Flow physics.
2. Set the **dependent variables** to: up, vp, wp, pp
3. Use dimensionless units (`1`) for simplicity.
4. Apply the PDE to the same domain as the base flow.

---

# 5. Encoding the Linearized Navier–Stokes Operator

The General Form PDE uses the structure:

$$
d_a \frac{\partial u}{\partial t}
+ \nabla \cdot \Gamma = f.
$$

Each term maps to a physical part of the LNS equations.

## 5.1 Mass Matrix

We want:

- Velocities to contribute to the eigenvalue mass matrix ($d_a = \rho$)
- Pressure to act as a constraint ($d_a = 0$)

In COMSOL:

- Set $d_a = \rho$ on the first three diagonal terms
- Set $d_a = 0$ on the pressure equation

This defines the generalized eigenvalue structure.

---

## 5.2 Diffusion and Pressure Gradient (Conservative Flux)

The flux for each momentum equation is:

$$
\Gamma = 
\begin{bmatrix}
-\mu \nabla u_p - p_p \hat{e}_x \\
-\mu \nabla v_p - p_p \hat{e}_y \\
-\mu \nabla w_p - p_p \hat{e}_z
\end{bmatrix}.
$$

and for continuity:

$$
\Gamma_4 = 
\begin{bmatrix}
u_p, \; v_p, \; w_p
\end{bmatrix}
$$

which yields:

$$
\nabla \cdot \Gamma_4 = 
\frac{\partial u_p}{\partial x} 
+ \frac{\partial v_p}{\partial y}
+ \frac{\partial w_p}{\partial z} = 0.
$$

These fluxes enforce both viscous diffusion and incompressibility.

---

## 5.3 Convection by the Base Flow

The terms $(\mathbf{U} \cdot \nabla)\mathbf{u}'$ expand to:

$$
U_x \frac{\partial u_p}{\partial x}
+ U_y \frac{\partial u_p}{\partial y}
+ U_z \frac{\partial u_p}{\partial z},
$$

and similarly for $v_p$ and $w_p$.

Because COMSOL’s flux form does not allow first derivatives of one variable to appear directly in another variable’s flux, these convective terms are included in the source term:

$$
f_1 = -\rho \big( U_x u_{px} + U_y u_{py} + U_z u_{pz} \big),
$$

and analogous expressions for $f_2$ and $f_3$.

---

## 5.4 Shear Terms

The perturbation interacts with gradients of the base flow:

$$
(\mathbf{u}' \cdot \nabla) U_x
=
u_p \frac{\partial U_x}{\partial x}
+ v_p \frac{\partial U_x}{\partial y}
+ w_p \frac{\partial U_x}{\partial z},
$$

and similarly for $U_y$ and $U_z$.

These enter the source term as well.  
Including them is important because shear often drives instabilities in buoyancy-affected reactor flows.

---

# 6. Boundary Conditions for Perturbations

To study natural modes of the system, we impose **homogeneous** (zero-value) perturbations:

- **Walls:**  
  $u_p = v_p = w_p = 0$  
- **Inlet:**  
  No incoming perturbation: $u_p = v_p = w_p = 0$  
- **Outlet:**  
  Natural condition for velocities; pressure perturbation may be fixed to zero for reference.

This ensures the eigenmodes represent intrinsic dynamics of the flow, not forced disturbances.

---

# 7. Solving the Eigenvalue Problem

The workflow in COMSOL:

1. **Study 1:** Solve the steady base flow with the Laminar Flow interface.
2. **Study 2:** Set up an **Eigenvalue** study.
3. In the eigenvalue study:
   - Include only the General Form PDE.
   - Use the **Study 1 solution** for base-flow variables.
   - Search for eigenvalues near zero.
4. Visualize eigenmodes in COMSOL using slice or isosurface plots.

Depending on the mesh size, these solves can be computationally intensive.  
The number of degrees of freedom in full 3D problems can exceed several million, and the solver may require out-of-core memory.

---

# 8. Why This Approach Is Useful for CNT Reactors

Linearized stability analysis may help identify:

- Conditions where buoyancy-driven recirculation becomes unsteady
- Oscillation frequencies that may correlate with probe or optical measurements
- Flow regions where perturbations preferentially grow or decay
- How injection conditions influence global reactor behavior

Although this implementation focuses on the mathematical structure rather than specific CNT chemistry, it provides a way to systematically explore transitions that may affect residence time, catalyst distribution, or heat transfer.

This type of analysis complements transient CFD and may reduce the need for long, expensive unsteady simulations.

---

# 9. Limitations and Future Improvements

Some important constraints include:

- The analysis is based on the **linearized** equations; nonlinear effects are not captured.
- The accuracy depends on mesh resolution and the quality of the base flow.
- Out-of-core matrix factorization can introduce significant computational cost.
- Further work is needed to map specific reactor parameters (e.g., Re, temperature gradients) to experimentally observed flow transitions.

Despite these limitations, constructing the stability operator directly in COMSOL offers a flexible and extensible workflow for analyzing flow instabilities in complex reactor geometries.

---

# 10. Reflections

Building the linearized Navier–Stokes operator manually in COMSOL requires attention to detail. It helped me understand how different physical mechanisms—diffusion, convection, shear, and incompressibility—enter the linearized system. The process also highlighted how commercial CFD software can be adapted to explore research questions that go beyond steady-state solutions.

This approach may be useful for others studying hydrodynamic stability in reactors or other engineering systems where flow transitions matter and where traditional CFD tools offer limited access to the underlying linearized operator.

