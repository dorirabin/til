# Three way merging

## Background

```
# Setup
git init
# 1, 2, 3 (master)
echo -n "1\n2\n3\n" > test.txt
git add test.txt
git commit -m "commit #1"

# => 1, a, 3 (a)
git checkout -b a
sed -i '' 's/2/a/g' test.txt
git add test.txt
git commit -m "commit #2"

# => 1, 2, 3, 4 (master)
git checkout master
echo -n "4\n" >> test.txt
git add test.txt
git commit -m "commit #3"

# => 1, b, 3, 4 (b)
git checkout -b b
sed -i '' 's/2/b/g' test.txt
git add test.txt
git commit -m "commit #4"

# Merge b to master
# => 1, b, 3, 4 (master)
git checkout master
git merge b
```

## Scenarios

### Scenario 1: Merge a to master

```
git checkout master
git merge a
git mergetool
```

A (Base)
```
1
2
3
```

B (Local)
```
1
b
3
4
```

C (Remote)
```
1
a
3
```

### Scenario 2: Merge master to a

```
git checkout a
git merge master
git mergetool
```

A (Base)
```
1
2
3
```

B (Local)
```
1
a
3
```

C (Remote)
```
1
b
3
4
```

### Scenario 3: Rebase a onto master

```
git checkout a
git rebase master
git mergetool
```

A (Base)
```
1
2
3
```

B (Local)
```
1
b
3
4
```

C (Remote)
```
1
a
3
```

## Wrap up

Base always refers to the common ancestor (i.e. commit #1).

Local and Remote may change according to the destination and action (Merge / Rebase).

