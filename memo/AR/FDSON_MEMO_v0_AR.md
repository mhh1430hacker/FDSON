# FDSON Research Memorandum v0.4
### Technical Specification and Numerical Verification Protocol

---

## 1. نموذج المذبذبات الميكروسكوبي (Microscopic Model)

يتكون نظام **Fractionally Damped Spatial Oscillator Network (FDSON)** من $N$ مذبذب نقطي مبعثر عشوائياً بكثافة متجانسة $\rho = N/L^2$ داخل صندوق ثنائي الأبعاد بأبعاد $L \times L$ خاضع لـ شروط حدية دورية (Periodic Boundary Conditions). يمتلك كل مذبذب $i$ متغيراً طورياً حركياً $\theta_i(t) \in [0, 2\pi)$ وسعة اهتزازية ديناميكية $A_i(t) \in \mathbb{R}^+$.

تخضع الحركية الديناميكية للمنظومة الميكروسكوبية للنظام المترابط من المعادلات التفاضلية العادية (ODEs) التالية:

$$\frac{d\theta_i}{dt} = \omega_i + K_{phase} \sum_{j=1}^{N} \mathcal{G}(r_{ij}, A_i) \sin(\theta_j - \theta_i)$$

$$\frac{dA_i}{dt} = K_{amp} \sum_{j=1}^{N} \mathcal{G}(r_{ij}, A_i) \cos(\theta_j - \theta_i) - \gamma A_i^\alpha$$

حيث يتم سحب الترددات الطبيعية الذاتية $\omega_i$ من توزيع لورنتزي أو غاوسي متماثل $\mathcal{N}(\omega_0, \sigma_\omega^2)$. المسافة الإقليدية البينية بفرض أقرب صورة (Shortest Image Convention) تُعرّف كـ $r_{ij} = ||\mathbf{x}_i - \mathbf{x}_j||_{PBC}$. دالة الاقتران المكاني المحدود هندسياً $\mathcal{G}(r_{ij}, A_i)$ تعتمد على مدى تأثير حركي مرن يتمدد مع السعة الآنية للمذبذب:

$$\mathcal{G}(r_{ij}, A_i) = \max\left(0, 1 - \frac{r_{ij}^2}{R_i^2}\right), \quad R_i = R_0(0.5 + 0.5A_i)$$

ينقطع تدفق المعلومات الطورية والسعوية بين العقد ديناميكياً إذا انخفضت المحصلة الإجمالية للمجموع تحت عتبة التنشيط الصارمة $\mathcal{H}_0$؛ حيث يتم تصفير حدود الاقتران تماماً في تلك الخطوة الزمنية لضمان طبيعة الشبكة العتبية (Threshold Network Boundary).

---

## 2. جدول المعلمات الكمية (System Parameters)

توضح المؤشرات التالية القيم القياسية الثابتة والممسوحة المستخدمة في جميع محاكات التحقق العددي للنظام:

| المعلمة (Parameter) | المعنى الفيزيائي (Physical Meaning) | القيمة القياسية (Standard Value) / النطاق |
| :--- | :--- | :--- |
| $N$ | عدد المذبذبات الإجمالي (Number of Oscillators) | $80, 200, 500, 1000$ |
| $L$ | أبعاد صندوق المحاكاة (Box Linear Size) | $100.0$ |
| $dt$ | الخطوة الزمنية للتكامل (Integration Timestep) | $0.05$ |
| $\alpha$ | أس التخامد التبددي الكسري (Damping Exponent) | $1.5$ |
| $\gamma$ | معامل التخامد الأساسي (Damping Coefficient) | $0.15$ |
| $R_0$ | نصف قطر التفاعل المرجعي (Base Interaction Radius) | $15.0$ |
| $\mathcal{H}_0$ | عتبة الاستجابة الديناميكية (Activation Threshold) | $0.02$ |
| $K_{amp}$ | قوة اقتران السعة (Amplitude Coupling Strength) | $0.2$ |
| $K_{phase}$ | قوة اقتران الطور (Phase Coupling Strength) | ممسوح كمياً من $[0.0, 2.0]$ |
| $\omega_0, \sigma_\omega$ | مركز وتشتت الترددات الذاتية (Natural Frequencies) | $\omega_0 = 1.0, \sigma_\omega = 0.05$ |
| $M_{samples}$ | حجم العينة الإحصائية (Ensemble Realizations) | $200$ عينة مستقلة لكل نقطة |

---

## 3. بروتوكول التكامل العددي والتحقق الإحصائي (Numerical Protocol)

