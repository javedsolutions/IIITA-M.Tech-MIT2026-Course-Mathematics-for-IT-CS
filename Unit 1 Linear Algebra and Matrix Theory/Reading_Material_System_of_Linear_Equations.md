# System of Linear Equations

**Types, graphical interpretation, solved examples, and Python implementation**

## Learning Objectives

By the end of this material, you should be able to:

- Understand what a system of linear equations is.
- Identify the main types of systems.
- Interpret systems graphically.
- Solve systems algebraically.
- Represent systems using matrices.
- Implement solutions in Python using NumPy and SymPy.
- Graph a system of equations using Matplotlib.

---

## 1. What is a System of Linear Equations?

A **system of linear equations** is a collection of two or more linear equations that contain the same variables.

A **solution** is a set of variable values that satisfies **every equation in the system simultaneously**.

For two variables, a common system is:

$$
a_1x+b_1y=c_1
$$

$$
a_2x+b_2y=c_2
$$

For example:

$$
x+y=3
$$

$$
x-y=-1
$$

The solution is the pair $(x,y)$ that makes both equations true.

---

## 2. Types of Systems

| Type | Graph | Number of Solutions | Meaning |
|---|---|---:|---|
| Consistent, independent | Intersecting lines | One | The equations meet at one point. |
| Inconsistent | Parallel distinct lines | None | The equations never meet. |
| Consistent, dependent | Same/coincident line | Infinitely many | Every point on the common line satisfies both equations. |

### Quick visual interpretation

- **One intersection → one solution**
- **Parallel lines → no solution**
- **Same line → infinitely many solutions**

---

## 3. Type I — Unique Solution

A system has a **unique solution** when the two lines intersect at exactly one point.

### Example

$$
x+y=3
$$

$$
x-y=-1
$$

Add the equations:

$$
2x=2
$$

Therefore:

$$
x=1
$$

Substitute into $x+y=3$:

$$
1+y=3
$$

$$
y=2
$$

### Solution

$$
\boxed{(x,y)=(1,2)}
$$

![Unique solution: intersecting lines](unique_solution.png)

**Figure 1.** Intersecting lines represent a system with exactly one solution.

---

## 4. Type II — No Solution

A system has **no solution** when the two lines are parallel and distinct.

### Example

$$
y=2x+1
$$

$$
y=2x-3
$$

Both equations have slope $2$, but different y-intercepts. Therefore, the lines are parallel and never intersect.

Subtracting the equations leads to the impossible statement:

$$
4=0
$$

### Solution

$$
\boxed{\varnothing}
$$

![No solution: parallel lines](no_solution.png)

**Figure 2.** Parallel distinct lines represent an inconsistent system.

---

## 5. Type III — Infinitely Many Solutions

A system has **infinitely many solutions** when both equations describe the same line.

### Example

$$
y=2x+1
$$

$$
2y=4x+2
$$

Divide the second equation by 2:

$$
y=2x+1
$$

Both equations are identical.

### Solution

$$
\boxed{\{(x,y):y=2x+1\}}
$$

![Infinitely many solutions: coincident lines](infinitely_many_solutions.png)

**Figure 3.** Coincident lines represent infinitely many solutions.

---

## 6. Matrix Form

A system can be written as:

$$
A\mathbf{x}=\mathbf{b}
$$

For a two-variable system:

$$
\begin{bmatrix}
a_1 & b_1\\
a_2 & b_2
\end{bmatrix}
\begin{bmatrix}
x\\
y
\end{bmatrix}
=
\begin{bmatrix}
c_1\\
c_2
\end{bmatrix}
$$

Here:

- $A$ is the coefficient matrix.
- $\mathbf{x}$ is the variable vector.
- $\mathbf{b}$ is the constant vector.

For a square system, if $\det(A)\neq0$, the system has a unique solution.

---

## 7. Main Methods of Solving

### Graphical Method

Plot each equation as a line. The intersection point(s) give the solution.

### Substitution

Solve one equation for one variable and substitute into the other equation.

### Elimination

Add or subtract equations to eliminate one variable, then solve for the remaining variable.

### Matrix/Gaussian Elimination

Use row operations on an augmented matrix to reduce the system.

### Inverse Matrix

When $A$ is square and invertible:

$$
\mathbf{x}=A^{-1}\mathbf{b}
$$

---

## 8. Worked Example Using Elimination

Solve:

$$
2x+3y=13
$$

$$
x-y=1
$$

Multiply the second equation by 3:

$$
3x-3y=3
$$

Add the equations:

$$
5x=16
$$

Therefore:

$$
x=\frac{16}{5}
$$

Substitute into $x-y=1$:

$$
\frac{16}{5}-y=1
$$

$$
y=\frac{11}{5}
$$

### Solution

$$
\boxed{(x,y)=\left(\frac{16}{5},\frac{11}{5}\right)}
$$

---

## 9. Identifying the Type Algebraically

For a system $A\mathbf{x}=\mathbf{b}$, compare the rank of the coefficient matrix $A$ with the rank of the augmented matrix $[A|\mathbf{b}]$.

| Condition | Type | Number of Solutions |
|---|---|---:|
| $\operatorname{rank}(A)=\operatorname{rank}([A|b])=$ number of variables | Consistent, independent | One |
| $\operatorname{rank}(A)=\operatorname{rank}([A|b])<$ number of variables | Consistent, dependent | Infinitely many |
| $\operatorname{rank}(A)<\operatorname{rank}([A|b])$ | Inconsistent | None |

