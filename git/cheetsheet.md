# Git cheat sheet

## Submodules

### setup

```
git submodule add <giturl> path/to/submodule
```

### init and update submodules

```
git submodule update --init --recursive
```

### remove

used to be complicated, but with recent git, the following is enough

```
git rm path/to/submodule
```
