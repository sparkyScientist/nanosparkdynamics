---
layout: post
title: "The Dimensionality Dilemma in CFD: How 2D and 3D Models Reshape Jet Stability and Flow Physics"
date: 2025-10-07
image: "assets/Jet_dimensionality.png"
image_alt: "Comparison of jet behavior across 2D and 3D simulations"
summary: "A detailed and mathematically grounded discussion of how dimensionality filters instability modes and constrains the physics the Navier–Stokes equations are allowed to express."
---

## Introduction

One of the most important choices in any CFD workflow is dimensionality.  
It seems like a technical decision at first. Use 2D to save time, switch to 3D only when needed.  
But the more I have worked on mixed convection, jet stability, and FCCVD reactor flows, the more obvious it becomes that dimensionality fundamentally reshapes the physics.

When we go from 3D to 2D, we are not simply reducing the computational domain.  
We are modifying the mathematical operator that governs the flow.  
We are removing derivatives, velocity components, and therefore entire stability pathways.  

This post is my attempt to lay out, as clearly as possible, how 2D planar, 2D axisymmetric, and 3D models differ, what each can and cannot represent, and why these choices matter so much for asymmetric jets, buoyancy driven vortices, and FCCVD transport phenomena.

I care about this topic because I’ve seen how easy it is to misinterpret a “symmetric” 2D solution or to misclassify a Coandă deflection as numerical noise. Once you understand how the reduced equations work, these behaviors make complete sense.

---

## Governing Equations and What Dimensionality Removes

Everything starts from the incompressible Navier–Stokes equations.

**Continuity**
$$
\nabla \cdot \mathbf{u} = 0
$$

**Momentum**
$$
\rho\left(\frac{\partial \mathbf{u}}{\partial t} + \mathbf{u}\cdot\nabla\mathbf{u}\right)
= -\nabla p + \mu\nabla^2 \mathbf{u} + \mathbf{f}_b
$$

Once we apply dimensional assumptions like  
$$
\frac{\partial}{\partial z}=0,\ w=0
\quad\text{or}\quad
\frac{\partial}{\partial \theta}=0,\ u_\theta=0,
$$
we modify both:

- the differential operators, and  
- the space of admissible solutions.  

This restricts the **instability modes** and even the **symmetry breaking pathways** the flow can take.

I like to think of dimensionality as a filter applied to the Navier–Stokes operator.

---

## 3D: The Full Physics and the Full Instability Spectrum

![Dimensionality filtering diagram]({{ 'assets/COMSOL_post5_f1.png' | relative_url }})

*Figure 1. Dimensionality filtering applied to the Navier–Stokes equations. Going from 3D to axisymmetric removes all azimuthal dependence and all \(m \neq 0\) modes. Going from axisymmetric to 2D planar removes out-of-plane gradients and vortex-stretching terms.*
In 3D cylindrical coordinates \((r,\theta,z)\), velocity is  
$$
\mathbf{u} = (u_r, u_\theta, u_z),
$$
and continuity is  
$$
\frac{1}{r}\frac{\partial (r u_r)}{\partial r}
+ \frac{1}{r}\frac{\partial u_\theta}{\partial \theta}
+ \frac{\partial u_z}{\partial z} = 0.
$$

The key term that survives only in 3D is vortex stretching:
$$
\boldsymbol{\omega} \cdot \nabla \mathbf{u}.
$$

This term drives:

- helical instabilities  
- jet flapping  
- azimuthal drift of buoyant plumes  
- the classical turbulence cascade  

The 3D stability spectrum contains all azimuthal modes \(m = 0, 1, 2, \dots\).  
This is why 3D is the only setting where we see the familiar \(m=1\) helical jet instability.

---

## Axisymmetric 2D: The \(m=0\) World

![Instability mode comparison]({{ 'assets/COMSOL_post5_f2.png' | relative_url }})

*Figure 2. Instability mode illustration. A 3D system admits all azimuthal modes \(m = 0,1,2,\dots\). Axisymmetric modeling retains only \(m=0\). Planar and 3D models allow modes \(m \ge 1\), enabling lateral deflection and symmetry breaking.*
Axisymmetry enforces
$$
\frac{\partial}{\partial \theta}=0, \quad u_\theta=0.
$$

The consequence is that all perturbations of the form  
$$
e^{i m \theta},\quad m\neq 0
$$
are removed from the operator.

Continuity becomes:
$$
\frac{1}{r}\frac{\partial (r u_r)}{\partial r}
+ \frac{\partial u_z}{\partial z} = 0.
$$

This is why axisymmetric simulations can only produce **varicose** (breathing) modes.  
They completely suppress:

