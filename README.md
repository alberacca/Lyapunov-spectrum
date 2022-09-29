---
header-includes:
  - \usepackage{array}
  - \usepackage{booktabs}
  - \usepackage{mathtools}
  - \usepackage{braket}
---

# Lyapunov-spectrum

Jacobian-free implementation for the computation of the first m largest lyapunov exponents and Kaplan-Yorke dimension of a dynamical system. 
The algorithm requires only the right hand side of the nonlinear governing equations.

The exponents are computed following the orthonormalization algorithm of Benettin et al. (https://doi.org/10.1007/BF02128236). Kaplan-Yorke dimension (https://doi.org/10.1007/BFb0064319).

## Algorithm


The algorithm requires the integration of the governing equations $m+1$ times.

We consider a nonlinear autonomous dynamical system in the form of 

$$
\begin{aligned}
    \mathbf{\dot{q}} = \mathbf{f(q)}
\end{aligned}
\tag{1}
$$

where $\mathbf{q}$ is the system's state and $\mathbf{f}$ is a nonlinear operator. In chaotic solutions, the norm of a perturbation $\mathbf{y}_{i}$, such that $\mathbf{\hat{q}}_i = \mathbf{\overline{q}} + \mathbf{y}_i$ with $\mathbf{y}_i \ll 1$, grows in time until nonlinear saturation. For small enough times, $t_1 - t_0$, so that we avoid nonlinear saturation, the evolution of  $\mathbf{y}_i$ can be computed as

$$
\tag{2}
\begin{aligned}
    \mathbf{y}_i (t_1) = \mathbf{\overline{q}}(t_1) - \mathbf{\hat{q}}_i(t_1),
\end{aligned}
$$

where both elements in the right-hand side are computed by solving (1) with initial conditions equal to $\mathbf{\overline{q}}(t_0)$ and $\mathbf{\overline{q}}(t_0)+\mathbf{y}_i(t_0)$, respectively. The average exponential growth rate for the perturbation $\mathbf{y}_i$ between $t_0$ and $t_1$ is

$$
\tag{3}
\begin{aligned}
    \lambda = \frac{1}{t_1 - t_0}\ln\left(\frac{||\mathbf{y}(t_1)||}{||\mathbf{y}(t_0)||}\right),
\end{aligned}
$$
