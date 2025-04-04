
---

# Estimating π Using Monte Carlo Methods

## 1. Motivation

Monte Carlo methods use randomness to estimate values or solve problems, offering a simple yet powerful approach. Estimating π (≈ 3.14159) through geometric probability is a classic example, connecting probability, geometry, and computation. This task explores two methods:
- **Circle-Based Method:** Uses random points in a square and circle.
- **Buffon’s Needle Method:** Uses random needle drops on a lined plane.

These simulations show how randomness can approximate mathematical constants and provide insights into convergence and computational efficiency.

---

## Part 1: Estimating π Using a Circle

### 1.1 Theoretical Foundation
- **Setup:** Consider a square with side length 2 (area = 4) centered at (0, 0), containing a unit circle (radius = 1, area = π).
- **Probability:** A random point in the square has a probability of landing inside the circle equal to the ratio of areas: $\frac{\text{Area of circle}}{\text{Area of square}} = \frac{\pi}{4}$.
- **Formula:** Drop $N$ points, count $M$ inside the circle (distance from (0, 0) ≤ 1), then: $\frac{\pi}{4} \approx \frac{M}{N}$, so $\pi \approx 4 \times \frac{M}{N}$.

### 1.2 Simulation and Visualization
We’ll generate points, count those inside the circle, and plot them.

---

## Part 2: Estimating π Using Buffon’s Needle

### 2.1 Theoretical Foundation
- **Setup:** Parallel lines are spaced $d$ apart on a plane. A needle of length $l$ ($l \leq d$) is dropped randomly.
- **Probability:** The chance of crossing a line is $P = \frac{2l}{\pi d}$ (derived from integrating over all possible angles and positions).
- **Formula:** Drop $N$ needles, count $M$ crossings, then: $P = \frac{2l}{\pi d} \approx \frac{M}{N}$, so $\pi \approx \frac{2l}{d} \times \frac{N}{M}$.

### 2.2 Simulation and Visualization
We’ll simulate needle drops, count crossings, and visualize the setup.

---

## 3. Python Code for Google Colab

This code implements both methods, visualizes the simulations, and analyzes convergence.

```python
# Import libraries (Colab-compatible)
import numpy as np
import matplotlib.pyplot as plt
from google.colab import files

# Set random seed for reproducibility
np.random.seed(42)

# --- Circle-Based Method ---
def circle_method(num_points):
    """Estimate π using points in a square and circle."""
    x = np.random.uniform(-1, 1, num_points)
    y = np.random.uniform(-1, 1, num_points)
    distances = np.sqrt(x**2 + y**2)
    inside_circle = distances <= 1
    num_inside = np.sum(inside_circle)
    pi_estimate = 4 * num_inside / num_points
    return pi_estimate, x, y, inside_circle

# --- Buffon's Needle Method ---
def buffon_method(num_drops, needle_length=1, line_distance=1):
    """Estimate π using needle drops on lined plane."""
    x_center = np.random.uniform(-2, 2, num_drops)  # For visualization
    y_center = np.random.uniform(0, line_distance, num_drops)
    angles = np.random.uniform(0, np.pi, num_drops)
    x1 = x_center - (needle_length / 2) * np.cos(angles)
    x2 = x_center + (needle_length / 2) * np.cos(angles)
    y1 = y_center - (needle_length / 2) * np.sin(angles)
    y2 = y_center + (needle_length / 2) * np.sin(angles)
    crosses = ((y1 <= 0) != (y2 <= 0)) | ((y1 >= line_distance) != (y2 >= line_distance))
    num_crosses = np.sum(crosses)
    pi_estimate = (2 * needle_length * num_drops) / (line_distance * num_crosses) if num_crosses > 0 else float('inf')
    return pi_estimate, x1, x2, y1, y2, crosses

# Test iterations
iterations = [100, 1000, 10000, 100000]
circle_estimates = []
buffon_estimates = []

# Circle Method
for n in iterations:
    pi_est, x, y, inside = circle_method(n)
    circle_estimates.append(pi_est)
    
    # Visualize (only for n=1000 for clarity)
    if n == 1000:
        plt.figure(figsize=(8, 8), dpi=100)
        plt.scatter(x[inside], y[inside], c='blue', s=5, label='Inside Circle')
        plt.scatter(x[~inside], y[~inside], c='red', s=5, label='Outside Circle')
        circle = plt.Circle((0, 0), 1, fill=False, color='black')
        plt.gca().add_patch(circle)
        plt.gca().set_aspect('equal')
        plt.xlabel('x', fontsize=14)
        plt.ylabel('y', fontsize=14)
        plt.title(f'Circle Method (n={n}): π ≈ {pi_est:.5f}', fontsize=16)
        plt.legend(fontsize=12)
        plt.grid(True, linestyle='--', alpha=0.5)
        plt.tight_layout()
        plt.savefig('circle_1000.png', dpi=100, bbox_inches='tight')
        plt.show()

# Buffon's Needle Method
for n in iterations:
    pi_est, x1, x2, y1, y2, crosses = buffon_method(n)
    buffon_estimates.append(pi_est)
    
    # Visualize (only for n=1000 for clarity)
    if n == 1000:
        plt.figure(figsize=(10, 6), dpi=100)
        for i in range(min(n, 100)):  # Show up to 100 needles
            color = 'red' if crosses[i] else 'blue'
            plt.plot([x1[i], x2[i]], [y1[i], y2[i]], color=color, linewidth=1)
        for y in [0, 1]:
            plt.axhline(y, color='black', linestyle='--', alpha=0.5)
        plt.xlim(-2, 2)
        plt.ylim(-0.5, 1.5)
        plt.xlabel('x', fontsize=14)
        plt.ylabel('y', fontsize=14)
        plt.title(f'Buffon\'s Needle (n={n}): π ≈ {pi_est:.5f}', fontsize=16)
        plt.grid(True, linestyle='--', alpha=0.5)
        plt.tight_layout()
        plt.savefig('buffon_1000.png', dpi=100, bbox_inches='tight')
        plt.show()

# Convergence Analysis
plt.figure(figsize=(10, 6), dpi=100)
plt.plot(iterations, circle_estimates, 'o-', label='Circle Method', color='blue')
plt.plot(iterations, buffon_estimates, 's-', label='Buffon\'s Needle', color='red')
plt.axhline(np.pi, color='black', linestyle='--', label='True π')
plt.xscale('log')
plt.xlabel('Number of Iterations (log scale)', fontsize=14)
plt.ylabel('Estimated π', fontsize=14)
plt.title('Convergence of π Estimates', fontsize=16)
plt.legend(fontsize=12)
plt.grid(True, linestyle='--', alpha=0.5)
plt.tight_layout()
plt.savefig('convergence.png', dpi=100, bbox_inches='tight')
plt.show()

# Print results
print("=== Circle Method Estimates ===")
for n, pi_est in zip(iterations, circle_estimates):
    print(f"n={n}: π ≈ {pi_est:.5f}, Error = {abs(pi_est - np.pi):.5f}")

print("\n=== Buffon's Needle Estimates ===")
for n, pi_est in zip(iterations, buffon_estimates):
    print(f"n={n}: π ≈ {pi_est:.5f}, Error = {abs(pi_est - np.pi):.5f}")

# Download plots
files.download('circle_1000.png')
files.download('buffon_1000.png')
files.download('convergence.png')
```

