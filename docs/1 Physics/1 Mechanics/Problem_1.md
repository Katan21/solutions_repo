# Problem 1


# Investigating the Range as a Function of the Angle of Projection

## 1. Theoretical Foundation

### Deriving the Equations of Motion

Projectile motion is a classic problem in physics governed by Newton’s laws under constant gravitational acceleration, assuming no air resistance for simplicity. Let’s start with the fundamentals.

Consider a projectile launched from the origin $(x_0, y_0) = (0, 0)$ with an initial velocity $v_0$ at an angle $\theta$ above the horizontal. The acceleration is solely due to gravity, acting downward with magnitude $g$ (typically $9.8 \, \text{m/s}^2$ on Earth). We can break this into components:

- **Horizontal motion**: No acceleration ($a_x = 0$).
- **Vertical motion**: Acceleration $a_y = -g$.

The initial velocity components are:

- $$v_{0x} = v_0 \cos\theta$$
- $$v_{0y} = v_0 \sin\theta$$

#### Horizontal Motion

The differential equation is:

$$
\frac{d^2 x}{dt^2} = 0
$$

Integrating once:

$$
\frac{dx}{dt} = v_{0x} = v_0 \cos\theta
$$

Integrating again with initial condition \(x(0) = 0\):

$$
x(t) = v_0 \cos\theta \cdot t
$$

#### Vertical Motion

The differential equation is:

$$
\frac{d^2 y}{dt^2} = -g
$$

Integrating once:

$$
\frac{dy}{dt} = v_{0y} - g t = v_0 \sin\theta - g t
$$

Integrating again with $$y(0) = 0$$:

$$
y(t) = v_0 \sin\theta \cdot t - \frac{1}{2} g t^2
$$

These equations describe the projectile’s position parametrically. The trajectory is a parabola, as $$y(t)$$ is quadratic in $$t$$, while $$x(t)$$ is linear.

### Family of Solutions
The solutions depend on free parameters:
- $$v_0$$: Initial velocity
- $$\theta$$: Angle of projection
- $$g$$: Gravitational acceleration
- Initial height $$h$$ (if $$y_0 \neq 0$$)

Varying these parameters generates a family of trajectories, from flat, fast arcs (low $$\theta$$, high $$v_0$$) to steep, short parabolas (high $$\theta$$, low $$v_0$$).

## 2. Analysis of the Range

### Deriving the Range
The range $$R$$ is the horizontal distance traveled when the projectile returns to $$y = 0$$. Set $$y(t) = 0$$:

$$
v_0 \sin\theta \cdot t - \frac{1}{2} g t^2 = 0
$$

Factorize:

$$
t \left( v_0 \sin\theta - \frac{1}{2} g t \right) = 0
$$

Solutions are $$t = 0$$ (launch) and:

$$
t = \frac{2 v_0 \sin\theta}{g}
$$

This is the time of flight. Substitute into $$x(t)$$:

$$
R = x\left(\frac{2 v_0 \sin\theta}{g}\right) = v_0 \cos\theta \cdot \frac{2 v_0 \sin\theta}{g} = \frac{2 v_0^2 \sin\theta \cos\theta}{g}
$$

Using the identity $$2 \sin\theta \cos\theta = \sin 2\theta$$:

$$
R = \frac{v_0^2 \sin 2\theta}{g}
$$

### Dependence on Angle
- **Maximum Range**: $$R$$ is maximized when $$\sin 2\theta = 1$$, i.e., $$2\theta = 90^\circ$$, so $$\theta = 45^\circ$$. Then:
  
$$
  R_{\text{max}} = \frac{v_0^2}{g}
$$

- **Symmetry**: $$R(\theta) = R(90^\circ - \theta)$$, e.g., ranges at $$30^\circ$$ and $$60^\circ$$ are equal, due to $$\sin(180^\circ - 2\theta) = \sin 2\theta$$.
- **Limits**: At $$\theta = 0^\circ$$ or $$90^\circ$$, $$\sin 2\theta = 0$$, so $$R = 0$$.

### Other Parameters
- **Initial Velocity ($$v_0$$)**: $$R \propto v_0^2$$, a quadratic relationship. Doubling $$v_0$$ quadruples the range.
- **Gravity ($$g$$)**: $$R \propto 1/g$$. On a planet with lower $$g$$ (e.g., the Moon), the range increases.

## 3. Practical Applications

- **Sports**: In soccer or golf, athletes adjust $$\theta$$ and $$v_0$$ for desired distance, though air resistance and spin complicate the ideal $$45^\circ$$.
- **Engineering**: Artillery and rocket launches optimize $$\theta$$ based on target distance and terrain.
- **Uneven Terrain**: If launched from height $$h$$, the range equation becomes:

$$
  R = \frac{v_0^2}{g} \left( \sin 2\theta + \frac{2 h g}{v_0^2} \cos^2\theta \right)^{1/2}
$$

- **Air Resistance**: Introduces a drag force proportional to velocity squared, reducing range and shifting the optimal angle below $$45^\circ$$.

## 4. Implementation

Here’s a Python script to simulate and visualize the range versus angle:

```python
import numpy as np
import matplotlib.pyplot as plt

# Parameters
g = 9.8  # m/s^2
v0_values = [10, 20, 30]  # m/s
theta_deg = np.linspace(0, 90, 91)  # degrees
theta_rad = np.radians(theta_deg)

# Range function
def range_theta(v0, theta_rad, g):
    return (v0**2 * np.sin(2 * theta_rad)) / g

# Compute ranges for different v0
ranges = {v0: range_theta(v0, theta_rad, g) for v0 in v0_values}

# Plotting
plt.figure(figsize=(10, 6))
for v0, R in ranges.items():
    plt.plot(theta_deg, R, label=f'v0 = {v0} m/s')
plt.xlabel('Angle of Projection (degrees)')
plt.ylabel('Range (m)')
plt.title('Range vs. Angle of Projection')
plt.legend()
plt.grid(True)
plt.show()

# Find maximum range and optimal angle
for v0 in v0_values:
    R = range_theta(v0, theta_rad, g)
    max_R = np.max(R)
    opt_theta = theta_deg[np.argmax(R)]
    print(f"v0 = {v0} m/s: Max Range = {max_R:.2f} m at {opt_theta}°")
```

### Output Explanation
- The script plots $$R$$ versus $$\theta$$ for $$v_0 = 10, 20, 30 \, \text{m/s}$$.
- Peaks occur at $$45^\circ$$, with $$R_{\text{max}} = v_0^2 / g$$.
- Higher $$v_0$$ shifts the curve upward quadratically.

## Discussion

### Limitations
- **Idealization**: Assumes no air resistance, flat terrain, and constant $$g$$.
- **Realism**: Drag reduces range and optimal $$\theta$$; wind adds lateral deviation.

### Extensions
- **Drag**: Model with $$F_d = -k v^2$$, solved numerically (e.g., Runge-Kutta).
- **Terrain**: Adjust $$y(t) = 0$$ to $$y(t) = f(x)$$.
- **3D Motion**: Include crosswinds or spin (Magnus effect).

This model, while simple, bridges theory and application, from classroom physics to real-world engineering challenges.
