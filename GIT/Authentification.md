#git

### Save auth in cache 
[ref](https://git-scm.com/docs/git-credential-cache)

```shell
git config credential.helper cache
```

+ Set your git repo and then put the username & password 

```shell
git push http://example.com/repo.git
```

+ Set a timeout (e.g. 5 min)

``` shell
git config credential.helper 'cache --timeout=300'
```

