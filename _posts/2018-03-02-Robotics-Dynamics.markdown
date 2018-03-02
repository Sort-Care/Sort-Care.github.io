---
layout: single
title: Dynamics for Robotics
date: 2018-03-02 10:47 +0800
mathjax: true

excerpt: "Rotational Dynamics, Momentum of Intertia and so forth"
header:
  teaser: /assets/images/robot.jpg
  overlay_image: /assets/images/robot.jpg
  overlay_filter: 0.5
---
{% include toc title="Contents" %}


# Dynamics

**how force act on bodies to create accelerations**

1.  the moment of inertia and how it depends on the geometry of motion
2.  *computed-torque equation*




## Newton's Laws

-   **1st Law:** A particle remains in a state of constant rectilinear motion unless acted on by an external force
-   **2nd Law:** The time-rate-of-change in the momentum(mv) of the particle is proportional to the externally applied forces, $\mathbf{f} = \frac{d}{dt}(m\mathbf{v})$
-   **3rd Law:** Any force imposed on body $A$ by body $B$ is reciprocated by an equal and opposite reaction force on body $B$ by body $A$



## Euler's Equation written in the form of Newton's 2nd Law

$$
\mathbf{\tau} = \frac{d}{dt}[\mathbf{J}\omega] = \mathbf{J}\ddot{\theta}
$$


<a id="orgf1f0328"></a>

## Construction of the inertia tensor

A inertia tensor is derived by summing the contribution of each lamina to the total moment of inertia of the body.
$$
\begin{align*}
^A\mathbf{J} &=
	\begin{bmatrix}
	(y^2+z^2) &-xy &-xz\\
	-yx &(x^2 + z^2) &-yz\\
	-zx &-zy &(x^2+y^2)
	\end{bmatrix}\\
&=
	\begin{bmatrix}
		\mathbf{J}_{xx} &-\mathbf{J}_{xy} &-\mathbf{J}_{xz}\\
		-\mathbf{J}_{yx} &\mathbf{J}_{yy} &-\mathbf{J}_{yz}\\
		-\mathbf{J}_{zx} &-\mathbf{J}_{zy} &-\mathbf{J}_{zz}
	\end{bmatrix}
\end{align*}
$$

<a id="org33008ed"></a>

## The Parallel Axis Theorem

Given an arbitrary choice for frame $A$, the center of mass of the body is defined:
$$
\mathbf{r}_{cm} = \frac{\sum m_i \mathbf{r}_i}{\sum m_i}
$$

For a body with a known inertia tensor at the center of mass $^{CM}\mathbf{I}$, the parallel axis theorem states that the inertia tensor at any other parallel frame is computed:
$$
\begin{equation}
^A\mathbf{J} = ^{CM}J +
\begin{bmatrix}
m(r_y^2 + r_z^2) &-m(r_x r_y) &-m(r_xr_z)\\
-m(r_y r_x) &m(r_x^2+r_z^2) &-m(r_y r_z)\\
-m(r_z r_x) &-m(r_z r_y) &m(r_x^2 + r_y^2)
\end{bmatrix}
\end{equation}
$$

<a id="org46b832c"></a>

## Rotating the Inertia Tensor

The angular momentum vector $\mathbf{J\omega}$ of a rotating body is conserved during the rotation of coordinates, therefore

$$
\begin{align}
\mathbf{J}_1 \mathbf{\omega}_1  &=_1\mathbf{R}_0 (\mathbf{J}_0\mathbf{\omega}_0) \\
	&=_1\mathbf{R}_0 \mathbf{J}_0 ({_1\mathbf{R}_0}^T _1\mathbf{R}_0)\mathbf{\omega}_0\\
	&= (_1\mathbf{R}_0 \;\mathbf{J}_0 \;{_1\mathbf{R}_0}^T)\mathbf{\omega_1}
\end{align}
$$
