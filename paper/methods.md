# 2. Numerical and Analytical Methodologies

## 2.1 Microscopic Model Formulation
The microscopic architecture of the **FDSON** model consists of $N$ point oscillators randomly distributed with a uniform density $\rho = N/L^2$ inside a two-dimensional box of linear dimensions $L \times L$, subjected entirely to periodic boundary conditions (PBC) evaluated under the shortest image convention. The dynamical state of each node $i$ is defined by two coupled continuous fields: the continuous phase variable $\theta_i(t) \in [0, 2\pi)$ and the non-negative vibration amplitude $A_i(t) \in \mathbb{R}^+$.

The global continuous-time dynamics are governed by the following system of coupled ordinary differential equations (ODEs):
$$\frac{d\theta_i}{dt} = \omega_i + K_{phase} \sum_{j=1}^{N} \mathcal{G}(r_{ij}, A_i) \sin(\theta_j - \theta_i)$$
$$\frac{dA_i}{dt} = K_{amp} \sum_{j=1}^{N} \mathcal{G}(r_{ij}, A_i) \cos(\theta_j - \theta_i) - \gamma A_i^\alpha$$

Where the intrinsic natural frequencies $\omega_i$ of individual oscillators are drawn from a symmetric Gaussian distribution characterized by a mean $\omega_0$ and a narrow variance $\sigma_\omega$:
$$g(\omega) = \frac{1}{\sigma_\omega \sqrt{2\pi}} \exp\left( - \frac{(\omega - \omega_0)^2}{2\sigma_\omega^2} \right)$$
The spatial Euclidean distance $r_{ij}$ is calculated accounting for coordinate folding at the boundaries:
$$r_{ij} = \sqrt{ \min(|x_i - x_j|, L - |x_i - x_j|)^2 + \min(|y_i - y_j|, L - |y_i - y_j|)^2 }$$
The term $\mathcal{G}(r_{ij}, A_i)$ defines the continuous spatial coupling kernel, where the dynamic interaction radius $R_i$ expands elastically as a linear function of the instantaneous amplitude of the local oscillator:
$$\mathcal{G}(r_{ij}, A_i) = \max\left(0, 1 - \frac{r_{ij}^2}{R_i^2}\right), \quad R_i = R_0(0.5 + 0.5A_i)$$
The transmission of phase and amplitude information across the network is nonlinearly modulated by a strict activation threshold $\mathcal{H}_0 = 0.02$. If the total integrated stimulus falls below $\mathcal{H}_0$, the coupling terms for that specific timestep are truncated to zero:
$$\text{If } \left| \sum_{j} \mathcal{G}(r_{ij}, A_i) \sin(\theta_j - \theta_i) \right| < \mathcal{H}_0 \implies \text{Coupling Terms} = 0$$

## 2.2 Integration Protocol and Transient Removal
To maintain amplitude stability and systematically prevent numerical blow-up triggered by the fractional non-linear exponent $\alpha = 1.5$, the coupled ordinary differential equations are integrated utilizing a deterministic **fourth-order Runge-Kutta (RK4)** scheme with a fixed timestep $dt = 0.05$. The system is evolved for a total duration of $T_{total} = 2 \times 10^5$ integration steps. To guarantee the complete elimination of mathematical transient artifacts, the initial $T_{transient} = 5 \times 10^4$ steps are systematically discarded as a transient burn-in phase, ensuring the trajectories relax entirely onto the stable asymptotic attractor. Macroscopic observables are recorded once every $\tau_s = 20$ integration steps to filter out short-term autocorrelation time effects.

## 2.3 Tangent Space Dynamics and Lyapunov Spectrum Computation
The first three exponents of the spectrum ($\lambda_1, \lambda_2, \lambda_3$) are computed via full linearized tangent dynamics inside the true $2N$-dimensional real phase space of the system, implementing **Benettin's standard algorithm**.

Given the instantaneous Jacobian matrix $J(t)$, three mutually orthogonal perturbation vectors $\mathbf{v}_1, \mathbf{v}_2, \mathbf{v}_3$ are tracked in the tangent space alongside the primary non-linear trajectory. To avoid numerical overflow of the expanding vectors, an orthonormalization procedure is executed via the **Gram-Schmidt algorithm** every $\tau_{norm} = 10$ integration steps ($t_{norm} = 0.5$ time units). The asymptotic Lyapunov exponents are extracted via:
$$\lambda_i = \lim_{M \to \infty} \frac{1}{M \tau_{norm}} \sum_{m=1}^{M} \ln ||\mathbf{v}_i^{(m)}||$$
The fractional Kaplan-Yorke dimension ($D_{KY}$) and the system-wide dynamic information production rate ($h_{KS}$) are explicitly derived from the converged spectrum using the following relations:
$$D_{KY} = j + \frac{\sum_{i=1}^{j}\lambda_i}{|\lambda_{j+1}|}, \quad h_{KS} = \sum_{\lambda_i > 0} \lambda_i$$
Where $j$ is the largest index such that the running sum of exponents remains strictly positive. Statistical uncertainty metrics and $95\%$ confidence intervals (CI) are verified using bootstrap resampling over $500$ iterations.

## 2.4 Spatial Correlation and Non-linear Model Fitting
To quantify the macroscopic structural length scales of the medium, the time-averaged spatial phase correlation function is calculated via $C(r) = \langle \cos(\theta_i - \theta_j) \rangle_{r_{ij}=r}$. The spatial distance domain from $r=0$ up to the geometric limit imposed by boundaries $r_{max} = L/2 = 50.0$ is partitioned into $N_{bins} = 40$ equidistant bins of uniform width ($\Delta r = 1.25$).

Non-linear least-squares fitting using the Levenberg-Marquardt algorithm is restricted to the range $r \in [R_0, r_{max}]$ to isolate the long-range emergent structure from direct-coupling artifacts occurring below the reference interaction radius ($R_0 = 15.0$). The empirical data is fitted to the frustrated spatial decay model:
$$C(r) = A e^{-r/\xi} \cos(k_0 r + \delta)$$
Where the macroscopic correlation length $\xi$ and the dominant spatial wavenumber $k_0$ are explicitly extracted.

## 2.5 Finite-Size Scaling and Ensemble Statistical Criteria
To determine the precise thermodynamic nature of the collective transitions, the fourth-order statistical Binder cumulant of the global Kuramoto order parameter ($R = |\frac{1}{N}\sum_j e^{i\theta_j}|$) is monitored across four distinct system scales: $N = 80, 200, 500, 1000$. The ensemble size per scale consists of $M_{ensemble} = 200$ independent macro-realizations initialized with randomized seeds and independent natural frequency draws. The cumulant is governed by:
$$U_4 = 1 - \frac{\langle R^4 \rangle}{3 \langle R^2 \rangle^2}$$
Concurrently, the asymptotic state is mapped as a full Probability Density Function ($PDF$), $P(R_\infty | \epsilon)$, across $150$ independent realizations executed for each value of the perturbation amplitude scanned systematically from $\epsilon \in [10^{-4}, 0.2]$ to differentiate between unimodal and bimodal distributions.