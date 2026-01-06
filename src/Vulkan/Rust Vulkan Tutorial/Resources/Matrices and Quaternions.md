---
tags: [math, gamedev, graphics, matrices, quaternions, linear-algebra, faq]
source: "The Matrix and Quaternions FAQ v1.19"
author: "Andreas Junghanns (Maintainer)"
---

# The Matrix and Quaternions FAQ

**Version 1.19 (20th March 2002)**
*This FAQ is maintained by "andreas@(no-spam)cs.ualberta.ca".*

## Introduction

### I1. Important note relating to OpenGL and this document

In this document (as in most math textbooks), all matrices are drawn in the standard mathematical manner. Unfortunately, graphics libraries like IrisGL, OpenGL, and SGI's Performer represent them with the rows and columns swapped (column-major order).

In this document, a $4 \times 4$ Translation matrix is represented as:

$$
M = \begin{bmatrix} 
1 & 0 & 0 & X \\
0 & 1 & 0 & Y \\
0 & 0 & 1 & Z \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

In **OpenGL/Performer**, the matrix is stored in a one-dimensional array where the ordering is transposed:

**This Document (Row Major):**
$$
M = \begin{bmatrix} 
0 & 1 & 2 & 3 \\
4 & 5 & 6 & 7 \\
8 & 9 & 10 & 11 \\
12 & 13 & 14 & 15 
\end{bmatrix}
$$

**OpenGL (Column Major):**
$$
M = \begin{bmatrix} 
0 & 4 & 8 & 12 \\
1 & 5 & 9 & 13 \\
2 & 6 & 10 & 14 \\
3 & 7 & 11 & 15 
\end{bmatrix}
$$

---

## Basics

### Q1. What is a matrix?
A matrix is a two-dimensional array of numeric data.
The size is defined by $M$ rows and $N$ columns ($M \times N$).

Elements are referenced as $M_{i,j}$ (Row $i$, Column $j$).
Example $4 \times 4$ matrix:
$$
M = \begin{bmatrix} 
00 & 01 & 02 & 03 \\
10 & 11 & 12 & 13 \\
20 & 21 & 22 & 23 \\
30 & 31 & 32 & 33 
\end{bmatrix}
$$

* **2x2**: Rotations, shears, image processing.
* **3x3**: Low-budget 3D animation (rotation/scale), convolution.
* **4x4**: High-end 3D animation (includes translation and perspective projection).

### Q2. What is the "order" of a matrix?
The "order" is the size ($M \times N$).

### Q3. How do I represent a matrix using C/C++?
Use `typedef` with linear arrays for efficiency.

```cpp
typedef float MATRIX3[9];
typedef float MATRIX4[16];
```

Mapping a 2D matrix to a linear array:
$$
M = \begin{bmatrix} 
0 & 1 & 2 & 3 \\
4 & 5 & 6 & 7 \\
8 & 9 & 10 & 11 \\
12 & 13 & 14 & 15 
\end{bmatrix}
$$
In code: `mat[0] = M00`, `mat[3] = M03`, `mat[12] = M30`, etc.