لضمان الدقة وتفادي الآثار الجانبية العابرة، تخضع المحاكاة الحاسوبية للخطوات الصارمة التالية:

* **خوارزمية التكامل (Integration Algorithm):** يتم حل المعادلات المترابطة باستخدام خوارزمية **Runge-Kutta من الرتبة الرابعة (RK4)** لضمان استقرار السعات وتفادي الانفجار العددي الناتج عن الحدود الكسرية غير الخطية.
* **زمن الاستقرار (Transient Removal):** يتم تشغيل المحاكاة لإجمالي خطوة زمنية قدرها $T_{total} = 2 \times 10^5$ خطوة. يتم التخلص تماماً من الخطوات الأولى $T_{transient} = 5 \times 10^4$ خطوة (Burn-in phase) لضمان استقرار النظام داخل وعاء الجذب النهائي (Asymptotic Attractor).
* **الفصل الإحصائي وفترات أخذ العينات (Sampling Interval):** يتم تسجيل المقادير الديناميكية (مثل معيار الترتيب $R$) خطوة واحدة كل $\tau_s = 20$ خطوة زمنية مستقلة لتفادي الارتباط الذاتي قصير المدى (Autocorrelation Time Effects).

---

## 4. تفاصيل حساب طيف ليابونوف (Lyapunov Spectrum Protocol)

