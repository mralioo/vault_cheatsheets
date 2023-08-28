

# Jobs

From SGD to SLURM : https://github.com/aws/aws-parallelcluster/wiki/Transition-from-SGE-to-SLURM

[SLURM CHEATING CARD](https://git.tu-berlin.de/ml-group/hydra/documentation/-/blob/main/cheating-card.md)
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


* Interactive session

```shell
srun --partition=cpu-2h --pty bash
```

* List all running jobs for a user
```shell
squeue -u USERNAME -t RUNNING
```

* List detailed information for a job (useful for troubleshooting)

```shell
scontrol show jobid -dd JOBID
```


* Get priority of a job
```shell
sprio -j JOBID
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


launch MLflow UI on remote server with port forwarding


```shell
ssh -L 5009:localhost:5009 ali_alouane@hydra.ml.tu-berlin.de "sh activate_env.sh; conda activate bbcpy_automl;mlflow ui --backend-store-uri file:///$HOME/MA_BCI/bbcpy_AutoML/logs/mlflow/ --port 5009"
```

~/MA_BCI/bbcpy_AutoML/logs/mlflow

--backend-store-uri file:///$HOME/MA_BCI/bbcpy_AutoML/logs/mlflow/

```shell
ssh -N -L 5000:localhost:5000 ali_alouane@hydra.ml.tu-berlin.de
```


```shell
mlflow ui --port 5005 
```

``
* Check if port is used 

```shell
netstat -tuln | grep <mlflow_port>
```