### Q4. What are the advantages of using matrices?
While calculating rotation manually per vertex ($x' = x \cos \theta - y \sin \theta$) seems faster for a single operation, matrices provide:
1.  **Unified approach**: Rotations, translations, and scales are handled uniformly.
2.  **Concatenation**: You can multiply a rotation, scale, and translation matrix once to get a single "World" matrix, then apply that *one* matrix to thousands of vertices. This is significantly faster than applying individual math operations to every vertex.

### Q5. How do matrices relate to coordinate systems?
The first three columns (in this document's notation) define the direction vectors of the X, Y, and Z axes.

$$
M = \begin{bmatrix} 
A & B & C & D \\
E & F & G & H \\
I & J & K & L \\
M & N & O & P 
\end{bmatrix}
$$

* **X-axis vector**: $[A, E, I]$
* **Y-axis vector**: $[B, F, J]$
* **Z-axis vector**: $[C, G, K]$

---

## Arithmetic

### Q6. What is the identity matrix?
$$
I = \begin{bmatrix} 
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

### Q7. What is the major diagonal matrix of a matrix?
The elements where $i = j$ (e.g., $M_{00}, M_{11}, M_{22}, \dots$).

### Q8. What is the transpose of a matrix?
Swapping rows and columns: $M'_{ij} = M_{ji}$.
Example:
$$
M = \begin{bmatrix} 0.7 & -0.8 \\ 0.8 & 0.7 \end{bmatrix} \xrightarrow{\text{Transpose}} T = \begin{bmatrix} 0.7 & 0.8 \\ -0.8 & 0.7 \end{bmatrix}
$$
*Note: If $M$ is a rotation matrix, the transpose is its inverse.*

### Q9. How do I add two matrices?
Add corresponding elements: $R_{ij} = M_{ij} + L_{ij}$.

### Q10. How do I subtract two matrices?
Subtract corresponding elements: $R_{ij} = M_{ij} - L_{ij}$.

### Q11. How do I multiply two matrices together?
"Multiply row into column and sum the result."
$$
R_{ij} = \sum_{k=1}^{n} M_{ik} \times L_{kj}
$$
**Dimensions:** $(A \times B) \cdot (B \times C) = (A \times C)$.

Example ($4 \times 4$) multiplied by ($4 \times 2$):
$$
\begin{bmatrix} A & B & C & D \\ E & F & G & H \\ \dots & \dots & \dots & \dots \end{bmatrix} \times \begin{bmatrix} Q & R \\ S & T \\ U & V \\ W & X \end{bmatrix} = \begin{bmatrix} AQ+BS+CU+DW & AR+BT+CV+DX \\ \dots & \dots \end{bmatrix}
$$

### Q12. How do I square or raise a matrix to a power?
$M^2 = M \cdot M$.
$M^3 = M \cdot M \cdot M$.
Requires a square matrix.

**Optimization Algorithm:**
```cpp
// m = matrix, i = power, a = result, I = identity
a = I;
while (i > 0) {
   if (i % 2) a = a * m;
   i = i / 2;
   m = m * m;
}
```

### Q13. How do I multiply one or more vectors by a matrix?
Treat the vector as a column matrix.
$$
\begin{bmatrix} A & B & C & D \\ E & F & G & H \\ I & J & K & L \\ M & N & O & P \end{bmatrix} \cdot \begin{bmatrix} x \\ y \\ z \\ 1 \end{bmatrix} = \begin{bmatrix} Ax + By + Cz + D \\ Ex + Fy + Gz + H \\ Ix + Jy + Kz + L \\ Mx + Ny + Oz + P \end{bmatrix}
$$

---

## Determinants and Inverses

### Q14. What is the determinant of a matrix?
A scalar value indicating if an inverse exists. If $det(M) = 0$, no inverse exists.
* **Isotropic Matrix**: Determinant is 1.0 (e.g., Rotation matrices).

### Q15. How do I calculate the determinant?
For a $2 \times 2$ matrix:
$$
M = \begin{bmatrix} A & B \\ C & D \end{bmatrix} \rightarrow \det = AD - BC
$$

### Q16. What are Isotropic and Anisotropic matrices?
* **Isotropic**: Sum of squares of rows/columns = 1. Used for rotation.
* **Anisotropic**: Non-uniform scaling.
    * *Warning*: Anisotropic scaling messes up lighting normals. You must calculate the **Inverse Transpose** matrix to transform normals correctly if using non-uniform scaling.

### Q17. What is the inverse of a matrix?
$M \cdot M^{-1} = I$.

### Q20. How do I calculate the inverse of a rotation matrix?
For pure rotation matrices, the Inverse is equal to the Transpose.
$$ M^{-1} = M^T $$

### Q22. How do I calculate the inverse of a 2x2 matrix?
$$
M^{-1} = \frac{1}{AD-BC} \begin{bmatrix} D & -B \\ -C & A \end{bmatrix}
$$

### Q23. Inverse of a 3x3 Matrix
Using Kramer's Rule / Co-factors:

```cpp
VFLOAT m3_det(MATRIX3 mat) {
    return mat[0] * (mat[4]*mat[8] - mat[7]*mat[5])
         - mat[1] * (mat[3]*mat[8] - mat[6]*mat[5])
         + mat[2] * (mat[3]*mat[7] - mat[6]*mat[4]);
}

int m3_inverse(MATRIX3 mr, MATRIX3 ma) {
    VFLOAT det = m3_det(ma);
    if (fabs(det) < 0.0005) { m3_identity(mr); return 0; }
    
    mr[0] =  (ma[4]*ma[8] - ma[5]*ma[7]) / det;
    mr[1] = -(ma[1]*ma[8] - ma[7]*ma[2]) / det;
    mr[2] =  (ma[1]*ma[5] - ma[4]*ma[2]) / det;
    // ... (rest of implementation in original text)
    return 1;
}
```

### Q24. Inverse of a 4x4 Matrix
Uses sub-matrices (minors). See code in original document for full implementation involving `m4_submat` and `m4_det`.

---

## Transforms

### Q26. What is a rotation matrix?
Based on sine and cosine.
2D Rotation:
$$
\begin{bmatrix} \cos A & -\sin A \\ \sin A & \cos A \end{bmatrix}
$$

### Q27-Q30. Axis Rotations (Right-Handed System)


**X-Axis Rotation:**
$$
R_x = \begin{bmatrix} 
1 & 0 & 0 & 0 \\
0 & \cos A & -\sin A & 0 \\
0 & \sin A & \cos A & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

**Y-Axis Rotation:**
$$
R_y = \begin{bmatrix} 
\cos A & 0 & \sin A & 0 \\
0 & 1 & 0 & 0 \\
-\sin A & 0 & \cos A & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

**Z-Axis Rotation:**
$$
R_z = \begin{bmatrix} 
\cos A & -\sin A & 0 & 0 \\
\sin A & \cos A & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

### Q31. What are Euler angles?
Rotations specified as 3 separate angles for X, Y, and Z axes.

### Q33. How do I combine rotation matrices?
Matrix multiplication: $M_{final} = M_x \cdot M_y \cdot M_z$.
**Order is important.** $X \cdot Y \neq Y \cdot X$.

### Q34. What is Gimbal Lock?

When two rotation axes align (e.g., Pitching up 90 degrees makes Yaw and Roll rotate around the same physical axis), losing a degree of freedom.

### Q36. Generating a Rotation Matrix from Euler Angles (Optimized)
Instead of multiplying 3 matrices ($X \cdot Y \cdot Z$), fill the matrix directly using the algebraic result.

Given $A, B = \cos x, \sin x$; $C, D = \cos y, \sin y$; $E, F = \cos z, \sin z$.
The matrix $M = X \cdot Y \cdot Z$ is:

$$
M = \begin{bmatrix}
CE & -CF & D & 0 \\
BDE+AF & -BDF+AE & -BC & 0 \\
-ADE+BF & ADF+BE & AC & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

### Q37. Extracting Euler Angles from a Rotation Matrix
Given the matrix above, we can deduce:
1.  $D = \sin(Y) = M_{0,2}$ (or `mat[2]`).
2.  If $\cos Y \neq 0$:
    * Solve for X using $M_{1,2}$ and $M_{2,2}$.
    * Solve for Z using $M_{0,0}$ and $M_{0,1}$.
3.  If $\cos Y = 0$ (Gimbal Lock):
    * Force one angle to 0, solve for the other.

### Q38. Rotation Matrix for Arbitrary Axis and Angle
Using vector $u, v, w$ (normalized) and angle $\phi$:
$$
\begin{bmatrix}
c + u^2(1-c) & u v (1-c) - w s & u w (1-c) + v s \\
u v (1-c) + w s & c + v^2(1-c) & v w (1-c) - u s \\
u w (1-c) - v s & v w (1-c) + u s & c + w^2(1-c)
\end{bmatrix}
$$
*Where $c = \cos \phi, s = \sin \phi$.*

### Q39. Rotation Matrix to map Vector A to Vector B
1.  **Axis**: $V_{axis} = V_s \times V_f$ (Cross product).
2.  **Angle**: $Angle = \arccos(V_s \cdot V_f)$ (Dot product).
3.  Use Q38 to generate the matrix.

### Q41. Translation Matrix
$$
T = \begin{bmatrix} 
1 & 0 & 0 & X \\
0 & 1 & 0 & Y \\
0 & 0 & 1 & Z \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

### Q42. Scaling Matrix
$$
S = \begin{bmatrix} 
X & 0 & 0 & 0 \\
0 & Y & 0 & 0 \\
0 & 0 & Z & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

### Q43. Shearing Matrix

$$
Sh = \begin{bmatrix} 
1 & S_{yx} & S_{zx} & 0 \\
S_{xy} & 1 & S_{zy} & 0 \\
S_{xz} & S_{yz} & 1 & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$
Where $S_{xy}$ shears X by Y.

---

## Quaternions

### Q47. What are quaternions?

An extension of complex numbers to 4D ($x, y, z, w$) used to represent rotation without Gimbal lock.

### Q49. Conjugate of a Quaternion
$$ \bar{Q}_r = (Q_r.scalar, -Q_r.vector) $$
Code:
```cpp
qr->qw =  qa->qw;
qr->qx = -qa->qx;
qr->qy = -qa->qy;
qr->qz = -qa->qz;
```

### Q51. Magnitude
$$ |Q| = \sqrt{w^2 + x^2 + y^2 + z^2} $$

### Q53. Multiplying Quaternions
$$ Q_r = Q_1 \cdot Q_2 = (w_1 w_2 - v_1 \cdot v_2, w_1 v_2 + w_2 v_1 + v_1 \times v_2) $$
*Note: Quaternion multiplication is non-commutative.*

### Q54. Converting Quaternion to Rotation Matrix
For $Q = [X, Y, Z, W]$:

$$
M = \begin{bmatrix}
1 - 2(Y^2 + Z^2) & 2(XY - ZW) & 2(XZ + YW) & 0 \\
2(XY + ZW) & 1 - 2(X^2 + Z^2) & 2(YZ - XW) & 0 \\
2(XZ - YW) & 2(YZ + XW) & 1 - 2(X^2 + Y^2) & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

### Q55. Converting Rotation Matrix to Quaternion
Algorithm involves checking the **Trace** (sum of diagonal elements).
$$ T = 1 + M_{00} + M_{11} + M_{22} $$
1.  If $T > 0$:
    $$ S = \sqrt{T} \times 2 $$
    $$ X = (M_{21} - M_{12}) / S $$
    $$ Y = (M_{02} - M_{20}) / S $$
    $$ Z = (M_{10} - M_{01}) / S $$
    $$ W = 0.25 \times S $$
2.  If $T \le 0$: Use the largest diagonal element to determine the calculation (see original code for conditional branching).

### Q56. Axis/Angle to Quaternion
$$ X = axis.x \cdot \sin(angle/2) $$
$$ Y = axis.y \cdot \sin(angle/2) $$
$$ Z = axis.z \cdot \sin(angle/2) $$
$$ W = \cos(angle/2) $$

### Q61. Linear Interpolation (SLERP)
To smoothly interpolate between Matrix A and Matrix B:
1.  Convert matrices to Quaternions ($Q_a, Q_b$).
2.  Use Spherical Linear Interpolation (SLERP) on the quaternions.
3.  Convert the result back to a matrix.

### Q63. Rotating a Vector with a Quaternion
$$ v' = Q \cdot v \cdot Q^{-1} $$
Where $v$ is treated as a quaternion with $w=0$.