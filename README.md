# Lyapunov-spectrum

Jacobian-free implementation for the computation of the first m largest lyapunov exponents and Kaplan-Yorke dimension of a dynamical system. 
The algorithm requires only the right hand side of the nonlinear governing equations.

The exponents are computed following the orthonormalization algorithm of Benettin et al. (https://doi.org/10.1007/BF02128236). Kaplan-Yorke dimension (https://doi.org/10.1007/BFb0064319).

## Algorithm

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
\begin{aligned}
    \lambda = \frac{1}{t_1 - t_0}\ln\left(\frac{||\mathbf{y}(t_1)||}{||\mathbf{y}(t_0)||}\right),
\end{aligned}
$$

where $||\cdot||$ indicates the $L_2$ norm.
For long enough times, $t_1 \to \infty$ , any perturbation evolves with the same dominant Lyapunov exponent 

$$
\begin{aligned}
        \Lambda_1 = \lim_{t_1\to\infty}\frac{1}{t_1-t_0}\ln\left(\frac{||\mathbf{y}(t_1)||}{||\mathbf{y}(t_0)||}\right),
\end{aligned}
$$

as the component along the direction with maximum growth becomes dominant for sufficiently long times. However, due to saturation of the nonlinear equations (or instability of the linearized equations) computing $\Lambda_1$ is not straightforward.

To compute the growth along the $m$ most unstable directions for long times, Benettin et al. proposed to periodically orthonormalize the evolution of the subspace spanned by $m$ different perturbations. The algorithm works as follows. Every $t_o$, we orthonormalize the $m$ perturbations and compute the future evolution of the orthonormalized basis:

$$
\begin{aligned}
    \mathbf{\tilde{y}}_1(t) &= \frac{\mathbf{y}_1(t)}{||\mathbf{y}_1(t)||} \quad \mathrm{where} \quad \mathbf{y}_1(t-t_o) = \epsilon \mathbf{\tilde{y}}_1(t-t_o), \nonumber \\ 
    \vdots &  \nonumber \\
    \mathbf{\tilde{y}}_i (t) &= \frac{\mathbf{y'}_i(t)}{||\mathbf{y'}_i(t)||}; \quad \mathbf{y}_i' = \mathbf{y}_i - \sum^{i-1}_j   (\mathbf{y}_i^T\mathbf{\tilde{y}}_j) \mathbf{\tilde{y}}_j \quad \mathrm{where} \quad \mathbf{y}_i(t-t_o) = \epsilon\mathbf{\tilde{y}}_i(t-t_o), \nonumber \\
    \vdots &  \nonumber \\
\mathbf{\tilde{y}}_m (t) &= \cdots 
\end{aligned}
$$

where $\epsilon \ll 1$ is selected in order for initial condition to be infinitesimal and $\mathbf{y}_i(t)$ is computed using (2). This means that we are solving (1) for $m$ perturbations plus the unperturbed trajectory.
At each orthonormalization, we store the average exponential growths in the previous $t_o$ interval, so that for the $i$-th direction at the $k$-th orthonormalization we have 

$$
\begin{aligned}
  \lambda_i^{(k)} = \frac{1}{t_{\mathrm{o}}}\ln\left(\frac{||\mathbf{y}'_i(t)||}{||\mathbf{y}_i(t-t_o)||}\right)
\end{aligned}
$$

where $||\mathbf{y}_1(t-t_o)||=\epsilon$ and $t=kt_o$. After $N_o$ orthonormalizations, the Lyapunov exponents are the average of the stored exponential growths

$$
\begin{aligned}
  \Lambda_i = \frac{1}{N_o}\sum_{k=1}^{N_o}\lambda_i^{(k)}.
\end{aligned}
$$

The dimensionality of the attractor can then be estimated through the Kaplan-Yorke conjecture,

$$
\begin{aligned}
    D_{KY} = j +  \frac{\sum_{i=1} \Lambda_i}{|\Lambda_{j+1}|}
\end{aligned}
$$

where $\Lambda_i$ are the $j$ largest Lyapunov exponents for which $\sum_{i=1} \Lambda_i \geq 0$ for $i=1,\cdots,j$.

### Computational cost

To compute the $m$ largest Lyapunov exponents, the algorithm requires the governing equations to be solved $m+1$ times.