- helical motion  
- flapping  
- any lateral instability  
- buoyancy-induced azimuthal drift  

Axisymmetric CFD is powerful for mean flow predictions.  
But if the real flow is asymmetric, axisymmetric modeling cannot reveal it.  
The equations do not allow that solution.

---

## Planar 2D: Limited Physics, But Free Symmetry Breaking

Planar simulations impose  
$$
\frac{\partial}{\partial z}=0, \quad w=0.
$$

Continuity becomes:
$$
\frac{\partial u}{\partial x} + \frac{\partial v}{\partial y}=0.
$$

The vorticity reduces to a scalar:
$$
\omega = \frac{\partial v}{\partial x} - \frac{\partial u}{\partial y}.
$$

The vorticity transport equation is:
$$
\frac{D\omega}{Dt} = \nu\nabla^2 \omega.
$$

There is no vortex stretching because  
$$
(\omega\cdot\nabla)\mathbf{u} = 0.
$$

Despite this limitation, planar simulations *permit* lateral asymmetry.  
They are free to break symmetry in the plane because nothing constrains the solutions to remain centered.

This is why planar jets exhibit a pitchfork bifurcation.

---

## The Pitchfork Bifurcation: Why 2D Jets Pick a Side

![Pitchfork bifurcation]({{ 'assets/COMSOL_post5_F3.png' | relative_url }})

*Figure 3. Supercritical pitchfork bifurcation describing the onset of asymmetry. For \(Re < Re_\mathrm{crit}\), the centered jet is stable. For \(Re > Re_\mathrm{crit}\), the symmetric branch becomes unstable and two stable wall-attached branches appear.*

Near the critical Reynolds number, the slow dynamics of the jet centerline obey:

Near the critical Reynolds number, the slow dynamics of the jet centerline obey:
$$
\frac{du}{dt} = r u - u^3,
$$
with  
$$
r \propto Re - Re_{\text{crit}}.
$$

Steady states:
$$
u^* = 0, \quad u^* = \pm \sqrt{r}.
$$

Stability from  
$$
\lambda = r - 3u^2:
$$

- \(u=0\) stable for \(r<0\)  
- $(u=\pm\sqrt{r}\)$ stable for \(r>0\)  

This is the precise mathematical explanation for Coandă attachment and 2D symmetry breaking.

It is not numerical noise. It is encoded in the reduced equations.

---

## FCCVD and the Role of Buoyancy: Why 3D is Almost Always Required

FCCVD reactors operate with extreme thermal gradients.  
It is not uncommon to have  
$$
\Delta T > 1000\,\text{K}.
$$

The Grashof number:
$$
Gr = \frac{g\beta\Delta T L^3}{\nu^2},
$$
and the Richardson number:
$$
Ri = \frac{Gr}{Re^2},
$$
are usually very large.

When $(Ri\gg 1\)$, buoyancy dominates inertia.  
The resulting structures are:

- drifting asymmetric convection cells  
- azimuthally unstable plumes  
- lateral migration of catalyst particles  
- extended residence times  

All of these are inherently 3D.  
Axisymmetric modeling is not physically consistent here because buoyancy produces \(m=1\) and \(m=2\) plumes that the equations are not allowed to represent.

Planar simulations capture symmetry breaking, but not vortex stretching or full mixed convection structure.

Whether the reactor is vertical or horizontal, strong buoyancy forces generate asymmetric mixed-convection structures. In horizontal geometries, these appear as lateral drifting plumes and asymmetric recirculation cells. In vertical reactors, they appear as rising hot plumes that tilt or drift azimuthally. In both cases, the underlying mechanism is the same: buoyancy introduces m=1 and 
m=2 azimuthal modes that axisymmetric models cannot represent.
Below is an example from a horizontal DI-FCCVD reactor, which still shows the same buoyancy-driven asymmetry and mixed-convection structure discussed above.

![Buoyancy-driven mixed convection in a horizontal DI-FCCVD reactor]({{ 'assets/COMSOL_post5_f4.png' | relative_url }})

*Figure 4. Buoyancy-driven mixed convection in a **horizontal** deep-injection FCCVD reactor (adapted from Junnarkar et al., Carbon 2025). (Top) Nearly symmetric, inertia-dominated flow. (Bottom) Fully buoyant mixed-convection regime showing upward plume drift and a large asymmetric recirculation cell. These asymmetries correspond to \(m = 1\) and higher azimuthal modes, which cannot appear in 2D axisymmetric CFD.*



---

## Closing Thoughts

Dimensionality is a modeling choice that directly determines the physics a simulation can express.  
2D models are incredibly useful, especially for exploring parameter ranges and identifying transitions.  
But they also remove entire instability families.

