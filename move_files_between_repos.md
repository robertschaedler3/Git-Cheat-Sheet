# Move files between repos

Typically when moving files between repositories the git history of the files will be lost and we won't be able to trace it back anymore. This is bad practice and should be avoided.

## Preparing files to move 

### Step 1:
Make a copy of `REPO_A` as the following steps make major changes to this copy which **you should not push**! 
These changes are meant to isolate the target files to be copied/moved.

```bash
mkdir cloneA
cd cloneA

git clone --branch <branch> --origin origin --progress -v <git REPO_A url>
# makes a copy of 'REPO_A' 

cd <repo_A directory>
 ```
 
 ### Step 2: 
 Remove the connection to the original repository to avoid unwanted changes.
 
 ```bash
 git remote rm origin
 ```
 
 ### Step 3:
 Remove all folders/files that you do not want to copy/move. This will result in the contents of `FOLDER_TO_KEEP` being put into the base of `REPO_A`. 
 
 ```bash
 git filter-branch --subdirectory-filter <directory> -- --all
 # eg. git filter-branch --subdirectory-filter path/to/folder/FOLDER_TO_KEEP -- --all
 ```
 
 ### Step 4: 
 Clean up old/unwanted data.
 
 ```bash
 git reset --hard
 git gc --aggressive 
 git prune
 git clean -fd
 ```
### Step 5 (optional):
*If you want to copy the files within the folder rather than a folder directly you can skip this step.*
Move everything that you preserved into a folder `FOLDER_NAME` that you want to move to `REPO_B`.

```bash
mkdir <FOLDER_NAME>
mv * <FOLDER_NAME>
```

### Step 6:
Add and commit the changes.
*Note: this will not effect the original repository since the remote connection was removed*

```bash
git add .
git commit
```

## Merge the files into a different repository

### Step 1:
Make a copy/navigate to `REPO_B`.

```bash
mkdir cloneB
cd cloneB

git clone <git REPO_B url>
cd <git REPO_B directory>
```

### Step 2:
Create the remote connection to `REPO_A` as a new branch in `REPO_B`.

```bash
git remote add <TEMP_BRANCH> <path/to/REPO_A>
```

### Step 3:
Pull file/history from `TEMP_BRANCH` into `REPO_B`.
*Note: At this point TEMP_BRANCH should only contain the directory you are moving to REPO_B`

```bash
git pull <TEMP_BRANCH> master --allow-unrelated-histories
```

### Step 4:
Remove the remote connection to `REPO_A`

```bash
git remote rm <REPO_A>
```
### Step 5:
Push changes.

```bash
git push
```

*After changes are pushed the copy of `REPO_A` can be safely deleted.*
