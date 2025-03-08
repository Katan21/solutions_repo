# Problem 2

## 1. Theoretical Foundation

The forced damped pendulum is governed by a second-order nonlinear differential equation that incorporates gravity (restoring force), damping, and an external periodic force. Let’s derive it step-by-step.

### Governing Equation

Consider a pendulum of length $l$ and mass $m$, with angle $\theta$ from the vertical:
- **Restoring force:** Gravitational torque, $-\frac{mg}{l} \sin\theta$.
- **Damping:** Proportional to angular velocity, $-b \dot{\theta}$ (where $b$ is the damping coefficient).
- **External force:** A periodic driving torque, $F_0 \cos(\omega t)$, where $F_0$ is the amplitude and $\omega$ is the driving frequency.

The equation of motion, from Newton’s second law for rotation ($I \ddot{\theta} = \sum \tau$), is:
$$
ml^2 \ddot{\theta} + b \dot{\theta} + mg \sin\theta = F_0 \cos(\omega t)
$$
Divide through by $ml^2$ and define:
- $\omega_0 = \sqrt{\frac{g}{l}}$ (natural frequency),
- $\gamma = \frac{b}{ml^2}$ (damping rate),
- $f = \frac{F_0}{ml^2}$ (driving amplitude per unit inertia).

The standard form becomes:
$$
\ddot{\theta} + \gamma \dot{\theta} + \omega_0^2 \sin\theta = f \cos(\omega t)
$$

### Small-Angle Approximation

For small $\theta$, $\sin\theta \approx \theta$, simplifying the equation to a linear forced damped oscillator:
$$
\ddot{\theta} + \gamma \dot{\theta} + \omega_0^2 \theta = f \cos(\omega t)
$$
This is solvable analytically:
- **Homogeneous solution:** $\theta_h(t) = e^{-\frac{\gamma}{2} t} [A \cos(\omega_d t) + B \sin(\omega_d t)]$, where $\omega_d = \sqrt{\omega_0^2 - (\frac{\gamma}{2})^2}$ (damped frequency).
- **Particular solution:** $\theta_p(t) = C \cos(\omega t - \phi)$, with amplitude $C = \frac{f}{\sqrt{(\omega_0^2 - \omega^2)^2 + (\gamma \omega)^2}}$ and phase $\phi = \tan^{-1}\left(\frac{\gamma \omega}{\omega_0^2 - \omega^2}\right)$.

### Resonance

Resonance occurs when $\omega \approx \omega_0$, maximizing $C$. For light damping ($\gamma$ small), the amplitude peaks sharply, amplifying the pendulum’s response.

## 2. Analysis of Dynamics

### Parameter Effects

- **Damping ($\gamma$)**: High $\gamma$ suppresses oscillations; low $\gamma$ allows sustained or chaotic motion.
- **Driving Amplitude ($f$)**: Small $f$ yields regular oscillations; large $f$ can push the system into chaos.
- **Driving Frequency ($\omega$)**: Near $\omega_0$, resonance occurs; far from $\omega_0$, motion may become quasiperiodic or chaotic.

### Transition to Chaos

The nonlinear term $\sin\theta$ (absent in the small-angle case) introduces complexity:
- **Periodic Motion:** At low $f$, the pendulum locks to the driving frequency.
- **Chaos:** High $f$ or specific $\omega$ values lead to unpredictable, aperiodic motion, sensitive to initial conditions.

## 3. Practical Applications

- **Energy Harvesting:** Oscillating systems (e.g., piezoelectric devices) convert motion to electricity, optimized near resonance.
- **Suspension Bridges:** External forces (wind) can drive oscillations, requiring damping to prevent collapse (e.g., Tacoma Narrows).
- **Circuits:** Driven RLC circuits mirror this behavior, used in signal processing.

## 4. Implementation

Let’s simulate this using Python with the Runge-Kutta method (RK4) for the nonlinear equation, visualizing motion, phase portraits, and Poincaré sections.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.integrate import odeint