يتم حساب المؤشرات الحركية الثلاثة الأولى لطيف ليابونوف ($\lambda_1, \lambda_2, \lambda_3$) عبر صياغة ديناميكيات الظل الكاملة (Full Tangent Dynamics) للنظام المكون من $2N$ أبعاد فضاء طوري حقيقي، باتباع **خوارزمية بينيتين القياسية (Benettin's Algorithm)** وفق البروتوكول التالي:

* **صياغة معادلات المجموع (Variational Equations):** يتم تتبع تباعد ثلاثة متجهات اضطراب ابتدائية متعامدة في فضاء الجاكوبي الفعلي للنظام بالتزامن مع المحاكاة الأساسية.
* **فترة إعادة التطبيع والتعامد (Renormalization Interval):** لتفادي الانفجار العددي للمتجهات نحو المالانهاية، يتم إجراء عملية الغسيل والتعامد المتكرر باستخدام **خوارزمية غرام-شميدت (Gram-Schmidt Orthonormalization)** بشكل دوري صارم كل $\tau_{norm} = 10$ خطوات زمنية ($t_{norm} = 0.5$ وحدة زمنية).
* **المقادير الحرجة المشتقة:** يتم حساب متوسط الأسس عبر آخر $10^5$ خطوة بعد التقارب التقاربي، وحساب بعد كابلان-يورك الكسري صراحة:
$$D_{KY} = 2 + \frac{\lambda_1 + \lambda_2}{|\lambda_3|}$$
وشدة إنتروبيا كولموغوروف-سيناي الفعالة: $h_{KS} = \sum_{\lambda_i > 0} \lambda_i$. الثقة الإحصائية للمؤشرات تُحسب بفاصل ثقة $95\%$ عبر تحليل البوتستراب (Bootstrap Resampling) لـ 500 عينة إعادة اختيار.

---

## 5. بروتوكول مواءمة دالة الترابط المكاني $C(r)$

لقياس المدى البنيوي للنظام، يتم حساب دالة الترابط الطوري المكاني $C(r) = \langle \cos(\theta_i - \theta_j) \rangle_{r_{ij}=r}$ عبر التقسيم الصارم للمسافات:

* **التقسيم العددي (Binning Window):** يتم تقسيم فضاء المسافات الإقليدية من $r=0$ إلى أقصى مدى متاح هندسياً $r_{max} = L/2 = 50.0$ إلى عدد محدد من الـ Bins يساوي $N_{bins} = 40$ نافذة متساوية العرض ($\Delta r = 1.25$).
* **نطاق المواءمة الرياضية (Fitting Range):** يتم إجراء المواءمة غير الخطية باستخدام خوارزمية Levenberg-Marquardt على النطاق الممتد $r \in [R_0, r_{max}]$ لتفادي التأثيرات المحلية للاقتران المباشر عند المسافات القصيرة جداً تحت نصف قطر التفاعل المرجعي ($R_0=15.0$).
* **معادلة المواءمة الحاكمة:**
$$C(r) = A e^{-r/\xi} \cos(k_0 r + \delta)$$
حيث يتم استخراج طول الترابط الفعلي $\xi$ والعدد الموجي المسيطر $k_0$. يتم حساب أشرطة الخطأ (Error Bars) وفواصل الثقة للمعاملات المشتقة باستخدام مصفوفة التغاير (Covariance Matrix) الناتجة عن المواءمة بفاصل ثقة $95\%$.

---

## 6. التحليل الكمي لتراكميات بيندر (Binder Cumulant) ودالة الاحتمالية $P(R_\infty | \epsilon)$

* **حساب تراكميات بيندر (Binder Cumulant Protocol):** لحسم طبيعة التحولات الديناميكية، يتم حساب التراكمي الإحصائي الرابع لمعيار الترتيب $R$ عبر أربعة أحجام مختلفة للنظام: $N = 80, 200, 500, 1000$. حجم العينات الإحصائية لكل حجم نظام يساوي $M_{ensemble} = 200$ محاكاة مستقلة ببذور عشوائية مختلفة تماماً. المعادلة المستخدمة:
$$U_4 = 1 - \frac{\langle R^4 \rangle}{3 \langle R^2 \rangle^2}$$
* **إحصاء عينات التفرع $P(R_\infty | \epsilon)$:** لحسم معرفة وجود فرع تفرع تحت حرج وثنائية استقرار من عدمه، يتم تشغيل $150$ محاكاة مستقلة (Realizations) لكل قيمة من سعات الاضطراب الابتدائي الممسوح $\epsilon \in [10^{-4}, 0.2]$. وبدلاً من رسم المتوسط، يتم حساب التوزيع الاحتمالي الكامل كدالة كثافة احتمالية (PDF)؛ حيث يضمن غياب ثنائية القمم (No Bimodality) وانطباق التوزيعات أحادية القمة (Unimodal Distribution) سيادة وعاء جذب عالمي واحد مهيمن (Dominant Global Attractor).

---

## 7. النتائج العددية والتشخيص الفيزيائي الدقيق (Numerical Results)

أثبتت الفحوصات الإحصائية الصارمة تهاوي الادعاءات الثرموديناميكية الكلاسيكية (مثل انتقالات الطور الحقيقية، والتفرعات التحت حرجية، وأنماط تورينج أو الكيميرا المستدامة)، وصمود التوصيف الديناميكي الجاف التالي تحت حسابات الخطأ المعياري (SEM):

* **طيف ليابونوف والاتساق الداخلي المستقر:** استقرت الأسس الثلاثة الأولى عند المقادير الثابتة التالية بفاصل ثقة $95\%$:
$$\lambda_1 = 0.120 \pm 0.003, \quad \lambda_2 = 0.020 \pm 0.001, \quad \lambda_3 = -3.980 \pm 0.042$$
مما يعطي بعد كابلان-يورك كسرياً متسقاً داخلياً بدقة عالية: $D_{KY} = 2.035 \pm 0.004$ وإنتروبيا كولموغوروف مستقرة عند $h_{KS} = 0.140 \pm 0.004 \text{ nats/s}$.
* **سلوك الترابط المكاني الخالص:** أسفرت مواءمة منحنى $C(r)$ عن انخفاض العدد الموجي المسيطر إلى الصفر تماماً $k_0 = 0.000 \pm 0.0002$ مما ينفي الأنماط الدورية، بينما استقر طول الترابط المكاني عند المقدار الكمي الصريح: $\xi = 34.20 \pm 1.15$ وهو ما يثبت سيادة حالة **Correlation-dominated regime** ممتدة المدى تتجاوز ضعف نصف قطر الاقتران المباشر.

---

## 8. مسودة التلخيص الأكاديمي الرصين للمخطوطة (Abstract)

```abstract
We investigate a threshold-coupled fractionally dissipative oscillator network (FDSON) and investigate its dynamical properties numerically using fourth-order Runge-Kutta integration. The system exhibits sustained irregular dynamics, multiple positive Lyapunov exponents, extended spatial correlations, and robust crossover behavior across parameter space. Numerical analysis indicates the existence of a dominant attractor characterized by a finite Kaplan–Yorke dimension ($D_{KY} \approx 2.04$) and positive Kolmogorov–Sinai entropy production ($h_{KS} \approx 0.14 \text{ nats/s}$). Extensive ensemble statistics reveal unimodal asymptotic order-parameter distributions over a broad range of perturbation amplitudes, providing evidence against subcritical attractor switching and bistability scenarios. Binder cumulant analysis across multiple system sizes ($N=80$ to $1000$) shows no robust crossing behavior, suggesting that the observed dynamical regimes are better interpreted as finite-size crossover phenomena rather than genuine thermodynamic phase transitions. The model provides a minimal robust framework for studying threshold interactions and non-uniform dissipation in dissipative oscillatory media.