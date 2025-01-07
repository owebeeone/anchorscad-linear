# anchorscad-linear

A linear algebra library designed specifically for 3D geometric transformations in AnchorSCAD. This library provides specialized matrix and vector operations optimized for working with OpenSCAD's multmatrix transform function.

## Key Features

- Specialized 4x4 matrix operations for 3D transformations (GMatrix)
- 3D/4D vector operations (GVector) 
- Rotation, translation, and scaling transformations
- Angle handling in degrees, radians, or sin/cos pairs
- Plane and line intersection calculations

## Core Classes

### GVector

A 3D vector class that internally uses homogeneous coordinates (4D with last element always 1).

```python
from anchorscad_lib.linear import GVector, X_AXIS, Y_AXIS, Z_AXIS

# Create vectors
v1 = GVector([1, 2, 3]) # Creates [1, 2, 3, 1]
v2 = GVector([1, 2, 3, 1]) # Same as above

# Vector operations
v3 = v1 + v2 # Addition
v4 = v1 - v2 # Subtraction
v5 = v1 *2 # Scalar multiplication
v6 = v1 / 2 # Scalar division

# Vector methods
length = v1.length() # Vector length
normalized = v1.N # Normalized vector
dot = v1.dot3D(v2) # Dot product
cross = v1.cross3D(v2) # Cross product
```

### GMatrix

A 4x4 matrix class specialized for 3D transformations. The last row is always maintained as [0, 0, 0, 1].

```python
from anchorscad_lib.linear import GMatrix, IDENTITY
# Create matrices
m1 = GMatrix([
    [1, 0, 0, 0], # 4x4 matrix
    [0, 1, 0, 0],
    [0, 0, 1, 0],
    [0, 0, 0, 1]])
# Matrix operations
m2 = m1 * m1 # Matrix multiplication
m3 = ~m1 # Matrix inverse (same as m1.I)
v2 = m1 v1 # Transform vector
```

### Angle

The `Angle` class and `angle` function are used to represent and manipulate angles in different units. This can be used to avoid transforming angles say from (sin, cos) to degrees and then back to (sin, cos) to use in a transformation matrix.

```python
from anchorscad_lib.linear import angle

# Create angles
a1 = angle(45) # 45 degrees
a2 = angle(radians=3.14159) # π radians
a3 = angle(sinr_cosr=(0, 1)) # Using sin/cos pair

# Angle operations
a4 = a1 + a2 # Addition
a5 = a1 - a2 # Subtraction
a6 = -a1 # Negation

# Rotations
a7 = a1.rotX() # Rotate around X axis (also rotY and rotZ)
a8 = a1.rotV(v1) # Rotate around vector v1

# Sweep
a9 = a1.sweepRadians(positive_dir=True, non_zero=True) # Sweep angle in the positive direction
a10 = a1.sweepRadians(positive_dir=False, non_zero=True) # Sweep angle in the negative direction
```
## Transformation Functions

### Basic Transformations

```python
from anchorscad_lib.linear import translate, scale, rotX, rotY, rotZ
# Translation
t1 = translate([1, 2, 3]) # Translate by vector
t2 = tranX(5) # Translate along X axis
t3 = tranY(5) # Translate along Y axis
t4 = tranZ(5) # Translate along Z axis

# Scaling
s1 = scale([2, 2, 2]) # Scale by vector
s2 = scale(2) # Uniform scale

# Rotation
r1 = rotX(90) # Rotate 90° around X axis
r2 = rotY(45) # Rotate 45° around Y axis
r3 = rotZ(30) # Rotate 30° around Z axis
r4 = rotV(v1) # Rotate around vector v1
r5 = rotVSinCos(v1, sinr, cosr) # Rotate around vector v1 with sin/cos pair
```

### Advanced Transformations

```python
from anchorscad_lib.linear import rotV, rot_to_V, rotAlign, mirror
# Rotate from one vector to another
r5 = rot_to_V(v1, v2) # Creates a rotation matrix that rotates v1 to align with v2

# Align vectors while preserving an axis
r6 = rotAlign(preserve_axis, align_axis, plane_axis)

# Mirror transformations
m1 = mirror(axis) # Mirror matrix for the given axis
```

## Plane and Line Intersection

```python

from anchorscad_lib.linear import *
# Find intersection line of two planes
# Planes are represented as GMatrix objects where the Z axis is the normal to the plane.
line = plane_intersect(plane1, plane2)

# Find intersection point of plane and line
# Line is a GMatrix object where the Z axis is the line direction.
point = plane_line_intersect(plane, line)

# Calculate distances
d1 = distance_between(point1, point2)
d2 = distance_between_point_plane(point, plane)

```

## Constants

- `IDENTITY`: Identity matrix
- `X_AXIS`, `Y_AXIS`, `Z_AXIS`: Standard basis vectors
- `ZERO_VEC`: Zero vector
- `MIRROR_X`, `MIRROR_Y`, `MIRROR_Z`: Mirror matrices
- `ROTX_90`, `ROTY_90`, `ROTZ_90`: 90° rotation matrices
- `ROTX_180`, `ROTY_180`, `ROTZ_180`: 180° rotation matrices
- `ROTX_270`, `ROTY_270`, `ROTZ_270`: 270° rotation matrices

## Installation

```bash
pip install anchorscad-linear
```


## Usage Example

```python
from anchorscad_lib.linear import *

# Create transformation matrix
transform = (translate([1, 2, 3])   # Translate last
             * rotZ(45)             # Then rotate 45° around Z
             * scale(2))            # Scale first

# Transform a vector
v1 = GVector([1, 0, 0])
v2 = transform * v1

# Chain transformations
result = transform * transform.I * v1 # Should equal v1
assert result.is_approx_equal(v1)
```

## License

This project is licensed under the LGPLv2.1 license. See the LICENSE file for details.
