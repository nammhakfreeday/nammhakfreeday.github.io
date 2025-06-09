---
layout: post
title: "เรารู้ได้อย่างไรว่า ODE มีผลเฉลย"
subtitle: หาแรงบันดาลใจในการแก้ไขปัญหา PDE ด้วยการดูเทคนิคทาง ODE 
description: "ทฤษฎีที่เกี่ยวข้องกับ ODE และ dynamical systems เป็นทฤษฎีที่ได้รับการค้นพบมากพอสมควร โดยมองพื้นฐานทางคณิตวิเคราะห์เบื้องต้น ทั้งยังเอาไปใช้ในการแก้ไขปัญหา PDE ได้อีกด้วย"
author: กฤตพัฒน์ รัตนภูผา
date: 2025-05-27 00:00:00 07:00
coverImage: /assets/images/posts_cover/pde_1.gif
imageCredit: "Brocca L., et al. (2023) \"A Digital Twin of the terrestrial water cycle:<br> a glimpse into the future through high-resolution Earth observations\", <i>Frontiers in Science</i>."
category: mathematics
series: ว่าง ๆ มาทำ PDE
order: 1
toc: true
---

**คำเตือน**: หากใช้บทความนี้อ้างอิง โปรดใช้อย่างระมัดระวัง เพราะอาจมีส่วนใดผิดได้

---

การแก้ปัญหาเกี่ยวกับสมการเชิงอนุพันธ์สามารถมองได้หลายลักษณะปัญหา โดยจำแนกได้ออกมาเป็นดังนี้

1. **การมีอยู่ของผลเฉลยของปัญหา (Existence and uniqueness)**
2. ความเสถียรในโครงสร้าง (Structural stability)[^4]
3. ทฤษฎีการรบกวน (Perturbation theory)[^5]
4. ทฤษฎีการแยกเป็นสองกิ่ง (Bifurcation theory)[^6]

