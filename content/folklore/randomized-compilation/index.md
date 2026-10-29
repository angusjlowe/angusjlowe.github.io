---
title: "Randomized Compilation"
linkTitle: "Randomized compilation"
topic: qalgs
date: 2026-03-26
lastmod: 2026-03-26
description: "A simple Hamiltonian that needs Ω(n) gates to simulate deterministically but only O(1) gates with randomized compilation."
aliases: ["/folklore/qalgs/randomized-compilation.html"]
---

A growing body of work in the last few years points to randomization being a powerful tool for simulating quantum processes. For example, the fact that one can use a linear combination of noisy quantum channels to simulate the action of a unitary is the basis for the probabilistic error cancellation method in error mitigation. However, this is an example where noise is used to counteract noise, and the improvements gained are necessarily modest. Perhaps more strikingly, randomness can also sometimes give dramatically improved methods in the noise-free setting where one wishes to simulate unitaries with other unitaries. For instance, it has recently been shown[^gkz25] that an n-qubit Toffoli gate can be implemented with exponentially fewer T gates using randomly drawn circuits, and other works[^Cam17]<sup>,</sup>[^CHKT21] draw attention to randomization reducing Trotter steps in Hamiltonian simulation. While we are used to thinking of uniquely quantum phenomena (e.g., entanglement) as resources for improving classical information processing tasks, these examples flip the script on this mantra: in the physically realistic setting of limited coherent control, classical randomness can be a resource for quantum information processing tasks.

In this note, I wanted to record my understanding of a clean example of the phenomenon that has been noted elsewhere[^CHKT21]. Without being too precise for the moment, the claim is as follows.

> There is a simple, physically realistic Hamiltonian $H$ which requires $\Omega(n)$ unitary gates to simulate to constant accuracy, but only $O(1)$ gates if one allows for randomness.

Suppose that one's goal is to implement the gate
$$
U=e^{i\pi\frac{1}{n}\sum_{i=1}^nZ_i}
$$
which is a product of $Z$ rotations. Evidently, we need at least $\lceil n/2\rceil$ one and two-qubit gates to implement it exactly: otherwise, we cannot even affect all $n$ qubits. Now, if we allow for some error tolerance, say, $\epsilon$, then we can try to get away with fewer gates via an approximation $V$ such that the error in diamond distance is at most $\epsilon$. It is easier to work with the quantity $\nu(W)=\min_{\ket{\psi}}|\langle \psi | W | \psi \rangle|$, which is reasonable since it is not difficult to see that for unitary channels $\mathcal{U},\mathcal{V}$ corresponding to unitaries $U$, $V$, respectively, we have
$$
\frac{1}{2}{\lVert \mathcal{U}-\mathcal{V}\rVert}_{\diamond} = \sqrt{1-\nu(U^\dagger V)^2}.
$$
The state $\ket{\psi}$ attaining the minimum is a witness for the distinguishability of the two unitaries: evolving by one of them gives a different state compared to evolution by the other, and the overlap is less than one. This difference can be used to distinguish the unitaries. Our goal will be to make sure that our simulation is good enough to fool an adversary who may try to make use of such a worst-case state. This is impossible to do without many gates deterministically, but can be accomplished using a random selection of a few gates.

## The lower bound

Suppose we have fewer than $n/8$ two-qubit gates. Then w.l.o.g. the first $m = 3n/4$ qubits are acted on by identity in the approximation. Also
$$
\nu(U\otimes V)\leq \min_{\ket{\psi}\otimes\ket{\phi}}|\bra{\psi}U\ket{\psi}||\bra{\phi}V\ket{\phi}| \leq \min_{\ket{\psi}}|\bra{\psi}U\ket{\psi}|
$$
implies
$$
\nu(e^{i\pi\sum_{i=1}^{m}Z_i/n}\otimes U)\leq \nu(e^{i\pi\sum_{i=1}^{m}Z_i/n})
$$
for any $U$, and the latter quantity is
$$
\begin{align}
|\bra{\textnormal{cat}}e^{i\pi\sum_{i=1}^mZ_i/n}\ket{\textnormal{cat}}| &= \frac{1}{2}|(\bra{00\dots 0}+\bra{11\dots 1})(e^{i\pi m/n}\ket{00\dots 0}+e^{-i\pi m/n}\ket{11\dots 1})|\\
&= |\cos(\pi m/n)|\\
&=|\cos(3\pi/4)|\\
&= \frac{1}{\sqrt{2}}.
\end{align}
$$
So the error in diamond distance is at least $1/2$ so long as there are fewer than $n/8$ gates.

## The upper bound

Next, let's allow for randomizing the approximation. More precisely, *we allow the user to implement a random unitary $V$ which is comprised of at most $m$ gates with probability one*. Let $\hat{V}\equiv \mathbb{E}V$. It can be shown[^footnote] that
$$
\frac{1}{2}{\lVert \mathcal{U}-\mathbb{E}\mathcal{V}\rVert}_\diamond \leq 2\lVert U-\hat{V}\rVert
$$
where $\mathcal{U}:\rho\mapsto U\rho U^\dagger$ and $\mathcal{V}: \rho\mapsto V\rho V^\dagger$. So it remains to bound the qDRIFT error where we sample $i$ with probability $1/n$ for each $i\in [n]$, evolve this single qubit by $e^{i\pi Z_i /m}$, and repeat this $m$ times. First, note that for $V$ a product of random $V_1,\dots, V_m$ we have
$$
\begin{align}
    \lVert \mathbb{E}V - U\rVert &= \lVert\mathbb{E}\left(V_m\dots V_1 + U^{1/m}V_{m-1}\dots V_1 - U^{1/m}V_{m-1}\dots V_1 - U^{1/m}\dots U^{1/m}\right)\rVert\\
    &\leq \lVert U^{1/m}\mathbb{E}(V_{m-1}\dots V_1-U^{1/m}\dots U^{1/m})\rVert + \lVert \mathbb{E}(V_m -U^{1/m})V_{m-1}\dots V_1\rVert
