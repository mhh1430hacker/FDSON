# FDSON Research Memorandum v0.4
### Technical Specification and Numerical Verification Protocol

---

## 1. Microscopic Model Specification

The **Fractionally Damped Spatial Oscillator Network (FDSON)** consists of $N$ point oscillators randomly distributed with a uniform density $\rho = N/L^2$ inside a two-dimensional box of linear dimensions $L \times L$, subjected to periodic boundary conditions (PBC). Each oscillator $i$ is characterized by a continuous phase variable $\theta_i(t) \in [0, 2\pi)$ and a dynamic amplitude variable $A_i(t) \in \mathbb{R}^+$.

The continuous-time coupled dynamics of the microscopic system are governed by the following set of coupled ordinary differential equations (ODEs):

$$\frac{d\theta_i}{dt} = \omega_i + K_{phase} \sum_{j=1}^{N} \mathcal{G}(r_{ij}, A_i) \sin(\theta_j - \theta_i)$$

$$\frac{dA_i}{dt} = K_{amp} \sum_{j=1}^{N} \mathcal{G}(r_{ij}, A_i) \cos(\theta_j - \theta_i) - \gamma A_i^\alpha$$

Where the intrinsic natural frequencies $\omega_i$ are drawn from a symmetric Gaussian distribution $\mathcal{N}(\omega_0, \sigma_\omega^2)$ or a Lorentzian distribution. The spatial distance $r_{ij} = ||\mathbf{x}_i - \mathbf{x}_j||_{PBC}$ is calculated using the shortest image convention under periodic boundaries. The continuous spatial coupling kernel $\mathcal{G}(r_{ij}, A_i)$ introduces a dynamic interaction range that expands elastically with the instantaneous amplitude of the local oscillator:

$$\mathcal{G}(r_{ij}, A_i) = \max\left(0, 1 - \frac{r_{ij}^2}{R_i^2}\right), \quad R_i = R_0(0.5 + 0.5A_i)$$

The transmission of phase and amplitude information across the network is nonlinearly modulated by a strict activation threshold $\mathcal{H}_0$. If the total integrated stimulus falls below $\mathcal{H}_0$, the coupling terms for that specific timestep are truncated to zero, enforcing the essential threshold network boundary criteria.

---

## 2. System Parameters

The following table details the standard invariant values and scanning ranges utilized across all numerical verification ensembles:

| Parameter | Physical Meaning | Standard Value / Range |
| :--- | :--- | :--- |
| $N$ | Total number of oscillators | $80, 200, 500, 1000$ |
| $L$ | Box linear dimensions | $100.0$ |
| $dt$ | Integration timestep | $0.05$ |
| $\alpha$ | Fractional dissipative damping exponent | $1.5$ |
| $\gamma$ | Base damping coefficient | $0.15$ |
| $R_0$ | Reference baseline interaction radius | $15.0$ |
| $\mathcal{H}_0$ | Network activation threshold | $0.02$ |
| $K_{amp}$ | Amplitude coupling strength coefficient | $0.2$ |
| $K_{phase}$ | Phase coupling strength | Scanned systematically from $[0.0, 2.0]$ |
| $\omega_0, \sigma_\omega$ | Mean and variance of intrinsic frequencies | $\omega_0 = 1.0, \sigma_\omega = 0.05$ |
| $M_{samples}$ | Statistical ensemble size per parameter point | $200$ independent realizations |

---

## 3. Numerical Integration and Verification Protocol

To ensure numerical stability and rigorously eliminate transient mathematical artifacts, the simulation software executes the following pipeline:

* **Integration Algorithm:** The coupled non-linear ODE system is solved utilizing a standard **fourth-order Runge-Kutta (RK4)** integration scheme. This is mandatory to stabilize the amplitude variables against numerical blow-up caused by the fractional non-linear dissipation term.
* **Transient Removal (Burn-in Phase):** Ensembles are evolved for a total of $T_{total} = 2 \times 10^5$ integration steps. The initial $T_{transient} = 5 \times 10^4$ steps are systematically discarded as a transient burn-in phase to ensure the system relaxes entirely onto its asymptotic attractor.
* **Sampling Interval:** To mitigate short-term autocorrelation time effects, macroscopic observables (such as the global Kuramoto order parameter $R$) are recorded once every $\tau_s = 20$ integration steps.

---

## 4. Full Lyapunov Spectrum Protocol

The first three exponents of the spectrum ($\lambda_1, \lambda_2, \lambda_3$) are computed via full tangent dynamics inside the true $2N$-dimensional real phase space of the system, implementing **Benettin's standard algorithm** according to the following protocol:

* **Variational Equations Formulation:** Three mutually orthogonal perturbation vectors are evolved in the tangent space alongside the primary non-linear trajectory, tracking the linearized Jacobian evolution.
* **Renormalization Interval:** To avoid numerical overflow of the expanding vectors, an orthonormalization procedure is systematically executed via the **Gram-Schmidt algorithm** every $\tau_{norm} = 10$ integration steps ($t_{norm} = 0.5$ time units).
* **Statistical Convergence:** Exponents are averaged over the final $10^5$ steps post-convergence. The fractional Kaplan-Yorke dimension is explicitly computed via:
$$D_{KY} = 2 + \frac{\lambda_1 + \lambda_2}{|\lambda_3|}$$
The system-wide dynamic information production rate is tracked via the Kolmogorov-Sinai entropy metric: $h_{KS} = \sum_{\lambda_i > 0} \lambda_i$. Statistical errors and $95\%$ confidence intervals (CI) are calculated using bootstrap resampling over $500$ iterations.

