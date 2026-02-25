# Perspective Projection

In perspective projection $\tan(\theta)$ is the angle of the perspective triangle but we want a scaler value like $\frac{1}{45} > \frac{1}{60}$ so we make $\frac{1}{\tan(\theta)}$.

<p align="center">
  <img src="images/wide-angle-vs-telephoto-lens.webp" width="500"/>
</p>

We have something called aspect ratio. It is a ratio of window width/window height and it is used to make objects have correct width like size because window is a rectangle not a square in most times.

We multiply aspect ratio with $x$ and we multiply $\frac{1}{\tan(\theta)}$ with $x, y$ because we want object to appear smaller if field of view (FOV) bigger and appear bigger if FOV smaller.

Then we have

$$
\frac{far + near}{far - near}
$$

and

$$
\frac{2 \cdot far \cdot near}{far - near}
$$

to normalize near and far from $-1$ to $1$ to make if object $z$ is like a big number it normalizes it from $-1$ to $1$ and this formula is found in the calculation of the non-linear depth in the GPU.

Then the hard part.

Firstly lets call

$$
\frac{far + near}{far - near}
$$

term $A$ and call

$$
\frac{2 \cdot far \cdot near}{far - near}
$$

$B$ because of multiplication of perspective projection matrix with the vertex $[X,Y,Z,W]$ components $A$ is being multiplied by $z$ and this is useful to make the normalization to $-1, 1$ correct so $(A \cdot Z + B)$ is the formula to make the z clip that is the z normalized $(-1,1)$.

So now we need to use this to calculate the non linear depth. To achieve that we can make

$$
\frac{A \cdot Z}{-Z} + \frac{B}{-Z}
$$

so it is

$$
-A - \frac{B}{Z}
$$

that is

$$
-A - B \cdot \frac{1}{Z}
$$

so now we have two constants to normalize $z$ and the $\frac{1}{Z}$ to make depth non linear.

We need depth non linear because imagine this if a tree is 3 meters from you so if you moved it 1 meter right you will see the effect clearly now imagine that the tree is 50 meters away if you moved the tree the same one meter you will not see the difference.

In perspective projection geometry shrinks proportionally to $\frac{1}{Z}$ so if we want depth precision to match how perspective compresses space depth must also behave like $\frac{1}{Z}$ if depth was linear far objects would collapse into very tiny depth differences and cause massive Z-fighting.

Non-linear depth gives very high precision near camera and less precision far away which matches how perspective works.