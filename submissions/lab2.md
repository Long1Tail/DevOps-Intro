# Lab 2 submission

## Task 1.

### 1

```
$ git cat-file -p HEAD   
tree a0ebc8d78ec5cebe0bd415bf9038265fa72a8af1
parent e74f9ee679e546b734846cf659a43188e28b395f
author Long1TaiL <m.shulaev@innopolis.university> 1780906325 +0300
committer Long1TaiL <m.shulaev@innopolis.university> 1780906325 +0300
gpgsig -----BEGIN SSH SIGNATURE-----
 U1NIU0lHAAAAAQAAADMAAAALc3NoLWVkMjU1MTkAAAAgLHMegGfcirLfZ5gTp0dP5aRlNq
 vT3dfDT0vtJN7xsekAAAADZ2l0AAAAAAAAAAZzaGE1MTIAAABTAAAAC3NzaC1lZDI1NTE5
 AAAAQOUFCQ6k/mRLpmKWcPwXupIb9EPwLYcnQN/RgKv7jnS925zDEVDbg6zQU9HtRt1n0k
 jNb/lcQ1hQ9QyY7n/OBQw=
 -----END SSH SIGNATURE-----

docs(lab2): start submission

Signed-off-by: Long1TaiL <m.shulaev@innopolis.university>
```

```
$ git cat-file -p a0ebc8d78ec5cebe0bd415bf9038265fa72a8af1
040000 tree b77c55b65711d03e8add51a686680252046b672e    .github
100644 blob 1c0a1e94b7bbdd951f456cda51af6b8484cc3cee    .gitignore
100644 blob d10c04c6e7e0014f4fe883599c11747c15012d4e    README.md
040000 tree 7d0898a908e274ea809722844cdbd836f3b1c05a    app
040000 tree 6db686e340ecdd318fa43375e26254293371942a    labs
040000 tree 3f11973a71be5915539cb53313149aa319d69cb5    lectures
040000 tree 015b347aa515248532bc54c11dbbd07748aa9fc5    submissions
```

```
$ git cat-file -p b77c55b65711d03e8add51a686680252046b672e
100644 blob 8c55e7441819834ab88d58c7f453c033dd4c6b11    pull_request_template.md
```

```
## Goal
To submit tasks from lab1

## Changes
submissions/lab1.md and a pull request template were added

## Testing

QuickNotes runs locally
git log --show-signature demonstrates verified commits
main branch requires a verification

## Checklist
- [x] Title is a clear sentence (≤ 70 chars)
- [x] Commits are signed (`git log --show-signature`)
- [x] `submissions/labN.md` updated
```

### 2

```
ls -la .git/ 
итого 56
drwxr-xr-x  7 long1tail long1tail 4096 июн  8 11:12 .
drwxr-xr-x  8 long1tail long1tail 4096 июн  8 11:10 ..
-rw-r--r--  1 long1tail long1tail   88 июн  8 11:12 COMMIT_EDITMSG
-rw-r--r--  1 long1tail long1tail  575 июн  8 11:08 config
-rw-r--r--  1 long1tail long1tail   73 июн  5 11:06 description
-rw-r--r--  1 long1tail long1tail    0 июн  5 11:28 FETCH_HEAD
-rw-r--r--  1 long1tail long1tail   29 июн  8 11:08 HEAD
drwxr-xr-x  2 long1tail long1tail 4096 июн  5 11:06 hooks
-rw-r--r--  1 long1tail long1tail 3307 июн  8 11:12 index
drwxr-xr-x  2 long1tail long1tail 4096 июн  5 11:06 info
drwxr-xr-x  3 long1tail long1tail 4096 июн  5 11:06 logs
drwxr-xr-x 54 long1tail long1tail 4096 июн  8 11:12 objects
-rw-r--r--  1 long1tail long1tail   41 июн  5 11:40 ORIG_HEAD
-rw-r--r--  1 long1tail long1tail  112 июн  5 11:06 packed-refs
drwxr-xr-x  5 long1tail long1tail 4096 июн  5 11:06 refs
```

