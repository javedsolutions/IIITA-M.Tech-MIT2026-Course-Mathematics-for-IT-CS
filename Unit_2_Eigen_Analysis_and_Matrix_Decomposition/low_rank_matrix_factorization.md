# :classical_building: Mathematics for IT Course - M.Tech. 1st Semester, IIIT Allahabad
## Unit 2: Eigen Analysis and Matrix Decomposition
* ### Current Topic: Low-Rank Approximation and Matrix Factorization Concepts
* #### Low-rank approximation, matrix factorization, SVD, QR, LU, Cholesky, CUR, and Python implementation
---
## 👥 Instructor Information
* **Edited by Instructor:** [Dr. Mohammed Javed](https://sites.google.com/site/mohammedjaved2016/)
* **Email:** javed@iiita.ac.in
* **Teaching Assistants:** Mr. Apurba Chakraborty (rsi2024005@iiita.ac.in)
---
## 🎯 1. Learning Objectives

After completing this reading material, you should be able to:

- explain the concept of matrix rank;
- identify rank-1 and low-rank matrices;
- represent a rank-1 matrix using an outer product;
- explain why low-rank approximation is useful;
- construct low-rank approximations using SVD;
- understand the Eckart–Young theorem;
- implement truncated SVD in Python;
- understand LU, QR, Cholesky, eigendecomposition, and SVD;
- compare different matrix factorizations;
- use matrix factorization for compression, denoising, dimensionality reduction, and recommendation systems;
- implement practical matrix factorization algorithms with NumPy.

---

## 2. Introduction

A matrix can contain a large number of entries while still having relatively simple underlying structure.

For example,

$$
A=
\begin{bmatrix}
1&2&3\\
2&4&6\\
3&6&9
\end{bmatrix}.
$$

Although $(A)$ contains nine entries, every row is a scalar multiple of the first row. Therefore,

$$
\text{rank}(A)=1.
$$

![Rank-1 matrix](figures/01_rank1_matrix.png)

This motivates an important idea:

> **Can we represent a large matrix using fewer dimensions while preserving its most important information?**

The answer is often **yes**, using a **low-rank approximation**.

Low-rank representations are fundamental in:

- data compression;
- image compression;
- machine learning;
- recommendation systems;
- dimensionality reduction;
- signal processing;
- computer vision;
- numerical linear algebra.

---

# Part I — Matrix Rank and Low-Rank Structure

## 3. What is Matrix Rank?

The **rank** of a matrix is the dimension of its column space, equivalently the dimension of its row space.

For

$$
A\in\mathbb{R}^{m\times n},
$$

we have

$$
\text{rank}(A)\leq\min(m,n).
$$

For example,

$$
A=
\begin{bmatrix}
1&2\\
2&4
\end{bmatrix}
$$

has rank 1 because the second row is twice the first.

---

## 4. Rank using Python

NumPy provides `np.linalg.matrix_rank`.

```python
import numpy as np

A = np.array([
    [1, 2],
    [2, 4]
], dtype=float)

rank = np.linalg.matrix_rank(A)

print("Rank:", rank)
```

Output:

```text
Rank: 1
```

For numerical data, rank is determined using a tolerance because floating-point computations rarely produce exact zeros.

---

## 5. Full-Rank and Low-Rank Matrices

Suppose

$$
A\in\mathbb{R}^{m\times n}.
$$

The maximum possible rank is

$$
\min(m,n).
$$

A matrix is **full rank** when it reaches this maximum.

A matrix is considered **low rank** when

$$
r=\text{rank}(A)
$$

is much smaller than $(\min(m,n))$.

For example, a $(1000\times1000)$ matrix with rank 5 has a very strong low-dimensional structure.

---

## 6. Rank-1 Matrices

Every rank-1 matrix can be written as an outer product:

$$
\boxed{A=\mathbf{u}\mathbf{v}^T}
$$

where

$$
\mathbf{u}\in\mathbb{R}^{m},
\qquad
\mathbf{v}\in\mathbb{R}^{n}.
$$

For example,

$$
\mathbf u=
\begin{bmatrix}
1\\2\\3
\end{bmatrix},
\qquad
\mathbf v=
\begin{bmatrix}
2\\1\\4
\end{bmatrix}.
$$

Then

$$
\mathbf u\mathbf v^T=\begin{bmatrix}1\\2\\3\end{bmatrix}\begin{bmatrix}2&1&4\end{bmatrix}=
\begin{bmatrix}
2&1&4\\
4&2&8\\
6&3&12
\end{bmatrix}.
$$

![Outer product](figures/02_outer_product.png)

---

## 7. Outer Product in Python

```python
import numpy as np

u = np.array([1, 2, 3])
v = np.array([2, 1, 4])

A = np.outer(u, v)

print(A)
print("Rank:", np.linalg.matrix_rank(A))
```

The result has rank 1.

An equivalent implementation is:

```python
A = u.reshape(-1, 1) @ v.reshape(1, -1)
```

---

## 8. Rank- $(r)$ Matrix as a Sum of Rank-1 Matrices

A rank- $(r)$ matrix can be represented as

$$
\boxed{
A=
\sum_{i=1}^{r}
\mathbf u_i\mathbf v_i^T
}
$$

with suitable scaling factors.

More generally,

$$
A=
\sum_{i=1}^{r}
\sigma_i\mathbf u_i\mathbf v_i^T.
$$

This expression is particularly important because it leads directly to the **Singular Value Decomposition**.

---

# Part II — What is Low-Rank Approximation?

## 9. Definition

Given a matrix

$$
A\in\mathbb{R}^{m\times n},
$$

a matrix $(A_k)$ is a **rank-$(k)$ approximation** if

$$
\rank{rank}(A_k)\leq k.
$$

We want $(A_k)$ to be close to $(A)$:

$$
\boxed{
A_k\approx A.
}
$$

The approximation error can be measured using a matrix norm such as

$$
\|A-A_k\|_F
$$

or

$$
\|A-A_k\|_2.
$$

---

## 10. Why Approximate a Matrix?

A matrix might contain:

- redundant information;
- noise;
- highly correlated features;
- repeated patterns;
- dimensions that contribute very little information.

Low-rank approximation attempts to preserve the dominant structure while discarding less important components.

This can lead to:

- lower memory requirements;
- faster computation;
- noise reduction;
- dimensionality reduction;
- easier visualization;
- interpretable latent features.

---

# Part III — Singular Value Decomposition and Low-Rank Approximation

## 11. SVD as a Matrix Factorization

For

$$
A\in\mathbb{R}^{m\times n},
$$

the Singular Value Decomposition is

$$
\boxed{
A=U\Sigma V^T.
}
$$

Here:

- $(U)$ contains left singular vectors;
- $(V)$ contains right singular vectors;
- $(\Sigma)$ contains singular values.

The singular values satisfy

$$
\sigma_1\geq\sigma_2\geq\cdots\geq0.
$$

![Factorization pipeline](figures/03_factorization_pipeline.png)

---

## 12. SVD as a Sum of Rank-1 Matrices

SVD can be written as

$$
\boxed{
A=
\sigma_1\mathbf u_1\mathbf v_1^T+
\sigma_2\mathbf u_2\mathbf v_2^T+
\cdots+
\sigma_r\mathbf u_r\mathbf v_r^T.
}
$$

Each term

$$
\sigma_i\mathbf u_i\mathbf v_i^T
$$

has rank at most 1.

Thus, a matrix is decomposed into a sum of rank-1 matrices.

This gives us a natural way to approximate $(A)$.

---

## 13. Rank- $(k)$ Approximation from SVD

Keep only the first $(k)$ singular values:

$$
\boxed{
A_k=
\sum_{i=1}^{k}
\sigma_i\mathbf u_i\mathbf v_i^T
}
$$

or equivalently,

$$
\boxed{
A_k=U_k\Sigma_kV_k^T.
}
$$

This is called the **truncated SVD**.

![Truncated SVD](figures/04_truncated_svd.png)

---

## 14. Python Implementation of Truncated SVD

```python
import numpy as np

def low_rank_approximation(A, k):
    U, S, Vt = np.linalg.svd(A, full_matrices=False)

    A_k = (U[:, :k] * S[:k]) @ Vt[:k, :]

    return A_k

A = np.random.randn(10, 8)

A_2 = low_rank_approximation(A, 2)

print("Original shape:", A.shape)
print("Approximation shape:", A_2.shape)
print("Approximation rank:", np.linalg.matrix_rank(A_2))
```

---

## 15. Why Singular Values Matter

The singular values tell us how much of the matrix's structure is associated with each singular direction.

Suppose

$$
\sigma_1\gg\sigma_2\gg\sigma_3.
$$

Then the first singular direction is much more important than the later ones.

![Singular values](figures/06_singular_values.png)

A rapidly decreasing singular-value spectrum is a strong indication that low-rank approximation may work well.

---

## 16. Approximation Error

For the rank-$(k)$ approximation

$$
A_k=U_k\Sigma_kV_k^T,
$$

the error decreases as $(k)$ increases.

![Error versus rank](figures/05_error_vs_rank.png)

Using the Frobenius norm,

$$
\boxed{
\|A-A_k\|_F=
\sqrt{\sum_{i=k+1}^{r}\sigma_i^2}.
}
$$

Using the spectral norm,

$$
\boxed{
\|A-A_k\|_2=\sigma_{k+1}.
}
$$

---

## 17. Eckart–Young Theorem

The **Eckart–Young theorem** is one of the most important results in low-rank approximation.

It states that the truncated SVD

$$
A_k=U_k\Sigma_kV_k^T
$$

is the best rank-$(k)$ approximation to $(A)$ under both the spectral norm and Frobenius norm.

In Frobenius norm,

$$
\boxed{
A_k=
\arg\min_{\text{rank}(B)\leq k}
\|A-B\|_F.
}
$$

Thus, among all matrices with rank at most $(k)$, the truncated SVD gives the smallest approximation error.

---

## 18. Selecting the Rank $(k)$

A common strategy is to retain a specified percentage of the total squared singular values.

The total energy is

$$
E=\sum_i\sigma_i^2.
$$

The cumulative energy through $(k)$ singular values is

$$
E_k=\sum_{i=1}^{k}\sigma_i^2.
$$

The explained-energy ratio is

$$
\boxed{
\frac{E_k}{E}.
}
$$

### Python

```python
import numpy as np

A = np.random.randn(100, 80)

U, S, Vt = np.linalg.svd(A, full_matrices=False)

energy = S**2
cumulative_energy = np.cumsum(energy) / np.sum(energy)

threshold = 0.90

k = np.argmax(cumulative_energy >= threshold) + 1

print("Rank required for 90% energy:", k)
print("Captured energy:", cumulative_energy[k-1])
```

---

# Part IV — Matrix Factorization Concepts

## 19. What is Matrix Factorization?

Matrix factorization expresses a matrix as a product of simpler matrices.

General form:

$$
\boxed{
A=BC
}
$$

or

$$
\boxed{
A=BCD.
}
$$

Factorization can expose structure that is not obvious from the original matrix.

Different factorizations are useful for different tasks.

Important examples include:

1. LU factorization;
2. QR factorization;
3. Cholesky factorization;
4. eigendecomposition;
5. Singular Value Decomposition;
6. nonnegative matrix factorization;
7. CUR decomposition.

---

# 20. LU Factorization

LU factorization writes

$$
\boxed{
A=LU
}
$$

where:

- $(L)$ is lower triangular;
- $(U)$ is upper triangular.

With pivoting, the usual form is

$$
\boxed{
PA=LU.
}
$$

LU factorization is useful for solving systems

$$
A\mathbf{x}=\mathbf b.
$$

---

## 21. LU Factorization Example

Consider

$$
A=
\begin{bmatrix}
4&3\\
6&3
\end{bmatrix}.
$$

A possible factorization is

$$
L=
\begin{bmatrix}
1&0\\
1.5&1
\end{bmatrix},
\qquad
U=
\begin{bmatrix}
4&3\\
0&-1.5
\end{bmatrix}.
$$

Then

$$
LU=A.
$$

### Python with SciPy

```python
import numpy as np
from scipy.linalg import lu

A = np.array([
    [4, 3],
    [6, 3]
], dtype=float)

P, L, U = lu(A)

print("P =")
print(P)

print("L =")
print(L)

print("U =")
print(U)

print("Reconstruction:")
print(P @ L @ U)
```

Install SciPy if necessary:

```bash
pip install scipy
```

---

# 22. QR Factorization

QR factorization expresses a matrix as

$$
\boxed{
A=QR
}
$$

where:

- $(Q)$ has orthonormal columns;
- $(R)$ is upper triangular.

The orthogonality property is

$$
\boxed{
Q^TQ=I.
}
$$

![QR orthogonal directions](figures/09_qr_orthogonal_directions.png)

QR factorization is particularly important for:

- least squares;
- orthogonalization;
- numerical eigenvalue algorithms;
- solving linear systems.

---

## 23. QR Factorization in Python

```python
import numpy as np

A = np.array([
    [1, 1],
    [1, 2],
    [1, 3]
], dtype=float)

Q, R = np.linalg.qr(A)

print("Q =")
print(Q)

print("\nR =")
print(R)

print("\nReconstruction:")
print(Q @ R)

print("\nOrthogonality:")
print(Q.T @ Q)
```

---

# 24. Cholesky Factorization

For a symmetric positive-definite matrix,

$$
A=A^T
$$

and

$$
\mathbf{x}^TA\mathbf{x}>0
$$

for every nonzero $(\mathbf{x})$.

Cholesky factorization expresses $(A)$ as

$$
\boxed{
A=LL^T
}
$$

where $(L)$ is lower triangular.

![Cholesky factorization](figures/10_cholesky_factorization.png)

---

## 25. Cholesky in Python

```python
import numpy as np

A = np.array([
    [4, 2],
    [2, 3]
], dtype=float)

L = np.linalg.cholesky(A)

print("L =")
print(L)

print("\nReconstruction:")
print(L @ L.T)
```

Cholesky is computationally efficient and is widely used in:

- optimization;
- statistics;
- covariance computations;
- Gaussian models;
- numerical linear algebra.

---

# 26. Eigendecomposition

For a square matrix $(A)$, eigendecomposition has the form

$$
\boxed{
A=V\Lambda V^{-1}
}
$$

when the matrix is diagonalizable.

Here,

$$
\Lambda=
\begin{bmatrix}
\lambda_1&0&\cdots\\
0&\lambda_2&\cdots\\
\vdots&\vdots&\ddots
\end{bmatrix}.
$$

The eigenvectors satisfy

$$
A\mathbf v_i=\lambda_i\mathbf v_i.
$$

### Python

```python
import numpy as np

A = np.array([
    [2, 1],
    [1, 2]
], dtype=float)

eigenvalues, eigenvectors = np.linalg.eig(A)

print("Eigenvalues:")
print(eigenvalues)

print("\nEigenvectors:")
print(eigenvectors)
```

---

# 27. Relationship Between Eigendecomposition and SVD

For

$$
A=U\Sigma V^T,
$$

we have

$$
A^TA=V\Sigma^T\Sigma V^T.
$$

Therefore,

$$
\boxed{
\lambda_i(A^TA)=\sigma_i^2.
}
$$

So the right singular vectors are eigenvectors of $(A^TA)$.

Similarly,

$$
AA^T=U\Sigma\Sigma^TU^T,
$$

so the left singular vectors are eigenvectors of $(AA^T)$.

This makes SVD particularly powerful for rectangular matrices, where ordinary eigendecomposition may not directly apply.

---

# Part V — Other Low-Rank Factorizations

## 28. CUR Decomposition

CUR approximates a matrix using selected columns and rows:

$$
\boxed{
A\approx CUR.
}
$$

Here:

- $(C)$ contains selected columns;
- $(R)$ contains selected rows;
- $(U)$ is a small linking matrix.

![CUR sampling](figures/08_cur_sampling.png)

CUR can be useful when interpretability matters because the factors correspond directly to actual rows and columns of the original dataset.

---

## 29. Nonnegative Matrix Factorization

Nonnegative Matrix Factorization (NMF) seeks

$$
\boxed{
A\approx WH
}
$$

subject to

$$
W\geq0,
\qquad
H\geq0.
$$

This is useful when the data naturally represents quantities such as:

- counts;
- pixel intensities;
- frequencies;
- purchase quantities;
- document-word frequencies.

### Python

```python
import numpy as np
from sklearn.decomposition import NMF

A = np.array([
    [5, 4, 0, 0],
    [4, 5, 0, 1],
    [0, 0, 5, 4],
    [0, 1, 4, 5]
], dtype=float)

model = NMF(n_components=2, init="random", random_state=0)

W = model.fit_transform(A)
H = model.components_

A_approx = W @ H

print("W:")
print(W)

print("\nH:")
print(H)

print("\nApproximation:")
print(A_approx)
```

Install scikit-learn if necessary:

```bash
pip install scikit-learn
```

---

# Part VI — Matrix Factorization for Data Science

## 30. Matrix Factorization for Recommender Systems

Suppose

$$
R\in\mathbb{R}^{m\times n}
$$

is a user-item rating matrix.

Many entries may be unknown.

We can approximate it as

$$
\boxed{
R\approx UV^T
}
$$

where:

- $(U)$ represents latent user features;
- $(V)$ represents latent item features.

If

$$
U\in\mathbb{R}^{m\times k},
\qquad
V\in\mathbb{R}^{n\times k},
$$

then

$$
UV^T\in\mathbb{R}^{m\times n}.
$$

When $(k\ll\min(m,n))$, this is a low-dimensional representation.

---

## 31. Basic Matrix Factorization with Gradient Descent

We can minimize

$$
J(U,V)=
\sum_{(i,j)\in\Omega}
(R_{ij}-\mathbf u_i^T\mathbf v_j)^2.
$$

A simple implementation:

```python
import numpy as np

R = np.array([
    [5, 4, 0, 0],
    [4, 5, 0, 1],
    [0, 0, 5, 4],
    [0, 1, 4, 5]
], dtype=float)

m, n = R.shape
k = 2

rng = np.random.default_rng(0)

U = rng.random((m, k))
V = rng.random((n, k))

learning_rate = 0.01
epochs = 2000

for epoch in range(epochs):

    for i in range(m):
        for j in range(n):

            if R[i, j] == 0:
                continue

            error = R[i, j] - U[i] @ V[j]

            U[i] += learning_rate * error * V[j]
            V[j] += learning_rate * error * U[i]

R_approx = U @ V.T

print(R_approx)
```

This simple implementation illustrates the central idea. Production recommender systems typically use more sophisticated optimization, regularization, and handling of missing values.

---

# Part VII — Low-Rank Approximation for Images

## 32. Image as a Matrix

A grayscale image can be represented as

$$
A\in\mathbb{R}^{m\times n}.
$$

Each entry represents a pixel intensity.

The SVD gives

$$
A=U\Sigma V^T.
$$

Keeping only the first $(k)$ singular values gives

$$
A_k=U_k\Sigma_kV_k^T.
$$

This can significantly reduce the number of values required to store the image.

---

## 33. Image Compression Example

```python
import numpy as np
import matplotlib.pyplot as plt

# Example synthetic grayscale image
x = np.linspace(-1, 1, 200)
y = np.linspace(-1, 1, 160)

X, Y = np.meshgrid(x, y)

image = (
    np.exp(-5*((X + 0.3)**2 + (Y + 0.2)**2))
    + 0.8*np.exp(-8*((X - 0.3)**2 + (Y - 0.2)**2))
)

U, S, Vt = np.linalg.svd(image, full_matrices=False)

k = 10

compressed = (U[:, :k] * S[:k]) @ Vt[:k, :]

plt.imshow(compressed, cmap="gray")
plt.axis("off")
plt.show()
```

---

# 34. Storage Savings

The original $(m\times n)$ matrix contains

$$
mn
$$

values.

A rank-$(k)$ SVD requires approximately

$$
mk+k+nk
$$

values:

$$
\boxed{
k(m+n+1).
}
$$

Therefore, SVD compression is useful when

$$
k\ll\min(m,n).
$$

For example, if

$$
m=n=1000
$$

and

$$
k=20,
$$

the original matrix has

$$
1,000,000
$$

entries, whereas the factorization needs approximately

$$
20(1000+1000+1)=40,020
$$

values.

---

# Part VIII — PCA and Dimensionality Reduction

## 35. Low-Rank Approximation and PCA

Principal Component Analysis can be interpreted as finding a low-dimensional approximation to centered data.

Let

$$
X\in\mathbb{R}^{m\times n}
$$

be centered data.

Its SVD is

$$
X=U\Sigma V^T.
$$

The first columns of $(V)$ identify the most important directions.

![PCA low-dimensional structure](figures/07_pca_low_dimensional_structure.png)

---

## 36. PCA with SVD

```python
import numpy as np

X = np.array([
    [2, 1],
    [3, 2],
    [4, 2],
    [5, 4],
    [6, 4]
], dtype=float)

# Center data
X_centered = X - X.mean(axis=0)

# SVD
U, S, Vt = np.linalg.svd(
    X_centered,
    full_matrices=False
)

# Principal directions
components = Vt

# Explained variance
explained_variance = S**2 / (len(X) - 1)

# Explained variance ratio
explained_ratio = (
    explained_variance /
    explained_variance.sum()
)

print("Principal directions:")
print(components)

print("\nExplained variance ratio:")
print(explained_ratio)
```

---

# Part IX — Comparing Matrix Factorizations

## 37. Important Factorizations

| Factorization | Form | Main Use |
|---|---|---|
| LU | $(A=LU)$ | Solving linear systems |
| QR | $(A=QR)$ | Least squares, orthogonalization |
| Cholesky | $(A=LL^T)$ | Symmetric positive-definite matrices |
| Eigendecomposition | $(A=V\Lambda V^{-1})$ | Eigenvalue/eigenvector analysis |
| SVD | $(A=U\Sigma V^T)$ | Low-rank approximation, PCA, compression |
| CUR | $(A\approx CUR)$ | Interpretable low-rank approximation |
| NMF | $(A\approx WH)$ | Nonnegative latent features |

---

# 38. When Should You Use Which Factorization?

### Use LU when:

- solving many systems with the same square matrix;
- Gaussian elimination is appropriate;
- a general square matrix is involved.

### Use QR when:

- solving least-squares problems;
- orthogonal bases are useful;
- numerical stability is important.

### Use Cholesky when:

- $(A)$ is symmetric positive definite;
- a fast factorization is needed.

### Use eigendecomposition when:

- the matrix is square;
- eigenvalues/eigenvectors are the goal;
- spectral structure is important.

### Use SVD when:

- the matrix may be rectangular;
- low-rank approximation is needed;
- dimensionality reduction is needed;
- pseudoinverse computation is needed;
- robustness and numerical stability matter.

### Use NMF when:

- all values should remain nonnegative;
- latent additive components are desirable.

### Use CUR when:

- actual rows and columns should remain interpretable.

---

# Part X — A Complete Low-Rank Approximation Program

## 39. Complete Python Example

```python
import numpy as np
import matplotlib.pyplot as plt

# ---------------------------------
# 1. Create a matrix
# ---------------------------------

rng = np.random.default_rng(42)

A = rng.normal(size=(50, 40))

# Add some dominant low-dimensional structure
A[:, 0] += np.linspace(-4, 4, 50)
A[:, 1] += 2*np.sin(np.linspace(0, 6, 50))

# ---------------------------------
# 2. Compute SVD
# ---------------------------------

U, S, Vt = np.linalg.svd(
    A,
    full_matrices=False
)

print("Matrix shape:", A.shape)
print("Number of singular values:", len(S))

# ---------------------------------
# 3. Display singular values
# ---------------------------------

plt.figure(figsize=(8, 5))
plt.plot(
    np.arange(1, len(S) + 1),
    S,
    marker="o"
)
plt.xlabel("Index")
plt.ylabel("Singular value")
plt.title("Singular Value Spectrum")
plt.grid(True)
plt.show()

# ---------------------------------
# 4. Choose rank
# ---------------------------------

k = 5

# ---------------------------------
# 5. Construct approximation
# ---------------------------------

A_k = (
    U[:, :k] *
    S[:k]
) @ Vt[:k, :]

# ---------------------------------
# 6. Compute error
# ---------------------------------

frobenius_error = np.linalg.norm(
    A - A_k,
    ord="fro"
)

spectral_error = np.linalg.norm(
    A - A_k,
    ord=2
)

print("Approximation rank:", k)
print("Frobenius error:", frobenius_error)
print("Spectral error:", spectral_error)

# ---------------------------------
# 7. Verify rank
# ---------------------------------

print(
    "Approximation rank:",
    np.linalg.matrix_rank(A_k)
)
```

---

# 40. Building a Reusable Low-Rank Function

```python
import numpy as np

def low_rank_svd(A, k):
    """
    Return the rank-k truncated SVD approximation of A.
    """

    A = np.asarray(A, dtype=float)

    U, S, Vt = np.linalg.svd(
        A,
        full_matrices=False
    )

    if k < 1 or k > len(S):
        raise ValueError(
            "k must be between 1 and min(A.shape)"
        )

    A_k = (
        U[:, :k] * S[:k]
    ) @ Vt[:k, :]

    return A_k


A = np.random.randn(20, 15)

A_3 = low_rank_svd(A, 3)

print("Original:", A.shape)
print("Approximation:", A_3.shape)
print(
    "Rank:",
    np.linalg.matrix_rank(A_3)
)
```

---

# 41. Finding the Best Rank for a Target Error

```python
import numpy as np

def find_rank_for_energy(A, target=0.95):

    U, S, Vt = np.linalg.svd(
        A,
        full_matrices=False
    )

    energy = S**2

    cumulative = np.cumsum(energy)

    ratio = cumulative / energy.sum()

    k = np.searchsorted(
        ratio,
        target
    ) + 1

    return k, ratio, S


A = np.random.randn(100, 80)

k, ratio, S = find_rank_for_energy(
    A,
    target=0.90
)

print("Required rank:", k)
print("Captured energy:", ratio[k-1])
```

---

# Part XI — Practical Error and Rank Analysis

## 42. Rank Versus Error

```python
import numpy as np
import matplotlib.pyplot as plt

A = np.random.randn(40, 30)

U, S, Vt = np.linalg.svd(
    A,
    full_matrices=False
)

errors = []

for k in range(1, len(S) + 1):

    A_k = (
        U[:, :k] * S[:k]
    ) @ Vt[:k, :]

    error = np.linalg.norm(
        A - A_k,
        "fro"
    )

    errors.append(error)

plt.figure(figsize=(8, 5))
plt.plot(
    range(1, len(S) + 1),
    errors,
    marker="o"
)

plt.xlabel("Rank k")
plt.ylabel("Frobenius error")
plt.title("Low-Rank Approximation Error")
plt.grid(True)
plt.show()
```

---

# 43. Effective Rank

In real datasets, mathematical rank may be full while the **effective rank** is small.

For example,

$$
\sigma_1,\sigma_2,\sigma_3
$$

may be large, while

$$
\sigma_4,\ldots,\sigma_n
$$

are very small.

The matrix is technically full rank but behaves approximately like a rank-3 matrix.

This distinction is important in:

- noisy measurements;
- machine learning datasets;
- image data;
- natural language processing;
- recommendation systems.

---

# 44. Noise Reduction

Suppose

$$
A=A_{\text{signal}}+E
$$

where $(E)$ is noise.

If the signal has a low-rank structure, then the dominant singular components may represent the signal while smaller singular components represent noise.

A truncated SVD can therefore produce

$$
A_k\approx A_{\text{signal}}.
$$

Example:

```python
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(0)

x = np.linspace(0, 1, 100)
y = np.linspace(0, 1, 80)

X, Y = np.meshgrid(x, y)

signal = np.sin(2*np.pi*X) * np.cos(2*np.pi*Y)

noise = 0.4 * rng.normal(size=signal.shape)

A = signal + noise

U, S, Vt = np.linalg.svd(
    A,
    full_matrices=False
)

k = 5

denoised = (
    U[:, :k] * S[:k]
) @ Vt[:k, :]

plt.figure(figsize=(8, 5))
plt.imshow(denoised, cmap="gray")
plt.title("Low-Rank SVD Denoising")
plt.axis("off")
plt.show()
```

---

# Part XII — Important Mathematical Results

## 45. Rank and Singular Values

If

$$
A=U\Sigma V^T,
$$

then

$$
\boxed{
\text{rank}(A)=
\text{number of nonzero singular values}.
}
$$

---

## 46. Rank-$(k)$ Approximation Error

For truncated SVD,

$$
A_k=
\sum_{i=1}^{k}
\sigma_i\mathbf u_i\mathbf v_i^T,
$$

we have

$$
\boxed{
\|A-A_k\|_2=\sigma_{k+1}
}
$$

and

$$
\boxed{
\|A-A_k\|_F=
\sqrt{
\sum_{i=k+1}^{r}
\sigma_i^2
}.
}
$$

---

## 47. Frobenius Norm and Singular Values

The Frobenius norm satisfies

$$
\boxed{
\|A\|_F^2=
\sum_i\sigma_i^2.
}
$$

Therefore, the squared singular values can be interpreted as contributions to the total matrix energy.

---

## 48. Spectral Norm

The spectral norm is the largest singular value:

$$
\boxed{
\|A\|_2=\sigma_1.
}
$$

This gives the maximum amount by which $(A)$ can stretch a unit vector.

---

## 49. Condition Number

For a full-rank matrix,

$$
\boxed{
\kappa_2(A)=
\frac{\sigma_{\max}}{\sigma_{\min}}.
}
$$

A large condition number indicates that the matrix may be sensitive to perturbations.

```python
import numpy as np

A = np.array([
    [1, 1],
    [1, 1.0001]
], dtype=float)

S = np.linalg.svd(
    A,
    compute_uv=False
)

condition_number = S[0] / S[-1]

print("Singular values:", S)
print("Condition number:", condition_number)
```

---

# Part XIII — Applications

## 50. Applications of Low-Rank Approximation

### 1. Image compression

Represent an image with fewer singular components.

### 2. Data compression

Store dominant patterns instead of every matrix entry.

### 3. Noise reduction

Discard weak components associated with noise.

### 4. PCA

Reduce the number of dimensions while preserving important variation.

### 5. Recommendation systems

Represent users and products using a small number of latent factors.

### 6. Natural language processing

Use low-dimensional representations of term-document matrices.

### 7. Computer vision

Extract dominant visual patterns.

### 8. Scientific computing

Reduce computational cost for large structured matrices.

---

# 51. Computational Complexity

For a dense $(m\times n)$ matrix, computing a full SVD can be expensive.

A rough conceptual complexity is related to

$$
O(mn\min(m,n)).
$$

For large matrices, **truncated or randomized SVD** methods can be much more efficient when only a small number of dominant components are required.

For example, scikit-learn provides:

```python
from sklearn.decomposition import TruncatedSVD

model = TruncatedSVD(
    n_components=5,
    random_state=0
)

X_reduced = model.fit_transform(X)

print(X_reduced.shape)
```

---

# 52. Randomized Low-Rank Approximation

For very large matrices, randomized algorithms can approximate the dominant singular subspace efficiently.

A common idea is:

1. generate a random test matrix $(\Omega)$;
2. compute $(Y=A\Omega)$;
3. find an orthonormal basis $(Q)$ for the range of $(Y)$;
4. approximate $(A)$ using the smaller matrix $(Q^TA)$;
5. compute an SVD of the smaller matrix.

The detailed randomized algorithm is beyond basic linear algebra, but the key idea is:

$$
A\approx QQ^TA.
$$

This can dramatically reduce computational cost.

---

# Part XIV — Summary Table

## 53. Low-Rank and Matrix Factorization Summary

| Concept | Mathematical Form | Main Idea |
|---|---|---|
| Rank | $(\operatorname{rank}(A))$ | Dimension of row/column space |
| Rank-1 matrix | $(uv^T)$ | Outer product |
| Low-rank approximation | $(A\approx A_k)$ | Approximate with small rank |
| SVD | $(A=U\Sigma V^T)$ | Orthogonal-scale-orthogonal factorization |
| Truncated SVD | $(A_k=U_k\Sigma_kV_k^T)$ | Best rank-$(k)$ approximation |
| LU | $(PA=LU)$ | Triangular factors |
| QR | $(A=QR)$ | Orthogonal + triangular |
| Cholesky | $(A=LL^T)$ | SPD factorization |
| Eigendecomposition | $(A=V\Lambda V^{-1})$ | Eigenvalue structure |
| CUR | $(A\approx CUR)$ | Actual rows/columns as factors |
| NMF | $(A\approx WH)$ | Nonnegative latent components |

---

# 54. Practice Questions

## Conceptual Questions

1. Define matrix rank.
2. What is a low-rank matrix?
3. Explain a rank-1 matrix.
4. What is an outer product?
5. Why is low-rank approximation useful?
6. Explain truncated SVD.
7. State the Eckart–Young theorem.
8. What do singular values represent?
9. How can singular values be used to determine effective rank?
10. Explain the difference between mathematical rank and effective rank.
11. Compare LU and QR factorization.
12. When is Cholesky factorization applicable?
13. Explain the connection between eigendecomposition and SVD.
14. What is CUR decomposition?
15. What is NMF?
16. How is matrix factorization used in recommender systems?

## Computational Questions

17. Find the rank of

$$
A=
\begin{bmatrix}
1&2&3\\
2&4&6\\
3&6&9
\end{bmatrix}.
$$

18. Express a rank-1 matrix as an outer product.

19. Compute the SVD of

$$
A=
\begin{bmatrix}
3&0\\
0&2
\end{bmatrix}.
$$

20. Construct a rank-1 approximation using SVD.

21. Construct a rank-2 approximation of a $(5\times5)$ matrix.

22. Plot the singular values of a matrix.

23. Calculate Frobenius approximation error.

24. Determine the rank needed to retain 95% of the singular-value energy.

25. Perform PCA using SVD.

26. Implement LU factorization using SciPy.

27. Implement QR factorization using NumPy.

28. Implement Cholesky factorization.

29. Construct a simple recommender system using matrix factorization.

30. Compare SVD and NMF on a nonnegative dataset.

---

# 55. Quick Reference: Python Functions

```python
# Rank
np.linalg.matrix_rank(A)

# SVD
U, S, Vt = np.linalg.svd(A)

# Singular values only
S = np.linalg.svd(A, compute_uv=False)

# Frobenius norm
np.linalg.norm(A, "fro")

# Spectral norm
np.linalg.norm(A, 2)

# QR
Q, R = np.linalg.qr(A)

# Cholesky
L = np.linalg.cholesky(A)

# Eigenvalues/eigenvectors
values, vectors = np.linalg.eig(A)

# Symmetric eigenproblem
values, vectors = np.linalg.eigh(A)

# Pseudoinverse
A_plus = np.linalg.pinv(A)
```

---

# 57. Conclusion

Low-rank approximation provides a powerful way to simplify large matrices while retaining their most important structure.

The central idea is

$$
\boxed{
A\approx A_k
}
$$

where

$$
\operatorname{rank}(A_k)=k
$$

and $(k)$ is substantially smaller than the dimensions of $(A)$.

The Singular Value Decomposition provides the mathematically optimal solution:

$$
\boxed{
A_k=U_k\Sigma_kV_k^T.
}
$$

Matrix factorization extends this idea into a broader collection of techniques:

$$
\boxed{
\text{Matrix}
\rightarrow
\begin{cases}
LU\\
QR\\
Cholesky\\
Eigendecomposition\\
SVD\\
CUR\\
NMF
\end{cases}
}
$$

These methods form an important computational foundation for linear algebra, numerical computing, machine learning, artificial intelligence, data analytics, computer vision, and recommender systems.

---
## ❓: CHALLENGING Questions - Check Your Understanding 
* ➡️ **[Q-01]**
* ➡️ 

---
## 📚 References 
* **[R-01]** Linear Algebra by Gilbert Strang, MIT Press
* **[R-02]** ChatGPT - for examples and codes

The most important practical principle is:

> **When a matrix has strong low-dimensional structure, a carefully chosen low-rank factorization can reduce storage and computation while preserving the information that matters most.**