# Define the system
def pendulum_deriv(state, t, gamma, omega0, f, omega):
    theta, theta_dot = state
    dtheta_dt = theta_dot
    dtheta_dot_dt = -gamma * theta_dot - omega0**2 * np.sin(theta) + f * np.cos(omega * t)
    return [dtheta_dt, dtheta_dot_dt]

# Parameters
g = 9.81  # m/s^2
l = 1.0   # m
omega0 = np.sqrt(g / l)
gamma = 0.5  # damping coefficient
f = 1.2      # driving amplitude
omega = 2/3 * omega0  # driving frequency

# Time array
t = np.linspace(0, 50, 1000)

# Initial conditions
theta0 = 0.1  # radians
theta_dot0 = 0.0
state0 = [theta0, theta_dot0]

# Solve ODE
sol = odeint(pendulum_deriv, state0, t, args=(gamma, omega0, f, omega))
theta, theta_dot = sol[:, 0], sol[:, 1]

# Plots
plt.figure(figsize=(12, 8))

# Time series
plt.subplot(2, 2, 1)
plt.plot(t, theta, 'b')
plt.xlabel('Time (s)')
plt.ylabel('θ (rad)')
plt.title('Pendulum Motion')

# Phase portrait
plt.subplot(2, 2, 2)
plt.plot(theta, theta_dot, 'r')
plt.xlabel('θ (rad)')
plt.ylabel('dθ/dt (rad/s)')
plt.title('Phase Portrait')

# Poincaré section (at t = 2π/ω multiples)
poincare_t = t[::int(2 * np.pi / (omega * (t[1] - t[0])))]
poincare_theta = []
poincare_theta_dot = []
for ti in poincare_t:
    idx = np.argmin(np.abs(t - ti))
    poincare_theta.append(theta[idx])
    poincare_theta_dot.append(theta_dot[idx])
plt.subplot(2, 2, 3)
plt.scatter(poincare_theta, poincare_theta_dot, s=5, c='g')
plt.xlabel('θ (rad)')
plt.ylabel('dθ/dt (rad/s)')
plt.title('Poincaré Section')

plt.tight_layout()
plt.show()

# Vary parameters for resonance and chaos
f_values = [0.5, 1.2, 1.5]  # Explore different amplitudes
plt.figure(figsize=(12, 4))
for i, f in enumerate(f_values):
    sol = odeint(pendulum_deriv, state0, t, args=(gamma, omega0, f, omega))
    plt.subplot(1, 3, i+1)
    plt.plot(t, sol[:, 0])
    plt.title(f'f = {f}')
    plt.xlabel('Time (s)')
    plt.ylabel('θ (rad)')
plt.tight_layout()
plt.show()
```

### Output Explanation

- **Time Series:** Shows $\theta(t)$—regular for small $f$, chaotic for large $f$.
- **Phase Portrait:** A closed loop indicates periodic motion; scattered points suggest chaos.
- **Poincaré Section:** Discrete points for periodic motion; a cloud for chaos.
- **Parameter Variation:** Low $f$ (0.5) gives damped oscillations, higher $f$ (1.5) shows chaotic behavior.

## Deliverables

- **Solutions:** Linear case has damped + driven terms; nonlinear requires numerical methods.
- **Graphics:** Time series, phase portraits, and Poincaré sections illustrate dynamics.
- **Limitations:** Assumes constant $\gamma$, periodic forcing, and no friction irregularities.
- **Extensions:** Add nonlinear damping ($\gamma |\dot{\theta}| \dot{\theta}$) or stochastic forcing.

## Discussion

The forced damped pendulum bridges simple oscillators and complex systems. Resonance amplifies energy transfer, while chaos reveals sensitivity to conditions—key for engineering and physics. For deeper analysis, bifurcation diagrams (varying $f$ or $\omega$) could map transitions to chaos.
