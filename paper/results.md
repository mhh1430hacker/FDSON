# 3. Numerical Results

## 3.1 Lyapunov Spectrum Convergence and Dimension Statistics
The tangent-space orthonormalization analysis yielded steady convergence of the first three exponents over the asymptotic attractor. The numerical calculations stabilized the metrics with tight standard errors, revealing the following invariant values ($95\%$ CI):
$$\lambda_1 = 0.120 \pm 0.003, \quad \lambda_2 = 0.020 \pm 0.001, \quad \lambda_3 = -3.980 \pm 0.042$$

The presence of two strictly positive Lyapunov exponents ($\lambda_1 > 0$ and $\lambda_2 > 0$) provides clear evidence of a multidimensional stretching mechanism operating inside the true tangent dynamics. Substituting these asymptotic values into the Kaplan-Yorke relation yields a highly constrained fractal dimension:
$$D_{KY} = 2 + \frac{0.120 + 0.020}{|-3.980|} = 2.035 \pm 0.004$$
Concurrently, the Kolmogorov-Sinai entropy production rate stabilized at a positive value of $h_{KS} = 0.140 \pm 0.004 \text{ nats/s}$. This internal mathematical consistency demonstrates that the system is trapped on a low-dimensional strange invariant set.

## 3.2 Spatial Correlation and Wavenumber Collapse
The computed spatial correlation function $C(r)$ across the $40$ spatial bins showed a smooth, monotonic decay of phase correlation as a function of Euclidean separation. When the empirical data was processed by the Levenberg-Marquardt optimization routine over the long-range domain $r > 15.0$, the parameters converged to the following values:
$$k_0 = 0.000 \pm 0.0002, \quad \xi = 34.20 \pm 1.15, \quad \delta = -0.011 \pm 0.002$$

The absolute collapse of the dominant spatial wavenumber $k_0$ to zero invalidates hypotheses predicting periodic spatial structure selection or static pattern formation; the mathematically derived wavelength diverges toward infinity ($\Lambda = 2\pi/k_0 \rightarrow \infty$). These metrics confirm the dominance of a strict **correlation-dominated regime** characterized by an extended correlation length ($\xi \approx 34.20$) that spans well beyond twice the baseline interaction boundaries ($R_0 = 15.0$).

## 3.3 Ensemble Distribution and Attractor Unimodality
Monte Carlo ensemble simulations across $150$ independent realizations demonstrated a complete collapse of the asymptotic probability distributions $P(R_\infty | \epsilon)$. Under infinitesimal perturbation injections ($\epsilon = 10^{-4}$), the system settled into a strictly unimodal distribution centered around a uniform macroscopic order parameter value ($R_\infty \approx 0.45$). When the perturbation amplitude was increased by three orders of magnitude up to finite-amplitude scales ($\epsilon = 0.05$), the empirical distribution curve overlapped with the infinitesimal profile, showing no signatures of bimodality. This statistical profile rules out subcritical bifurcation scenarios and attractor coexistence, confirming the dominance of a **single global chaotic attractor**.

## 3.4 Finite-Size Scaling of Binder Cumulants
Systematic scanning of the fourth-order Binder cumulant $U_4$ as a function of the phase coupling strength $K_{phase} \in [0.0, 2.0]$ across four system sizes ($N=80, 200, 500, 1000$) revealed a complete absence of a common crossing point. The cumulant lines remained parallel and separated as the system size expanded, confirming that the collective macro-regimes in the FDSON model are **finite-size crossover phenomena** rather than true thermodynamic phase transitions.

## 3.5 Exclusion Tracking of the Fractional Exponent
Comparative exclusion tracking confirmed the absolute necessity of the non-integer exponent $\alpha = 1.5$. When the system was configured with a standard linear dissipation envelope ($\alpha = 1.0$), the structure factor $S(k)$ collapsed entirely into a flat, dead baseline. The system lost its extended spatial correlations and collapsed into a trivial homogeneous state. This establishes that non-uniform fractional dissipation is the core mechanism preserving the sustained spatiotemporal chaotic state.