\end{align}
$$
The first term equals $\lVert \mathbb{E}V_{m-1}\dots V_1 - U^{1/m}\dots U^{1/m}\rVert$. The second term can be bounded using
$$
\begin{align}
    {\lVert(\mathbb{E}(V_m -U^{1/m})V_{m-1}\dots V_1)\rVert} &\leq \mathbb{E}_{V_{m-1},\dots, V_1}{\lVert \mathbb{E}(V_m -U^{1/m})V_{m-1}\dots V_1\rVert}\\
    &= \lVert \mathbb{E} V_m - U^{1/m}\rVert.
\end{align}
$$
Continuing this logic, we arrive at
$$
\begin{align}
    \lVert \mathbb{E}V - U\rVert&\leq \sum_{j=1}^m \lVert \mathbb{E}V_j - U^{1/m}\rVert.
\end{align}
$$
Hence, if $V_1,\dots, V_m$ are iid, we get $\lVert \hat{V}-U\rVert\leq m \lVert\mathbb{E}V_1 - U^{1/m}\rVert$. In our case, we have $U=e^{i\pi \frac{1}{n}\sum_{i=1}^n Z_i}$ and $V_1 = e^{i\pi X/m}$ where $X=Z_i$ with probability $p_i\equiv 1/n$. We can evaluate
$$
\mathbb{E}V_1 = \cos(\pi/m)I+i\sin(\pi/m)H
$$
where we have used the shorthand $H=\tfrac{1}{n}\sum_{i=1}^n Z_i$. Hence,
$$
\begin{align}
    \lVert\mathbb{E} V_1 - U^{1/m}\rVert &= \lVert\cos(\pi/m)I + i\sin(\pi/m)H - e^{i \pi H/m}\rVert\\
    &= \lVert\sum_{j=0,2,4,\dots}\frac{1}{j!}\left(\frac{\pi}{m}\right)^j (I-i^jH^j) + \sum_{j=1,3,5,\dots}\frac{1}{j!}\left(\frac{\pi}{m}\right)^j(iH - i^jH^j)\rVert\\
    &\leq \sum_{j=0,2,4,\dots}\frac{1}{j!}\left(\frac{\pi}{m}\right)^j \lVert I-i^jH^j\rVert + \sum_{j=1,3,5,\dots}\frac{1}{j!}\left(\frac{\pi}{m}\right)^j\lVert iH - i^jH^j\rVert\\
    &= \sum_{j=2,4,\dots}\frac{1}{j!}\left(\frac{\pi}{m}\right)^j \lVert I-i^jH^j\rVert + \sum_{j=3,5,\dots}\frac{1}{j!}\left(\frac{\pi}{m}\right)^j\lVert iH - i^jH^j\rVert\\
    &= \left(\frac{\pi}{m}\right)^2\sum_{j=0,2,\dots}\frac{1}{(j+2)!}\left(\frac{\pi}{m}\right)^j \lVert I-i^{j+2}H^{j+2}\rVert 
    \\ &\qquad\qquad\qquad+ \left(\frac{\pi}{m}\right)^3\sum_{j=3,5,\dots}\frac{1}{(j+3)!}\left(\frac{\pi}{m}\right)^{j}\lVert iH - i^{j+3}H^{j+3}\rVert\\
    &\leq 2\left(\frac{\pi}{m}\right)^2\exp(\pi)+2\left(\frac{\pi}{m}\right)^3\exp(\pi)\\
    &= \frac{2\pi^2e^{\pi}}{m^2}\left(1 + \frac{\pi}{m}\right)\\
    &= O(m^{-2})
\end{align}
$$
where, in the second-to-last line, we used triangle inequality for the operator norm and bounded the infinite series from above. Thus,
$$
\lVert \hat{V} - U\rVert = O(m^{-1})
$$
and in order to attain error $\epsilon$ in diamond error, it suffices to take $m=\Theta(\epsilon^{-1})$. Hence, for sufficiently small error, $m=\Omega(n)$ gates are required deterministically while $m=O(1)$ suffices if one uses randomness.

[^gkz25]: D. Gosset, R. Kothari, and C. Zhang. "Multi-qubit Toffoli with exponentially fewer T gates". arXiv:2510.07223.

[^Cam17]: E. Campbell. "A random compiler for fast Hamiltonian simulation". *Physical Review Letters* 123, 070503 (2019).

[^CHKT21]: C. Chen, H. Huang, R. Kueng, and J. Tropp. "Concentration for Random Product Formulas". *PRX Quantum* 2, 040305 (2021).

[^footnote]: This is left as a (non-trivial) exercise to the reader, mainly involving adding and subtracting terms and Hölder's inequality.
