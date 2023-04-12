# Solution of the difference equation

We are going to solve equation {eq}`eqAdvection` numerically using discretized time and space axis in the following way:

$$
	x_m &= m\Delta x, m=0,1,2,...,M \\
	t^n &= n\Delta t, n=0,1,2,...,N,
$$ (eqDiscretization)

where $\Delta t$ is the time step and $\Delta x$ is the grid resolution. Using second order finite difference approximations of the derivatives, we get:

$$
	\frac{u_{m}^{n+1} - u_{m}^{n-1}}{2\Delta t } + c \frac{u_{m+1}^{n} - u_{m-1}^{n}}{2\Delta x} = 0,
$$ (eqDiffEquation)

which is the difference equation corresponding to the discretization of {eq}`eqAdvection` by the second order centred finite difference formulas for the first derivative. Rearranging the terms of {eq}`eqDiffEquation` we obtain the time marching scheme:

$$
	u_{m}^{n+1} = u_{m}^{n-1} - c\frac{\Delta t}{\Delta x}(u_{m+1}^{n}-u_{m-1}^{n}),
$$ (eqLeapfrog)

which is referred to as a three-level scheme, because it employs information at time levels $n-1$, $n$ and $n+1$. It is also known as the *leapfrog* scheme.

We shall solve {eq}`eqDiffEquation` analytically because we want to illustrate some aspects of the numerical solution of {eq}`eqAdvection` by the leapfrog scheme. Indeed, while it is equation {eq}`eqAdvection` that we ultimately want to solve, the time marching scheme will effectively solve {eq}`eqDiffEquation`. Thus, by studying the solution to {eq}`eqDiffEquation` and especially how close it is to (\ref{eqn:anaSol}), the true solution of (\ref{eqn:linear1dadvection}), we shall be able to say whether or not our time marching method is suitable to the task. 

We assume an exponential solution to {eq}`eqDiffEquation`:

$$
	u_{m}^{n} =B^{n\Delta t}e^{i\mu m\Delta x}, 
$$ (eqDiffSolution)

where $B$ can be complex. Substituting in {eq}`eqDiffEquation`, we have:

\begin{align*}
        (B^{(n+1)\Delta t} - B^{(n-1)\Delta t})e^{i\mu m\Delta x} &= -c\frac{\Delta t}{\Delta x} B^{n\Delta t}(e^{i\mu (m+1)\Delta x}-e^{i\mu (m-1)\Delta x}) \Leftrightarrow \\
        (B^{\Delta t} - B^{-\Delta t})B^{n\Delta t} e^{i\mu m\Delta x} &= -c\frac{\Delta t}{\Delta x} B^{n\Delta t}e^{i\mu m\Delta x}(e^{i\mu \Delta x}-e^{-i\mu \Delta x}) \Leftrightarrow \\
        (B^{\Delta t} - B^{-\Delta t}) &= -c\frac{\Delta t}{\Delta x} (e^{i\mu \Delta x}-e^{-i\mu \Delta x}) 
\end{align*}

Since $e^{i\theta}=\cos \theta +i\sin \theta$, we can write:

\begin{align*}
        (B^{\Delta t} - B^{-\Delta t}) &= -c\frac{\Delta t}{\Delta x} (\cos (\mu \Delta x) +  i\sin (\mu \Delta x) -
        \cos (\mu \Delta x) +  i\sin (\mu \Delta x)) \Leftrightarrow \\
        (B^{\Delta t} - B^{-\Delta t}) &= -c\frac{\Delta t}{\Delta x} (2i\sin (\mu \Delta x))
\end{align*}

and then multiply by $B^{\Delta t}$ to get:

\begin{align*}
        B^{2\Delta t} - 1 &= -c\frac{\Delta t}{\Delta x} (\cos (\mu \Delta x) +  i\sin (\mu \Delta x) -
        \cos (\mu \Delta x) +  i\sin (\mu \Delta x)) \Leftrightarrow \\
        B^{2\Delta t} - 1 &= -2iB^{\Delta t} c\frac{\Delta t}{\Delta x} (\sin (\mu \Delta x))  \Leftrightarrow \\
        B^{2\Delta t}  + & 2i\sigma B^{\Delta t} -1 =  0,
\end{align*}

with $\sigma = c\frac{\Delta t}{\Delta x} \sin (\mu \Delta x)$. Solving for $B^{\Delta t}$, we obtain:

$$
	B^{\Delta t}_\pm = -i\sigma \pm \sqrt{1-\sigma^2}
$$ (eqBRoots)

The roots $B^{\Delta t}_\pm$ will have different behaviours, depending on the magnitude of $|\sigma|$. For $|\sigma| \leq 1$ we obtain two complex roots that we can express in polar form[^labPolarForm]. For $B^{\Delta t}_+$, we have:

\begin{align*}
	B^{\Delta t}_+ &= \left[ (\sqrt{1-\sigma^2})^2+(-\sigma)^2\right]^{1/2}\exp (i\arctan \frac{-\sigma}{\sqrt{1-\sigma^2}}) \\
	&= \left[1-\sigma^2+\sigma^2\right]^{1/2}\exp (i\arctan \frac{-\sigma}{\sqrt{1-\sigma^2}}) \\
	&= \exp (i\arctan \frac{-\sigma}{\sqrt{1-\sigma^2}}) \\
	&= \exp (-i\arcsin \sigma) \\
	&= e^{-i\alpha},
\end{align*}	

with $\alpha = \arcsin \sigma$, while for $B^{\Delta t}_{-}$, we obtain a similar expression:

\begin{equation}
	B^{\Delta t}_{-}= e^{i(\alpha+\pi)}.
\end{equation}

With the roots of $B^{\Delta t}$, we can return to {eq}`eqDiffSolution` and write:

$$
	u_{m}^{n} =\left[ De^{-i\alpha n} + Ee^{-i(\alpha+\pi) n} \right]e^{i\mu m\Delta x} 
$$ (eqDiffSolution2)

as the sum of the solutions for each root of $B^{\Delta t}$, since {eq}`eqAdvection` is linear. The constants $D$ and $E$ are to be found through the initial condition. At the initial time, $n=0$, and {eq}`eqDiffSolution2` becomes

$$
	u_{m}^{0} =\left( D + E \right) e^{i\mu m\Delta x},
$$

from which we conclude that $A=D+E$. For subsequent times ($n\ge1$), we have:

\begin{align}
	u_{m}^{n} &= \left[ De^{-i\alpha n} + Ee^{-i(\alpha+\pi) n} \right]e^{i\mu m\Delta x} \\
	&= De^{i\mu (m\Delta x - \alpha n/ \mu)} + Ee^{-i\pi}e^{i\mu (m\Delta x + \alpha n/ \mu)} \\
	&= (A-E)e^{i\mu (m\Delta x - \alpha n/ \mu)} + (-1)^{n}Ee^{i\mu (m\Delta x + \alpha n/ \mu)}.
\end{align}

The solution of {eq}`eqDiffEquation` can thus be seen to have two waves, whereas the solution to the exact equation {eq}`eqAdvection` had only one wave. The two waves arise from the two roots $B^{\Delta t}_\pm$. By using the initial condition, we were able to find the value of $D$, but the value of $E$ is still to be found.

We can find $E$ by marching the solution \label{eqn:diffEquationSolution2} to the first time step $n=1$. But we can't use (\ref{eqn:leapfrog}), because we don't have the value of $u^{-1}_m$, obviously. Instead, we have to use a two-time level like the forward scheme (Euler), that approximates the time derivative by a first-order forward formula:
\begin{equation*}%\label{eqn:diffEquationSolution2}
	u_{m}^{n+1} = u_{m}^{n} - c\frac{\Delta t}{2\Delta x}(u_{m+1}^{n}-u_{m-1}^{n}).
\end{equation*}
Using the initial condition $u_{m}^{0}=A e^{i \mu m\Delta x}$, we have:
\begin{align*}%\label{eqn:diffEquationSolution2}
	u_{m}^{1} &= Ae^{i \mu m\Delta x} - c\frac{\Delta t}{2\Delta x}A e^{i \mu m\Delta x}(e^{i \mu \Delta x}-e^{-i \mu m\Delta x}) \\
	&= Ae^{i \mu m\Delta x} \left[1 - c\frac{\Delta t}{2\Delta x}(e^{i \mu \Delta x}-e^{-i \mu m\Delta x})\right] \\
	&= A \left[1 - ic\frac{\Delta t}{2\Delta x}\sin \mu\Delta x \right]e^{i \mu m\Delta x}\\
	&= A \left[1 - i\sigma \right]e^{i \mu m\Delta x}\\
	&=A \left[1 - i\sin \alpha \right]e^{i \mu m\Delta x},
\end{align*}
which is solution (\ref{eqn:diffEquationSolution3}) at $n=1$. So now we can solve for $E$:
\begin{align*}%\label{eqn:diffEquationSolution2}
	A \left[1 - i\sin \alpha \right]e^{i \mu m\Delta x} &= (A-E)e^{i\mu (\Delta x - \alpha/ \mu)} - Ee^{i\mu (\Delta x + \alpha/ \mu)}\Leftrightarrow \\
	A \left[1 - i\sin \alpha \right]e^{i \mu m\Delta x} &= e^{i \mu m\Delta x} \left[(A-E)e^{-i\alpha} - Ee^{i\alpha}\right]\Leftrightarrow \\
	A \left[1 - i\sin \alpha \right] &=  (A-E)e^{-i\alpha} - Ee^{i\alpha} \Leftrightarrow \\
	A \left[1 - i\sin \alpha \right] &=  (A-E)e^{-i\alpha} - Ee^{i\alpha} \Leftrightarrow \\
	A - A\cos \alpha &= -E (2\cos \alpha) \Leftrightarrow \\
	E &= \frac{A(\cos \alpha -1)}{2\cos \alpha}.
\end{align*}
Since the difference equation (\ref{eqn:diffEquation}) is of second order (as the centred finite difference formulas are), we needed the solutions at $t^0$ and $t^1$ to determine the unknown constants $D$ and $E$ of (\label{eqn:diffEquationSolution2}). 

We can now write the full solution of the difference equation (\ref{eqn:diffEquation}) as: 
\begin{equation}\label{eqn:diffEquationSolution4} 
	u_{m}^{n} = A \frac{1+\cos \alpha}{2\cos \alpha}e^{i\mu (m\Delta x - \alpha n/ \mu)} + (-1)^{n+1}A \frac{1-\cos \alpha}{2\cos \alpha}e^{i\mu (m\Delta x + \alpha n/ \mu)}.
\end{equation}
This solution is composed of two wave forms of different amplitudes and that travel in opposite senses. When we
compare with the analytical solution of (\label{eqn:linear1dadvection}), that we write below as a discrete version of (\ref{eqn:anaSol}) at $(x_m,t^n)$:
\begin{equation}\label{eqn:anaSol2}
	u(x_m,t^n)=Ae^{i\mu (m\Delta x-cn\Delta t)},
\end{equation}
we see that the first waveform is similar to the true solution, while the second waveform travels in the opposite direction of the true solution. This second waveform is clearly an artifact introduced by the discretization of (\ref{eqn:linear1dadvection}) and is a source of errors.
[^labPolarForm]: The polar form of a complex number $a+ib$ is $(a^2+b^2)^{1/2}\exp (i\arctan \frac{b}{a})$