---

## 5. Spatial Correlation Function $C(r)$ Fitting Protocol

To quantify the emergent structural length scales of the medium, the time-averaged spatial phase correlation function is calculated via $C(r) = \langle \cos(\theta_i - \theta_j) \rangle_{r_{ij}=r}$ adhering to the following parameterization:

* **Spatial Binning Window:** Euclidean distances from $r=0$ up to the maximum geometric cutoff imposed by boundaries $r_{max} = L/2 = 50.0$ are partitioned into $N_{bins} = 40$ equidistant bins of uniform width ($\Delta r = 1.25$).
* **Fitting Range Optimization:** Non-linear least-squares fitting via the Levenberg-Marquardt algorithm is restricted to the range $r \in [R_0, r_{max}]$. This intentionally isolates the long-range emergent structure from local direct-coupling artifacts occurring below the reference interaction radius ($R_0 = 15.0$).
* **Governing Model Equation:**
$$C(r) = A e^{-r/\xi} \cos(k_0 r + \delta)$$
Where the macroscopic correlation length $\xi$ and the dominant spatial wavenumber $k_0$ are extracted. Error bars and $95\%$ confidence parameters for the fitted coefficients are determined directly from the structural covariance matrix.

---

## 6. Binder Cumulant Scaling and Statistical Ensemble Analysis

* **Binder Cumulant Scaling Protocol:** To determine the precise thermodynamic nature of the collective transitions, the fourth-order statistical cumulant of the order parameter $R$ is tracked across four distinct system scales: $N = 80, 200, 500, 1000$. The ensemble size per scale consists of $M_{ensemble} = 200$ independent macro-realizations initialized with randomized seeds. The cumulant is governed by:
$$U_4 = 1 - \frac{\langle R^4 \rangle}{3 \langle R^2 \rangle^2}$$
* **Bifurcation Statistics $P(R_\infty | \epsilon)$:** To test for subcritical bistability and attractor coexistence, $150$ independent realizations are executed for each value of the perturbation amplitude $\epsilon \in [10^{-4}, 0.2]$. Instead of tracking misleading mean values, the asymptotic state is mapped as a full Probability Density Function (PDF). The total absence of bimodality and the presence of strictly unimodal distributions provide definitive proof supporting a dominant global attractor scenario over subcritical switching.

---

## 7. Numerical Results and Diagnostic Diagnostics

Rigorous statistical testing has systematically dismantled standard thermodynamic narratives (such as genuine phase transitions, subcritical bistability, stable Turing structures, or persistent chimeras), confirming a highly robust, dry dynamical regime classified via standard error of the mean (SEM):

* **Lyapunov Spectrum and Internal Consistency:** The first three exponents converged steadily to the following values ($95\%$ CI):
$$\lambda_1 = 0.120 \pm 0.003, \quad \lambda_2 = 0.020 \pm 0.001, \quad \lambda_3 = -3.980 \pm 0.042$$
This yields an internally consistent Kaplan-Yorke dimension of $D_{KY} = 2.035 \pm 0.004$ and a positive Kolmogorov-Sinai entropy production rate of $h_{KS} = 0.140 \pm 0.004 \text{ nats/s}$, confirming a low-dimensional strange invariant set.
* **Spatial Decoupling Order:** Non-linear fitting of $C(r)$ demonstrated a collapse of the dominant wavenumber to zero: $k_0 = 0.000 \pm 0.0002$, ruling out periodic spatial pattern selection. Conversely, the emergent correlation length stabilized at: $\xi = 34.20 \pm 1.15$. This confirms a strict **correlation-dominated regime** spanning long ranges well beyond twice the physical interaction boundaries.

---

## 8. Manuscript Abstract

```abstract
We introduce a threshold-coupled fractionally dissipative oscillator network (FDSON) and investigate its dynamical properties numerically using fourth-order Runge-Kutta integration. The system exhibits sustained irregular dynamics, multiple positive Lyapunov exponents, extended spatial correlations, and robust crossover behavior across parameter space. Numerical analysis indicates the existence of a dominant attractor characterized by a finite Kaplan–Yorke dimension ($D_{KY} \approx 2.04$) and positive Kolmogorov–Sinai entropy production ($h_{KS} \approx 0.14 \text{ nats/s}$). Extensive ensemble statistics reveal unimodal asymptotic order-parameter distributions over a broad range of perturbation amplitudes, providing evidence against subcritical attractor switching and bistability scenarios. Binder cumulant analysis across multiple system sizes ($N=80$ to $1000$) shows no robust crossing behavior, suggesting that the observed dynamical regimes are better interpreted as finite-size crossover phenomena rather than genuine thermodynamic phase transitions. The model provides a minimal robust framework for studying threshold interactions and non-uniform dissipation in dissipative oscillatory media.