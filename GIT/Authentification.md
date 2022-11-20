### Save auth in cache 
[ref](https://git-scm.com/docs/git-credential-cache)
```shell
git config credential.helper cache
```

Set your git repo 

```shell
git push http://example.com/repo.git
```
put the username & password 
Username: <type your username>
Password: <type your password>

Set a timeout (e.g. 5 min)
```shell
git config credential.helper 'cache --timeout=300'
```