---

# 10. Python Implementation

Python provides several useful libraries:

- **NumPy** — numerical solutions.
- **SymPy** — symbolic and exact solutions.
- **Matplotlib** — graphical visualization.

## 10.1 Solve a System Using NumPy

Consider:

$$
2x+3y=13
$$

$$
x-y=1
$$

```python
import numpy as np

A = np.array([
    [2, 3],
    [1, -1]
], dtype=float)

b = np.array([13, 1], dtype=float)

det_A = np.linalg.det(A)

if not np.isclose(det_A, 0):
    solution = np.linalg.solve(A, b)

    print("Unique solution:")
    print("x =", solution[0])
    print("y =", solution[1])
else:
    print("The coefficient matrix is singular.")
    print("The system may have no solution or infinitely many solutions.")
```

Expected output:

```text
Unique solution:
x = 3.2
y = 2.2
```

Thus:

$$
\boxed{x=3.2,\quad y=2.2}
$$

or exactly:

$$
\boxed{x=\frac{16}{5},\quad y=\frac{11}{5}}
$$

---

## 10.2 Exact Solution Using SymPy

```python
import sympy as sp

x, y = sp.symbols("x y")

eq1 = sp.Eq(2*x + 3*y, 13)
eq2 = sp.Eq(x - y, 1)

solution = sp.solve((eq1, eq2), (x, y))

print(solution)
```

Output:

```text
{x: 16/5, y: 11/5}
```

---

# 11. General Python Function

```python
import numpy as np

def solve_linear_system(A, b):
    A = np.asarray(A, dtype=float)
    b = np.asarray(b, dtype=float)

    if A.shape[0] != A.shape[1]:
        raise ValueError("A must be a square matrix.")

    if b.shape[0] != A.shape[0]:
        raise ValueError("b must have the same number of rows as A.")

    if np.isclose(np.linalg.det(A), 0):
        return "No unique solution: system may have no or infinitely many solutions."

    return np.linalg.solve(A, b)


A = [
    [1, 1],
    [1, -1]
]

b = [3, -1]

print(solve_linear_system(A, b))
```

Output:

```text
[1. 2.]
```

Therefore:

$$
\boxed{x=1,\quad y=2}
$$

---

# 12. Python Code for Graphing a System

The following example graphs:

$$
x+y=3
$$

and

$$
x-y=-1
$$

Their solution is $(1,2)$.

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.linspace(-5, 5, 400)

# x + y = 3  ->  y = 3 - x
y1 = 3 - x

# x - y = -1  ->  y = x + 1
y2 = x + 1

plt.plot(x, y1, label="x + y = 3")
plt.plot(x, y2, label="x - y = -1")

plt.scatter(1, 2, s=60, label="Solution (1, 2)")

plt.axhline(0, linewidth=0.8)
plt.axvline(0, linewidth=0.8)

plt.xlabel("x")
plt.ylabel("y")
plt.title("Graphical Solution of a Linear System")

plt.grid(True, alpha=0.25)
plt.legend()
plt.show()
```

The graph shows two lines intersecting at:

$$
\boxed{(1,2)}
$$

---

# 13. Important Difference: Singular Systems

When:

$$
\det(A)=0
$$

the coefficient matrix is **singular**.

A singular system does **not automatically mean no solution**.

It can have either:

1. **No solution**, or
2. **Infinitely many solutions**.

For example:

### No solution

$$
y=2x+1
$$

$$
y=2x-3
$$

The lines are parallel.

### Infinitely many solutions

$$
y=2x+1
$$

$$
2y=4x+2
$$

The equations represent the same line.

Therefore, additional checks such as matrix rank or row reduction are needed.

---

# 14. Practice Problems

### Problem 1

Solve:

$$
x+y=7
$$

$$
x-y=1
$$

### Problem 2

Determine the type and solve:

$$
2x+4y=8
$$

$$
x+2y=4
$$

### Problem 3

Determine the type:

$$
y=3x+2
$$

$$
y=3x-5
$$

### Problem 4

Solve using Python:

$$
3x+2y=12
$$

$$
x-y=1
$$

### Problem 5

Explain geometrically why coincident lines have infinitely many solutions.

---

# 15. Key Takeaways

- A system of linear equations asks for values that satisfy all equations simultaneously.
- Two lines can intersect once, never intersect, or coincide.
- **One intersection → one solution.**
- **Parallel lines → no solution.**
- **Same line → infinitely many solutions.**
- Substitution and elimination are useful hand-solving techniques.
- Matrix representation provides a compact way to work with systems.
- `numpy.linalg.solve()` is useful for systems with a unique numerical solution.
- SymPy can provide exact symbolic answers.
- A singular coefficient matrix requires additional analysis to distinguish no solution from infinitely many solutions.
- Graphs provide an intuitive geometric interpretation of the solution.

---

## Summary Table

| Graphical Situation | Algebraic Description | Solution |
|---|---|---|
| Lines intersect once | Independent equations | One solution |
| Lines are parallel | Inconsistent equations | No solution |
| Lines coincide | Dependent equations | Infinitely many solutions |

---

**End of Reading Material**
