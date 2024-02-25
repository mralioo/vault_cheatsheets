

# Probe method: A reliable feature selection technique in ML.

![](../figures/Pasted%20image%2020231115095140.png)



- Step 1) Add a random feature (noise).
- Step 2) Train a model on the new dataset.
- Step 3) Measure feature importance.
- Step 4) Discard original features that rank below the random feature.
- Step 5) Repeat until convergence.


If a feature’s importance is ranked below a random (noise) feature, it is possibly a useless feature for the model.


