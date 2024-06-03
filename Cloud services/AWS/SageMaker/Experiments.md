#dataengineering #aws #experiment

The goal of SageMaker Experiments is to make it as simple as possible to create experiments, populate them with trials, add tracking and lineage information, and run analytics across trials and experiments.

When discussing SageMaker Experiments, we refer to the following concepts:

-   **Experiment** – A collection of related trials. You add trials to an experiment that you want to compare together.
-   **Trial** – A description of a multi-step ML workflow. Each step in the workflow is described by a trial component.
-   **Trial component** – A description of a single step in an ML workflow, such as data cleaning, feature extraction, model training, or model evaluation.
-   **Tracker** – A Python context manager for logging information about a single trial component (for example, parameters, metrics, or artifacts).

![](../../../figures/SageMaker%20Experiments.png)