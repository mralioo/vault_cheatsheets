#os #linux #python 
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

```python
pip3 freeze > requirements.txt
```

## Add env to ipykernel 

https://queirozf.com/entries/jupyter-kernels-how-to-add-change-remove

```shell
pip install ipykernel
```

```shell
ipython kernel install --name "local-venv" --user
```


```shell
jupyter kernelspec uninstall envname
```