

![](../figures/Big%20O.png)

![](../figures/Big%20O-1.png)


![](../figures/Big%20O-2.png)

The next question that comes to mind is how you know which algorithm has which time complexity, given that this is meant to be a cheatsheet 😂.

- When your calculation is not dependent on the input size, it is a constant time complexity (O(1)).
- When the input size is reduced by half, maybe when iterating, handling [recursion](https://joelolawanle.com/posts/recursion-in-javascript-explained-for-beginners), or whatsoever, it is a logarithmic time complexity (O(log n)).
- When you have a **single loop** within your algorithm, it is linear time complexity (O(n)).
- When you have **nested loops within your algorithm**, meaning a loop in a loop, it is quadratic time complexity (O(n^2)).
- When the growth rate doubles with each addition to the input, it is exponential time complexity (O2^n).


![](../figures/Big%20O-3.png)


- **O(1)** - Excellent/Best
- **O(log n)** - Good
- **O(n)** - Fair
- **O(n log n)** - Bad
- **O(n^2)**, **O(2^n)** and **O(n!)** - Horrible/Worst