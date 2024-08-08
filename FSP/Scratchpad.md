#### Part (a): Cumulative Distribution Function (CDF) of (Z = X + Y)

The random variables (X) and (Y) are uniformly distributed on ([0,1]). The sum (Z = X + Y) will have a range from 0 to 2. To find the cdf (F_Z(z)), we need to consider different ranges of (z):

1. For $(0 \leq z \leq 1)$: $$ F_Z(z) = P(Z \leq z) = P(X + Y \leq z) $$ The region where (X + Y \leq z) is a right triangle with legs of length (z) within the unit square. The area of this triangle is (\frac{1}{2}z^2).
    
2. For (1 < z \leq 2): $$ F_Z(z) = P(Z \leq z) = P(X + Y \leq z) $$ The region where (X + Y \leq z) is the entire unit square minus a right triangle with legs of length (2 - z). The area of this region is (1 - \frac{1}{2}(2 - z)^2).
    

Combining these, we get: $$ F_Z(z) = \begin{cases} 0 & \text{if } z < 0 \ \frac{1}{2}z^2 & \text{if } 0 \leq z \leq 1 \ 1 - \frac{1}{2}(2 - z)^2 & \text{if } 1 < z \leq 2 \ 1 & \text{if } z > 2 \end{cases} $$

#### Part (b): Probability (P(X + Y \leq 1))

From the cdf (F_Z(z)) at (z = 1): $$ P(X + Y \leq 1) = F_Z(1) = \frac{1}{2}(1)^2 = \frac{1}{2} $$

#### Part (c): Conditional pdf (f_{Y|Z}(y|z))

To find the conditional pdf (f_{Y|Z}(y|z)), we use the joint pdf (f_{X,Y}(x,y)) and the marginal pdf (f_Z(z)).

The joint pdf (f_{X,Y}(x,y)) is 1 for ((x,y) \in [0,1] \times [0,1]).

The marginal pdf (f_Z(z)) can be derived from the cdf (F_Z(z)): $$ f_Z(z) = \frac{d}{dz} F_Z(z) = \begin{cases} z & \text{if } 0 \leq z \leq 1 \ 2 - z & \text{if } 1 < z \leq 2 \end{cases} $$

The conditional pdf (f_{Y|Z}(y|z)) is given by: $$ f_{Y|Z}(y|z) = \frac{f_{X,Y}(x,y)}{f_Z(z)} $$

For (0 \leq z \leq 1): $$ f_{Y|Z}(y|z) = \frac{1}{z} \quad \text{for } 0 \leq y \leq z $$

For (1 < z \leq 2): $$ f_{Y|Z}(y|z) = \frac{1}{2 - z} \quad \text{for } z - 1 \leq y \leq 1 $$

#### Part (d): Probability (P(X \leq \alpha | X - Y \geq 0))

The condition (X - Y \geq 0) implies (X \geq Y). We need to find (P(X \leq \alpha | X \geq Y)).

The joint distribution of (X) and (Y) is uniform over the unit square. The region (X \geq Y) is the lower triangle of the unit square with area (\frac{1}{2}).

The region (X \leq \alpha) within (X \geq Y) is a smaller triangle with vertices ((0,0)), ((\alpha,0)), and ((\alpha,\alpha)).

The area of this smaller triangle is (\frac{1}{2}\alpha^2).

Thus, the conditional probability is: $$ P(X \leq \alpha | X \geq Y) = \frac{\text{Area of smaller triangle}}{\text{Area of larger triangle}} = \frac{\frac{1}{2}\alpha^2}{\frac{1}{2}} = \alpha^2 $$

So, the probability (P(X \leq \alpha | X - Y \geq 0) = \alpha^2).

### Summary:

a) (F_Z(z) = \begin{cases} 0 & \text{if } z < 0 \ \frac{1}{2}z^2 & \text{if } 0 \leq z \leq 1 \ 1 - \frac{1}{2}(2 - z)^2 & \text{if } 1 < z \leq 2 \ 1 & \text{if } z > 2 \end{cases})

b) (P(X + Y \leq 1) = \frac{1}{2})

c) (f_{Y|Z}(y|z) = \begin{cases} \frac{1}{z} & \text{for } 0 \leq y \leq z \text{ and } 0 \leq z \leq 1 \ \frac{1}{2 - z} & \text{for } z - 1 \leq y \leq 1 \text{ and } 1 < z \leq 2 \end{cases})

d) (P(X \leq \alpha | X - Y \geq 0) = \alpha^2)