---

## 4. Outputs and Analysis
![alt text](circle_1000.png)
![alt text](buffon_1000.png)
![alt text](convergence.png)

### Part 1: Circle-Based Method
- **Visualization (n=1000):** 
  - Blue points inside the circle, red points outside.
  - Circle boundary shown in black.
  - Example: π ≈ 3.152 (close to 3.14159).
- **Convergence:**
  - n=100: π ≈ 3.00, error ≈ 0.14.
  - n=1000: π ≈ 3.152, error ≈ 0.01.
  - n=10000: π ≈ 3.138, error ≈ 0.004.
  - n=100000: π ≈ 3.141, error ≈ 0.001.
- **Analysis:** Accuracy improves with more points. Convergence is steady, with error decreasing as $\frac{1}{\sqrt{n}}$ (typical for Monte Carlo).

### Part 2: Buffon’s Needle Method
- **Visualization (n=1000):**
  - Blue needles don’t cross lines, red needles do.
  - Lines at y=0 and y=1.
  - Example: π ≈ 3.076 (less accurate than circle method).
- **Convergence:**
  - n=100: π ≈ 2.857, error ≈ 0.285.
  - n=1000: π ≈ 3.076, error ≈ 0.065.
  - n=10000: π ≈ 3.165, error ≈ 0.023.
  - n=100000: π ≈ 3.139, error ≈ 0.003.
- **Analysis:** Slower convergence due to lower probability of crossings (P ≈ 0.637 for $l=d=1$), leading to more variability.

### Convergence Comparison
- **Plot:** Shows π estimates vs. iterations (log scale).
  - Circle method converges faster and is more stable.
  - Buffon’s Needle has higher variance but approaches π with large n.

### Efficiency
- **Circle Method:** Simple distance calculation per point, computationally light.
- **Buffon’s Needle:** Requires trigonometric calculations and line-crossing checks, making it slower.

---

## 5. Discussion

- **Theoretical Foundations:** Both methods rely on geometric probability. The circle method uses area ratios, while Buffon’s Needle uses line-crossing probability.
- **Accuracy:** Circle method is more accurate for fewer iterations due to a higher signal-to-noise ratio.
- **Efficiency:** Circle method is faster and less computationally intensive.
- **Real-World Insight:** Monte Carlo methods like these are used in physics (e.g., particle simulations) and finance (e.g., option pricing), showing how randomness can solve complex problems.

---