

# Jobs

From SGD to SLURM : https://github.com/aws/aws-parallelcluster/wiki/Transition-from-SGE-to-SLURM
## Single job


```shell
sbatch cpu_job.sh
```


```shell
sbatch scripts/test_baseline_cpu.sh
```

## Array jobs

```shell
sbatch --array=1-10 cpu_job.sh
```


# Extra 

```shell
srun --partition=cpu-2h --pty bash
```



## Apptainer

`apptainer` is art of container software , that create image , it will be use to create image of the environment

* Create apptainer image container

```shell
apptainer build bbcpy_lightning.sif bbcpy_lightning.def
```

* Test created image 

```shell
apptainer run --nv bbcpy_lightning.sif python -c "import lightning ; import bbcy"
```
## SquashFS

```shell
cd /home/bbci/data/teaching/ALI_MA
cp S1/S1_Session_2.mat  S1_test/
```

```shell
srun --partition=cpu-2h --pty bash
```

```shell
squash-dataset ./S1_test/ ~/MA_BCI/squashfs_smr_data/Subj_1_test.sqfs
```
## MLFlow



```shell
ssh -N -L 5000:localhost:5000 ali_alouane@hydra.ml.tu-berlin.de
```


``

