#git

```
git submodule update --init --recursive
```

```
git submodule update --recursive
```

```
git pull && git submodule init && git submodule update && git submodule status
```


## First time

Clone and Init Submodule

```
git clone git@github.com:speedovation/kiwi-resources.git resources
git submodule init
```
## Rest

During development just pull and update submodule
```
git pull --recurse-submodules  && git submodule update --recursive
```
## Update Git submodule to latest commit on origin
```
git submodule foreach git pull origin master
```
### Preferred way should be below
```
git submodule update --remote --merge
```
