# Conda 

```shell 
conda install --file requirements.txt
```


```shell
pip install -r requirements.txt
```

### add your Conda environment to your jupyter


```shell
conda create --name myenv
conda install -c anaconda ipykernel
python -m ipykernel install --user --name=myenv
```


# Virtualenv

```
python -m venv venv
```

```
venv\Scripts\activate
```

```
deactivate
```

	source ven/bin/activate