The key is to be honest about what each model is capable of and to interpret results in that context.  
Once you see dimensionality as a filter on the Navier–Stokes equations, the behavior of 2D and 3D jets becomes much easier to understand.

---

## References

- [1] Effects of 2D Planar, Axisymmetric, and 3D Simulations on Jet Behavior and Stability  
- [2] Hou, G. et al. (2016). Carbon nanotube reactor: Ferrocene decomposition, iron particle growth, nanotube aggregation, and scale up  
- [3] Fearn, R. M., Mullin, T., Cliffe, K. A. (1990). Nonlinear flow phenomena in a symmetric sudden expansion. J. Fluid Mech.  
- [4] Strogatz, S. H. Nonlinear Dynamics and Chaos  
- [5] Anderson, J. D. Computational Fluid Dynamics  
- [6] Additional FCCVD modeling insights from recent unpublished simulations  
- [7] Private communication, Pasquali Research Group  

# Supporting Information: Mathematical Structure of Dimensional Reductions of Navier–Stokes

## 1. Full 3D Navier–Stokes (Cylindrical Coordinates)

Velocity:
$$
\mathbf{u} = (u_r, u_\theta, u_z).
$$

Continuity:
$$
\frac{1}{r}\frac{\partial (r u_r)}{\partial r}
+ \frac{1}{r}\frac{\partial u_\theta}{\partial \theta}
+ \frac{\partial u_z}{\partial z} = 0.
$$

Vorticity:
$$
\boldsymbol{\omega} = \nabla \times \mathbf{u}.
$$

Vortex stretching term:
$$
\boldsymbol{\omega}\cdot\nabla\mathbf{u}.
$$
Nonzero only in 3D.

---

## 2. Axisymmetric Reduction

Constraints:
$$
\frac{\partial}{\partial \theta}=0, \quad u_\theta=0.
$$

Continuity reduces to:
$$
\frac{1}{r}\frac{\partial (r u_r)}{\partial r}
+ \frac{\partial u_z}{\partial z} = 0.
$$

Linearized perturbations of the form  
$$
\tilde{u}(r,z)\,e^{i m \theta}
$$
are only admissible for \(m=0\).

Axisymmetric operator:
$$
L_{\text{axi}} = L_{m=0}.
$$

All helical \(m=1\) and higher modes are removed.

---

## 3. Planar Reduction

Constraints:
$$
\frac{\partial}{\partial z}=0,
\quad w=0.
$$

Continuity:
$$
\frac{\partial u}{\partial x}
+ \frac{\partial v}{\partial y} = 0.
$$

Scalar vorticity:
$$
\omega = \frac{\partial v}{\partial x}
- \frac{\partial u}{\partial y}.
$$

Vorticity transport:
$$
\frac{D\omega}{Dt} = \nu\nabla^2\omega.
$$

No stretching term:
$$
(\omega\cdot\nabla)\mathbf{u}=0.
$$

Planar systems admit symmetry breaking in the plane but cannot develop 3D cascades.

---

## 4. Pitchfork Bifurcation Derivation

Let \(u(t)\) be the lateral jet displacement.  
Center manifold reduction of the full Navier–Stokes around the symmetric state yields
$$
\frac{du}{dt} = r u - u^3.
$$

Steady states:
$$
u^*(0)=0,\quad u^*(\pm)=\pm\sqrt{r}.
$$

Jacobian:
$$
\lambda(u^*) = r - 3{u^*}^2.
$$

Thus:

- stable for $(u=0, r<0\) $ 
- unstable for $(u=0, r>0\) $ 
- stable for $(\pm\sqrt{r}\)  $

---

## 5. Buoyancy Driven Mixed Convection in FCCVD

Nondimensional groups:
$$
Re = \frac{\rho U L}{\mu},
\qquad
Gr = \frac{g\beta\Delta T L^3}{\nu^2},
\qquad
Ri = \frac{Gr}{Re^2}.
$$

For FCCVD:
- $(\Delta T>1000\) K  
- $(Gr \gg 10^8\)  
- $(Ri \gg 1\)  

Buoyant plumes generate azimuthal instabilities of the form:
$$
\tilde{u}(r,z)\,e^{im\theta}
$$
with \(m=1,2\). These are inaccessible to axisymmetric models.

---

## 6. Summary Table

| Model | Removed Terms | Allowed Modes | Missing Physics |
|-------|---------------|----------------|------------------|
| 3D | none | all \(m\) | none |
| Axisymmetric | $(\partial_\theta, u_\theta\)$ | \(m=0\) only | lateral, helical, buoyancy drift |
| Planar | $(\partial_z, w\)$ | planar symmetry breaking | vortex stretching, 3D cascade |

