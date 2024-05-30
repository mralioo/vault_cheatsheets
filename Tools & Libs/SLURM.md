#os
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
squeue -u ali_alouane -t RUNNING
```

* List detailed information for a job (useful for troubleshooting)

```shell
scontrol show jobid -dd JOBID
```

```shell
sstat --format=AveCPU,AvePages,AveRSS,AveVMSize,JobID -j JOBID --allsteps
```


* Get priority of a job
```shell
sprio -j JOBID
```

+ Delete job

```shell
scancel JOBID
```

 All jobs
```shell
scancel -u USERNAME
```
## Apptainer

`apptainer` is art of container software , that create image , it will be use to create image of the environment

* Create apptainer image container

```shell
apptainer build bbcpy_env_1.sif new.def
```

* Test created image on cpu

```shell
srun --partition=cpu-2h --pty bash
```

```shell
apptainer run --nv bbcpy_lightning_v5.sif python -c "import lightning ; import bbcpy; import torcheeg; import lightning; print(torch.cuda.device_count()); print(torcheeg.__version__); print(lightning.__version__)"
```

```shell
apptainer run --nv bbcpy_lightning_v5.sif python -c "import torch;from torchsummary import summary; from torcheeg.models.cnn import EEGNet, TSCeption; model=EEGNet(); summary(model, (1, 60, 151))"
```


* Test created image on gpu 

```shell
srun --partition=gpu-test --gpus=1 --pty bash
```

```shell
apptainer run --nv bbcpy_en.sif python -c "import mlflow; print(mlflow.__version__)"
```

```Shell
apptainer run --nv bbcpy_env.sif python -c "import sqlalchemy; print(sqlalchemy.__version__)"

```

1
2.0.5
2.0.1+cu117
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


* Launch MLflow UI on remote server with port forwarding


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

```
mlflow ui --gunicorn-opts "--workers 4"

```


``
* Check if port is used 

```shell
netstat -tuln | grep <mlflow_port>
```


```shell
\\sshfs\ali_alouane@hydra.ml.tu-berlin.de!22\home\ali_alouane\MA_BCI\bbcpy_AutoML\logs

```



* Copy folder with scp   

```shell

scp -r ali_alouane@hydra.ml.tu-berlin.de:~/MA_BCI/bbcpy_AutoML/logs/mlflow/ Desktop/MA/

```

```shell

sshfs ali_alouane@hydra.ml.tu-berlin.de:/home/ali_alouane/MA_BCI/bbcpy_AutoML/logs/mlflow ./

```

```shell

mlflow server --default-artifact-root sftp://ali_alouane@hydra.ml.tu-berlin.de:/home/ali_alouane/MA_BCI/bbcpy_AutoML/logs/mlflow/mlruns 

```

```shell
mlflow server --backend-store-uri sqlite:///mlflow.db --default-artifact-root ./artifacts --host 0.0.0.0
```


```shell

ssh -L 5008:localhost:5008 -o ServerAliveInterval=60 ali_alouane@hydra.ml.tu-berlin.de

```

* postgres
```shell
ssh -N -f -L localhost:5433:localhost:5432 -o ServerAliveInterval=60 ali_alouane@hydra.ml.tu-berlin.de
```

* Delete all process that use port

```shell
lsof -i :8009 | awk 'NR>1 {print $2}' | xargs kill -9
```

* list all process that uses port

```shell
lsof -i :PORT_NUMBER
```

```shell
lsof -i :8000-8099
```


```shell
optuna-dashboard postgresql://postgres:Fida2002@localhost:5432/optuna
```


```shell
optuna-dashboard sqlite:///db.sqlite3
```


sacct

sinfo

```shell
sbatch --nodelist=head035 scripts/debug_hpo_tsception_2D.sh

```

```shell
 sbatch --nodelist=head040 scripts/tsception_2D.sh
```


```shell
pkill -u username
```


```shell
mlflow ui --backend-store-uri file:///path/to/your/mlruns/directory
```