โดยเราจะเริ่มที่การมีอยู่ของผลเฉลยของปัญหาก่อน ซึ่งการหาผลเฉลยของสมการเชิงอนุพันธ์แม้แต่สมการเชิงอนุพันธ์ย่อย ([Partial Differential Equations](https://en.wikipedia.org/wiki/Partial_differential_equation): PDE) ในบางครั้งเราสามารถใช้เทคนิคทางสมการเชิงอนุพันธ์สามัญ ([Ordinary Differential Equations](https://en.wikipedia.org/wiki/Ordinary_differential_equation): ODE) ได้ ซึ่งทฤษฎีทาง ODE จะสามารถนำพาการหาผลเฉลยของสมการได้ โดยเหลือเงื่อนไขเพียงการทำการวนซ้ำจุดตรึง ([fixed point iteration](https://en.wikipedia.org/wiki/Fixed-point_iteration)) เท่านั้น ทำให้การแก้ปัญหาผลเฉลยเป็นไปได้ง่ายยิ่งขึ้น[^1]

การพิสูจน์การมีอยู่ของผลเฉลยสมการสามารถแบ่งออกได้เป็น 2 ทฤษฎีบท คือ

พิจารณา IVP

$$
\begin{cases}
    y^\prime(t) = f(t, y(t))&t \in [a,b],\\\\
    y(t_0) = y_0
\end{cases}
$$

1. ทฤษฎีบทของพิกาค์-ลินเดเลิฟ ([Picard-Lindelöf Theorem](https://en.wikipedia.org/wiki/Picard%E2%80%93Lindel%C3%B6f_theorem)) ซึ่งกล่าวถึงการมีอยู่ของผลเฉลยหนึ่งเดียวของ ODE เฉพาะที่ (Local existence and uniqueness) โดยมีเงื่อนไข $f$ เป็น Lipschitz continuous ใน $y$ และเป็น continuous ใน $t$ ซึ่งมีความแรงมากกว่า Continuous function อยู่มาก แต่หากอีกทฤษฎีบทหนึ่ง
2. ทฤษฎีการมีอยู่ของพิเอโน ([Peano Existence Theorem](https://en.wikipedia.org/wiki/Peano_existence_theorem)) จะมองเพียงแค่ $f$ เป็น continuous function เท่านั้น ซึ่งสิ่งที่ขาดไปในผลลัพธ์ คือ การมีผลเฉลยหนึ่งเดียว (Uniqueness)
3. ทฤษฎีการมีอยู่ของคาราเธโอดอรี ([Carathéodory's existence theorem](https://en.wikipedia.org/wiki/Carath%C3%A9odory%27s_existence_theorem)) จะให้เงื่อนไขที่เบากว่า คือ ให้ $f$ เป็น measurable ใน $t$ แต่เป็น continuous ใน $y$

ณ ที่นี้ขอพูดถึงเพียง Picard-Lindelöf theorem

## Picard-Lindelöf Theorem

<blockquote>

**ทฤษฎีบท** (ทฤษฎีบทของพิกาค์-ลินเดเลิฟ) ให้ $D \subseteq \mathbb{R} \times \mathbb{R}^n$ เป็นเซตสี่เหลี่ยมผืนผ้าปิดที่มี $(t_0, y_0) \in \mathrm{int}\ D$ ซึ่งหมายถึงเซตของจุดภายใน $D$ และให้ $f: D \to \mathbb{R}^n$ เป็นฟังก์ชันต่อเนื่องในตัวแปร $t$ และต่อเนื่องแบบลิพชิทซ์ ([Lipschitz continuous](https://en.wikipedia.org/wiki/Lipschitz_continuity)) ในตัวแปร $y$ จะได้ว่ามี $\varepsilon > 0$ ที่ทำให้ปัญหาค่าเริ่มต้น (Initial value problem: IVT)

$$
\begin{cases}
    y^\prime(t) = f(t, y(t))&t \in [t_0 - \varepsilon, t_0 + \varepsilon],\\\\
    y(t_0) = y_0
\end{cases}
$$

</blockquote>

ทฤษฎีบทนี้อาศัยทฤษฎีบทจุดตรึงบานาก (Banach fixed-point theorem) ในการสร้างผลเฉลยของสมการเพื่อพิสูจน์ว่ามีอยู่จริงเพียงหนึ่งเดียว

เพื่อความสะดวก กำหนดให้ $a = t_0 - \varepsilon$ และ $b = t_0 + \varepsilon$

*บทพิสูจน์*. ให้ว่า $T: C([a, b]) \to C([a, b])$ เป็น operator ซึ่ง

$$
T[\varphi]\(t\) = y_0 + \int_{t_0}^t f(s, \varphi(s))\ ds
$$

โดยเราจะเห็นว่าถ้า $y \in C([a,b])$ เป็น fixed point ของ $T$ เราจะเห็นได้ว่า $y$ เป็นผลเฉลยของ IVT ข้างต้น (สามารถตรวจสอบได้ด้วยการทำอนุพันธ์ของ $T[y]$ และดู $T[y]\(t_0\)$) หากจะทำให้ fixed point มีเพียงจุดเดียว จะต้องพิสูจน์ให้ได้ว่า $T$ เป็น contraction mapping โดย

$$ d(T(f), T(g)) \leq \lambda d(f, g) $$

ซึ่ง $0 \leq \lambda < 1$ ตาม Banach fixed-point theorem

คำถามที่เกิดขึ้นจึงปรากฏอยู่ 2 คำถาม คือ

1. Metric $d$ ของ $C([a,b])$ คืออะไร
2. $\lambda$ มีค่าเท่าไหร่

## Metric ของปริภูมิคืออะไร

เราให้ว่า $d: C([a,b]) \times C([a,b]) \to [0, \infty)$ กำหนดนิยามออกมาดังนี้

$$d(f,g) = \sup_{x \in [a,b]} |f(x) - g(x)|$$

สำหรับทุก $f, g \in C([a,b])$

สิ่งที่จะต้องพิสูจน์ คือ $d$ มีความบริบูรณ์หรือไม่และ $d$ เป็น metric หรือไม่

เริ่มจากการดู $d$ ว่าเป็น metric หรือไม่

<details>
<summary><b>พิสูจน์</b> $d$ เป็น metric บน $C^1([a,b])$ </summary>

1) ระยะทางจากตัวเองไปหาตัวเองจะต้องเท่ากับศูนย์ 

ให้ $f \in C([a,b])$

$$
\begin{aligned}
d(f,f) &= \sup_{x \in [a,b]} |f(x) - f(x)|\\\\
&= \sup_{x \in [a,b]} |0|\\\\
&= 0.
\end{aligned}
$$

2) ระยะทางจากวัตถุใด ๆ ที่แตกต่างกันในปริภูมิจะต้องมีค่ามากกว่า 0

ให้ $f, g \in C([a,b])$ โดย $f \neq g$

นั่นหมายถึงจะต้องมีบางจุด $x \in [a,b]$ ที่ $f(x) \neq g(x)$ ซึ่งกำหนดโดยไม่สูญเสียนัยทั่วไป เราจะให้ $f(x) > g(x)$ หรือแปลว่ามี $\delta > 0$ ที่ทำให้ $f(x) - g(x) = \delta$

เราจะได้ว่า

$$
\begin{aligned}
d(f,g) &= \sup_{x \in [a,b]} |f(x) - g(x)|\\\\
&\geq |\delta|\\\\
&> 0.
\end{aligned}
$$

3) ระยะทางมีความสมมาตร

เป็นที่ชัดเจนอยู่แล้วว่า $d$ มีคุณสมบัตินั้นจริง (เทียบนิยาม)

4) มีคุณสมบัติสามเหลี่ยม

ให้ $f, g, h \in C([a,b])$

เราจะพบว่า

$$\begin{aligned}
d(f,h) &= \sup_{x \in [a,b]}|f(x) - h(x)|\\\\
&= \sup_{x \in [a,b]}|f(x) - g(x) + g(x) - h(x)|\\\\
&\leq \sup_{x \in [a,b]} (|f(x) - g(x)| + |g(x) - h(x)|)\\\\
&\leq \sup_{x \in [a,b]} |f(x) - g(x)| + \sup_{x \in [a,b]} |g(x) - h(x)|\\\\
&= d(f,g) + d(g,h)
\end{aligned}$$

จึงแปลได้ว่า $d$ เป็น metric บน $C([a,b])$

</details>

แล้ว $(C([a,b]),d)$ เป็น complete metric space หรือไม่

<details>
<summary><b>คำตอบ</b> $(C([a,b]), d)$ เป็นปริภูมิเมตริกบริบูรณ์</summary>

*บทพิสูจน์*. ให้ $\lbrace f_n\rbrace_{n \in \mathbb{N}}$ เป็น Cauchy sequence ใน $C([a,b])$

สำหรับ $r > 0$ จะมี $N \in \mathbb{N}$ ที่ทำให้ทุก $n,m \geq N$ เป็นไปตามอสมการ

$$
d(f_n, f_m) < r
$$

จะทำให้สำหรับทุก $x \in [a,b]$ มี $|f_n(x) - f_m(x)| < r$

ให้นิยาม $f = \lim_{n \to \infty} f_n$ จะให้ว่า

$$\begin{aligned}
\lim_{n \to \infty}| f_m(x) - f_n(x) | &= | f_m(x) - \lim_{n \to \infty}f_n(x)|\\\\
&= | f_m(x) - f(x) |\\\\
&\leq r
\end{aligned}$$

กำหนดให้ $\epsilon = 2r$ จะได้ว่า $|f_m(x) - f(x)| < \epsilon$

ได้ว่า $f_n$ นั้น uniform convergent จึงให้ได้ว่า $f$ เป็นฟังก์ชันต่อเนื่อง ซึ่งสรุปว่า $f \in C([a,b])$

ดังนั้น $(C([a,b]), d)$ จึงเป็นปริภูมิเมตริกบริบูรณ์.

</details>

## Operator ที่เรานิยามเป็น contraction mapping หรือไม่

เราจะพบว่า operator $T$ เป็น contraction mapping ใน $(C([a,b]), d)$

*พิสูจน์*. พิจารณา $f,g \in C([a,b])$ พบว่า

$$
\begin{aligned}
d(T[f],T[g]) &= \sup_{t \in [a,b]} |T[f] - T[g]|\\\\
&= \sup_{t \in [a,b]} \left|\int_{t_0}^t F(s,f(s)) - F(s,g(s))\ ds\right|
\end{aligned}
$$

เนื่องจาก $F$ เป็น Lipschitz continuous ใน $y$ จะมี $K > 0$ ที่ทำให้ $|F[f]\(x\) - F[g]\(x\)| \leq K|f(x)-g(x)|$ สำหรับ $x \in [a,b]$.

$$
\begin{aligned}
\phantom{d(T[f],T[g])} &\leq \sup_{t\in [a,b]} \int_{t_0}^t |F(s,f(s)) - F(s,g(s))|\ ds\\\\
&\leq \sup_{t\in [a,b]} \int_{t_0}^t K|f(s)-g(s)|\ ds
\end{aligned}
$$

เนื่องจาก $f, g$ เป็นฟังก์ชันต่อเนื่องใน bounded set $[a,b]$ จะได้ว่า $f-g$ จะเป็น bounded function ให้ว่ามี sup norm

เลือก $\varepsilon = \frac{1}{2K}$

$$
\begin{aligned}
&\leq K\varepsilon \sup_{t\in [a,b]} |f(t)-g(t)|\\\\
&= \frac{1}{2} d(f,g)
\end{aligned}
$$

จึงสามารถสรุปได้ว่าจาก Banach fixed point theorem ตัว operator $T$ มี fixed point เพียงหนึ่งเดียว ซึ่งวิธีการให้ได้ซึ่ง fixed point จะใช้กำหนดเป็น Picard iteration โดยให้ $f \in C([t_0-1/2K,t_0+1/2K])$

$$\varphi_n(t) = \begin{cases}
    f(t) & n = 0\\\\
    y_0 + \int_{t_0}^t F(s,\varphi_{n-1}(s))\ ds & n > 0
\end{cases}$$

ซึ่งต้องพิสูจน์

โดยหากพิจารณาการพิสูจน์ดังนี้ เราจะพบว่าเราสามารถทำ generalization ของ IVP ให้อยู่ในรูปแบบระบบสมการ ODE $(*)$ ได้เช่นกัน

$$
\begin{cases}
    \frac{dx_1}{dt} = X_1(t, x_1, \dots, x_n)&\text{on }[t_0 - \varepsilon, t_0 + \varepsilon]\\\\
    \dots\\\\
    \frac{dx_n}{dt} = X_n(t, x_1, \dots, x_n)&\text{on }[t_0 - \varepsilon, t_0 + \varepsilon]\\\\
    x_1(t_0) = x_1^0,\ \dots,\ x_n(t_0) = x_n^0
\end{cases}
$$

โดยให้ $X_1, \dots, X_n$ เป็น Lipschitz continuous ในช่วง $[t_0 - \varepsilon, t_0 + \varepsilon]$ ซึ่งหากมองอยู่ในรูปแบบเวกเตอร์จะได้ว่า

$$
\begin{cases}
    \frac{d\mathbf{x}}{dt}(t) = \mathbf{X}(t, \mathbf{x})&\text{on }[t_0 - \varepsilon, t_0 + \varepsilon]\\\\
    \mathbf{x}(t_0) = \mathbf{x}_0
\end{cases}
$$

ซึ่งให้ $\mathbf{X}$ เป็น Lipschitz continuous vector field ก็จะให้ผลลัพธ์เดียวกับทฤษฎี Picard-Lindelöf โดยเลือก $\varepsilon > 0$ ที่เหมาะสมคล้ายคลึงกับที่เราทำก่อนหน้านี้

### Local existence ของ ODE อันดับสูง

นอกจากนี้แล้ว แม้ทฤษฎีบทนี้กำลังหา local existence และ uniqueness ของ ODE อันดับ 1 แท้จริงแล้ว เราสามารถใช้กับ ODE อันดับสูงได้อีกด้วย โดยให้ $n\in \mathbb{N}$ แล้วพิจารณา IVP ดังนี้

$$
\begin{cases}
    \frac{d^n y}{dt^n} = F\left(t, y, \frac{dx}{dt}, \dots, \frac{d^{n-1} y}{dt^{n-1}}\right)&\text{on }[t_0 - \varepsilon, t_0 + \varepsilon]\\\\
    y(t_0) = y_0,\ \frac{dy}{dt}(t_0) = y^{(1)}_0,\ \dots,\ \frac{d^{n-1}y}{dt^{n-1}} = y^{(n-1)}_0
\end{cases}
$$

โดย $F$ เป็น locally Lipschitz เราจะเขียนอยู่ในรูปแบบระบบสมการ ODEs ได้ดังนี้

ให้ $x_1 = y$

$$
\begin{cases}
    \frac{dx_1}{dt}(t) = x_2\\\\
    \frac{dx_2}{dt}(t) = x_3\\\\
    ...\\\\
    \frac{dx_n}{dt}(t) = F(t, x_1, \dots, x_{n-1})
\end{cases}
$$

ซึ่งถ้าหากว่า $F$ เป็น Lipschitz continuous เราจะบอกได้ว่า $\mathbf{X}$ ตามระบบสมการ $(*)$ คือ $(\pi_2, \pi_3, \dots, \pi_{n-1}, F)$ ก็เป็น Lipschitz continuous เช่นกัน โดย $\pi_i(t, \mathbf{x}(t)) = x_i(t)$ เป็น projection operator ที่เรารู้ว่าเป็น Lipschitz continuous โดยชัดแจ้ง ฉะนั้นแล้วเราจึงสามารถใช้ Picard-Lindelöf theorem สำหรับสมการอนุพันธ์อันดับสูงได้เช่นกัน

## แล้ว ODE ของเรามี Global Existence หรือไม่

จาก Picard-Lindelöf theorem เราไม่สามารถสรุปได้ว่า IVP ที่เราพิจารณาอยู่หากกำหนดช่วงที่สนใจใด ๆ จะมี solution หรือไม่ โดยการสร้าง global solution ของ IVP จะทำตามแนวคิดดังต่อไปนี้

---

หมายเหตุ:
- เราจะพิจารณา local existence ในเซต $[t_0, t_0 + \varepsilon]$ โดย $\varepsilon$ มาจาก Picard-Lindelöf theorem
- เราจะพิจารณา IVP $(**)$ ดังนี้ ให้ $a,b \in \mathbb{R}$ โดย $a < b$ และ $\mathbf{F}: \mathbb{R}^n \times [a,b] \to \mathbb{R}^n$

$$
\begin{cases}
    \frac{d\mathbf{y}}{dt}(t) = \mathbf{F}(\mathbf{y}(t), t) &\text{on } [a,b]\\\\
    \mathbf{y}(a) = \mathbf{y}_0
\end{cases}
$$

---

1. เราพิจารณาเริ่มต้นที่ $t = a$ ก่อน ซึ่งถ้าหากมี local existence ตาม Picard-Lindelöf theorem เราจะได้ $\varepsilon_0$ มาตั้ง IVP ได้ว่า

$$
\begin{cases}
    \frac{d\mathbf{y}}{dt}(t) = \mathbf{F}(\mathbf{y}(t), t) &\text{on } [a,a + \varepsilon_0]\\\\
    \mathbf{y}(a) = \mathbf{y}_0
\end{cases}
$$

2. จากนั้นเราก็จะพิจารณา IVP ใหม่แล้วพยายามหา $\varepsilon_n$ จากการหา local existence ของ IVP นี้

$$
\begin{cases}
    \frac{d\tilde{\mathbf{y}}}{dt}(t) = \mathbf{F}(\tilde{\mathbf{y}}(t), t)\text{ on } [a + \varepsilon_0 + \cdots + \varepsilon_{n-1},a + \varepsilon_0 + \cdots + \varepsilon_n]\\\\
    \tilde{\mathbf{y}}(a + \varepsilon_0 + \cdots + \varepsilon_{n-1}) = \mathbf{y}(a + \varepsilon_0 + \cdots + \varepsilon_{n-1})
\end{cases}
$$

แล้วจากนั้นจึงนำ solution มาต่อกันเป็น $\mathbf{y}|\_{[a + \varepsilon_0 + \cdots + \varepsilon_{n-1}, a + \varepsilon_0 + \cdots + \varepsilon_n]} = \tilde{\mathbf{y}}$

ทว่าปัญหาที่เกิดขึ้น คือ เรารู้ได้อย่างไรว่า $\mathbf{y}$ จะสามารถหาคำตอบได้ตลอดช่วงย่อยใน $[a,b]$ (นัยเดียวกับสามารถใช้ Picard-Lindelöf theorem ได้)

ซึ่งจะพบว่าหากเราใช้วิธีตามที่เรากล่าวด้านบน เราจะได้ "ช่วงที่กว้างที่สุดที่สามารถให้คำตอบ IVP ได้" (maximal interval of existence) โดยอาศัย Zorn's lemma[^2] ในการยืนยันการมีอยู่ของช่วงนี้

หากว่า solution ของ IVP นั้นอยู่ในช่วงที่กว้างที่สุดที่สามารถให้คำตอบ IVP ได้ เราสามารถเรียกได้ 3 แบบ[^3] คือ 

1. Unbounded solution (blow-up)
2. non-global saturated solution ถ้าหากว่าช่วงนั้นไม่เท่ากับ $[a,b]$ ซึ่ง limit point ของ solution จะอยู่ที่ boundary ของ $\Omega$ เมื่อมอง $\mathbf{F}: \Omega \times [a,b] \to \mathbb{R}^n$
3. global solution ถ้าหากว่าช่วงนั้นเท่ากับ $[a,b]$ 

ซึ่งสำหรับ existence ของ global IVP solution นั้นจะมีเงื่อนไขพิเศษที่ไม่ต้องพิจารณาเป็น iteration อยู่ดังนี้

<blockquote>

**ทฤษฎีบท** (Global IVP solution) ถ้าพิจารณา IVP $(**)$ โดยที่

$$\lVert \mathbf{F}(\mathbf{y}(t), t)\rVert \leq h(t)\lVert \mathbf{y}(t) \rVert + k(t)$$

ซึ่ง $h, k: [a,b] \to \mathbb{R}$ เป็นฟังก์ชันต่อเนื่อง แล้ว IVP จะมีผลเฉลย (หนึ่งเดียว)

</blockquote>

บทพิสูจน์ของทฤษฎีบทนี้อาศัย Grönwall's inequality ในการพิสูจน์

<details>

<summary>Grönwall's inequality กล่าวถึงอะไร</summary>

<blockquote>

**บทตั้ง** (Grönwall's inequality) ถ้าเรามี $\beta, h: [a,b] \to \mathbb{R}$ เป็นฟังก์ชันต่อเนื่องและ $\beta(x) \leq 0$ ในทุก $x \in [a,b]$ ซึ่งหาก $ h(x) \leq \alpha + \int_{a}^x \beta(s)h(s)\ ds $ จะได้ว่า

$$
h(x) \leq \alpha \exp\left(\int_{a}^x \beta(s)\ ds\right) 
$$

</blockquote>

*บทพิสูจน์*. ให้ $\psi(x) = \alpha + \int_{a}^x \beta(s)h(s)\ ds $ จะได้ว่า

$$
\frac{d\psi}{dx}(x) = \beta(x) h(x) \leq \beta(x) \psi(x)
$$

หากเราแก้สมการเชิงอนุพันธ์จะได้ว่า

$$
\psi(x) \leq \alpha \exp\left(\int_{a}^x \beta(s)\ ds\right)
$$

ซึ่งเรากำหนดไว้ว่า $h(x) \leq \psi(x)$ ซึ่งต้องพิสูจน์.

</details>

*บทพิสูจน์*. ให้ $y$ เป็น saturated solution ของ IVP, $t \in [a,b]$ เราจะพิจารณา norm ของ $\mathbf{y}(t) = \mathbf{y}\_0 + \int\_{a}^x \mathbf{F}(\mathbf{y}(s), s)\ ds$ ซึ่งได้ว่า

$$
\begin{aligned}
    \lVert \mathbf{y}(t) \rVert &= \left\lVert \mathbf{y}_0 + \int_a^t \mathbf{F}(s,\mathbf{y}(s))\ ds \right\rVert\\\\
    &\leq \lVert \mathbf{y}_0 \rVert + \int_a^t \lVert \mathbf{F}(\mathbf{y}(s),s)\rVert\ ds\\\\
    &\leq \lVert \mathbf{y}_0 \rVert + \int_a^t h(s)\lVert \mathbf{y}(s) \rVert + k(s)\ ds
\end{aligned}
$$

เนื่องจากเป็นฟังก์ชันต่อเนื่อง จะมี $M > 0$ ที่ทำให้ $k(t) \leq M$ ในทุก $t \in [a,b]$

$$
\begin{aligned}
    &\leq \lVert \mathbf{y}\_0 \rVert + \int\_a^t k(s)\lVert \mathbf{y}(s) \rVert + M\ ds\\\\
    &\leq \underbrace{\left(\lVert \mathbf{y}\_0 \rVert + M (t-a)\right)}\_{\alpha} + \int_a^t \underbrace{h(s)}_{\beta(s)}\lVert \mathbf{y}(s) \rVert\ ds
\end{aligned}
$$

ใช้ Grönwall's inequality จะได้ว่า

$$
\lVert \mathbf{y}(t) \rVert \leq \left(\lVert \mathbf{y}\_0 \rVert + M (t-a)\right)\exp\left(\int_a^t h(s)\ ds\right)
$$

เราจะเห็นว่า $\mathbf{y}$ bounded จึงเหลือเพียง 2 กรณี แต่ว่า $\mathbb{R}^n$ ไม่มี boundary จึงทำให้ $\mathbf{y}$ เป็น global solution

ซึ่งต้องพิสูจน์.

---

**เชิงอรรถ**

[^1]: [Why we study abstract differential equations in Banach and Hilbert spaces? - Math StackExchange](https://math.stackexchange.com/questions/3335498/why-we-study-abstract-differential-equations-in-banach-and-hilbert-spaces)
[^2]: Zorn's lemma เป็นสัจพจน์ที่นักคณิตวิเคราะห์ยอมรับ ซึ่งเทียบได้กับ Axiom of choice ($\mathrm{AC}$) หากพิจารณาในทางทฤษฎีเซต นักคณิตศาสตร์มักใช้ axiomatic set theory ของ Zermelo-Fraenkel ($\mathrm{ZF}$) รวมกับ $\mathrm{AC}$ ซึ่งเราจะเรียกระบบสัจพจน์โดยลำลองว่า $\mathrm{ZFC}$ โดยในกรณีนี้ เราจะนิยาม $\leq$ ว่าเป็น restriction ของ solution ซึ่งความพิเศษของเซตนี้ คือ เซตเป็น linear order จึงพิจารณาได้โดยง่ายว่า subset ของเซตนี้ย่อมมี upper bound เสมอ
[^3]: ดูเพิ่มเติมที่[ทฤษฎีบท 1.42](https://www.math.iitb.ac.in/~siva/npdeiitropar.pdf) หน้า 23
[^4]: บันทึกไว้ก่อนว่าจะมาดูลิงก์[นี้](https://math.uchicago.edu/~may/REU2024/REUPapers/Khansa.pdf) ซึ่งกล่าวคร่าว ๆ เกี่ยวกับ structural stability ว่าเป็นการดู topological conjugacy ของ vector fields
[^5]: โดยอาจมองเป็นการสังเกตปรากฏการณ์เปลี่ยนแปลง vector field เป็นตัวอื่นใน $\epsilon$-neighborhood ของ vector field บนปริภูมิ $\mathrm{Diff}^r(U)$ ซึ่ง $U \subseteq X$ ก็ได้ หากให้ $X$ เป็น topological space แล้ว $\mathrm{Diff}^r$ หมายถึง set of all $r$-times differentiable vector fields (smooth section) ซึ่งรบกวนในระดับ $\epsilon > 0$ เล็ก ๆ
[^6]: เป็นการศึกษาเกี่ยวกับการเปลี่ยนแปลงโครงสร้างของ phase portraits ในมุมมองของ topological conjugate เราอาจใช้ normal form เพื่อพิจารณาหาก็ได้ โดย normal form อาจสามารถให้ center manifold, stable manifold และคุณสมบัติอื่น ๆ ของ dynamical systems ในเรื่อง stability และ bifurcation ได้อีกด้วย