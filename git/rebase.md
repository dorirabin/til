# Rebase

Master

```
C1 - C2 - C3 - C4 - C5
```

Feature

```
C1 - C2 - C3 - F1 - F2
```

Command

```shell
git checkout feature
git rebase master
```

Steps in rebase

1. Find the common ancestor of feature and master branch (i.e C3).
2. Create patch for commits after common ancestor in feature branch (i.e. F1, F2).
3. Apply patch (i.e. F1', F2') to master branch

Rebased feature branch

```
C1 - C2 - C3 - C4 - C5 - F1' - F2'
```

Merge back to master

```
git checkout master
git merge feature
```

Reproduce

```
mkdir git_rebase
cd git_rebase
git init
touch test.txt

echo C1 >> test.txt
git add .
git commit -m "C1"

echo C2 >> test.txt
git commit -am "C2"

echo C3 >> test.txt
git commit -am "C3"

git checkout -b feature

echo F1 >> test.txt
git commit -am "F1"

echo F2 >> test.txt
git commit -am "F2"

git checkout master

echo C4 >> test.txt
git commit -am "C4"

echo C5 >> test.txt
git commit -am "C5"

git checkout feature
git rebase master # need to resolve conflict

git checkout master
git merge feature
```