This is the core of git. Here is stored history, settings and additional data

```
cat .git/HEAD
ref: refs/heads/feature/lab2
```

This is the HEAD pointer. It points to my current state. Also, as .git/HEAD points not to some hash, it means that I am on some branch

```
ls .git/refs/heads/ 
feature  main
```

This is containment of /heads. Branches are stored here. In my repository there are 3 branches (yet). As "feature/lab1" and 'feature/lab2" contains in naming '/' symbol, git creates directory "feature", were 'lab1' and 'lab2' stored. It can be checked by running `ls .git/refs/heads/feature`

```
ls .git/objects/ | head
01
09
0c
0e
15
17
1a
1b
1c
1d
```

In these directories commits are stored. each directory named after first 2 bytes of SHA-hash of commits, stored in them. e.g: in directory ab would be stored abcd.... and abde.... commits

```
find .git/objects -type f | wc -l
56
```

This command shows, how many git objects were found

### 3

```
$ git reflog              
14e9283 (HEAD -> feature/lab2) HEAD@{0}: reset: moving to 14e9283
f6508f8 (origin/feature/lab2) HEAD@{1}: reset: moving to f6508f8
aa8dd05 HEAD@{2}: reset: moving to aa8dd05
f6508f8 (origin/feature/lab2) HEAD@{3}: reset: moving to f6508f8
f6508f8 (origin/feature/lab2) HEAD@{4}: reset: moving to HEAD~2
14e9283 (HEAD -> feature/lab2) HEAD@{5}: commit: wip(lab2): more progress
aa8dd05 HEAD@{6}: commit: wip(lab2): start
f6508f8 (origin/feature/lab2) HEAD@{7}: commit: docs(lab2): added 1.1, 1.2 tasks. Commit before execution of task 1.3
7722946 HEAD@{8}: commit: docs(lab2): start submission
e74f9ee (origin/main, origin/HEAD, main) HEAD@{9}: checkout: moving from main to feature/lab2
e74f9ee (origin/main, origin/HEAD, main) HEAD@{10}: commit: docs(lab1): pull request template completed
82d4bf6 HEAD@{11}: checkout: moving from feature/lab1 to main
```

```
$ git reset --hard 14e9283
Указатель HEAD сейчас на коммите 14e9283 wip(lab2): more progress
```

If `git gc` (garbage collector) were run before I restore my commits, information, stored in them would be lost and even git reset --hard <SHA> won't help

## Task 2.

Before:
```
git log --oneline --graph
* f6508f8 (HEAD -> feature/lab2) docs(lab2): added 1.1, 1.2 tasks. Commit before execution of task 1.3
* 7722946 docs(lab2): start submission
* e74f9ee (tag: v0.1.0-lab2-long1tail, upstream/main, upstream/HEAD) docs(lab1): pull request template completed
* 82d4bf6 test: unsigned commit (should fail)
* 492d4ec docs: add PR template
* 4b3d0b9 docs: add PR template
* 66bbd4d docs(lab1): align Task 3 GitHub Community engagement with other courses
```

After:
```
git log --oneline --graph
* c2aa5f9 (HEAD -> feature/lab2, origin/feature/lab2) docs(lab2): added 1.1, 1.2 tasks. Commit before execution of task 1.3
* a066fde docs(lab2): start submission
* 29bcd5e (origin/main, origin/HEAD, main) docs: upstream moved while you worked
* e74f9ee (tag: v0.1.0-lab2-long1tail, upstream/main, upstream/HEAD) docs(lab1): pull request template completed
* 82d4bf6 test: unsigned commit (should fail)
* 492d4ec docs: add PR template
* 4b3d0b9 docs: add PR template
* 66bbd4d docs(lab1): align Task 3 GitHub Community engagement with other courses
```

Merge is good for public branches. It saves complex, non-linear history.
Rebase creates clean, linear history and perfect for private branches
