
# Download data and Preparations

The data can for this tutorial can be downloaded [here](https://www.kaggle.com/datasets/fournierp/captcha-version-2-images?resource=download).

Rename the downloaded ```sample``` folder to ```data_folder``` and create a second folder called ```additional_data```.
Finally move all files starting with a 2 from the ```data_folder``` to the ```additional_data```

```
mv sample data_folder
mkdir additional_data
mv data_folder/2* additional_data

```

# Getting started

## 1. Initialization and linkage type

In the first step we create a new branch, initialize DVC and  add the data.
```
git checkout -b "test"
dvc init
dvc add data_folder
```
This data is now added to the dvc cache (see .dvc/cache). Currently simply as a copy
We now set the linkage type to ```symlink```. Now the files in the folder we added become
symlinks that show to the dvc cache where our original data is stored
```
dvc config cache.type symlink
dvc checkout --relink
```

Lets verify that with

```
ls -l data_folder/
```

Generally, there are three linkaget types that can be used as an argument

|cache.type	|speed	|space	|editable|
|-----------|-------|-------|--------|
|reflink | &check; | &check; | &check; |
|hardlink | &check;  | &check; | -|
|symlink | &check;  | &check; | -|
|copy |  - | - | &check;|


## 1.1 Upload data to remote

If you have a dagshub account and a repo. We can push ```data_folder``` to the repository.
This makes the data available for others.

### 1.1.0 Local remote
Setting up a local remote is straightforward

```
dvc remote add -d local_remote <path-to-remote>
```

### 1.1.1 Dagshub remote
Note that dagshub also provides a git repo, however currently it we only link a github repo to dagshub.
The reason for this is that we can run experiments on clouds through github actions.

To setup the remote use the following

```
dvc remote add dagshub https://dagshub.com/<username>/<repo-name>.dvc
dvc remote modify dagshub --local auth basic
dvc remote modify dagshub --local user <username>
dvc remote modify dagshub --local password <token>
```

and push the data

```
dvc push -r <local_remote or dagshub>
```


## 2. Add meta-data to git history

Next to the cache dvc has created some other files. One of them
is called ```data_folder.dvc```. We can add this to the git history and
with this we can verify if we use the correct data if we were to
reuse this commit.


```
git add data_folder.dvc .dvc/config
git commit -m "Added data to dvc"
```

Optionally we can now push this to the repository to share our progress with collegues
```
git push
```

## 3. Lets add more data

Move more data into data folded and lets ask dvc what has
changed

```
mv additional_data/2* data_folder
dvc status
```

We can not add the data directory again.

```
git add data_folder.dvc
git commit -m "Add all 2* files to dvc"
```
and push the changes again (optional)

```
git push
dvc push -r <local_remote or dagshub>
```

## 4. Checkout everything of v1.0

Lets now go back to the commit ```Added data to dvc``` of the data. Therefore, we go back to a
commit with git and dvc can use the meta data to automatically checkout the
correct data

```
git checkout <commit_hash>
dvc checkout
```

To verify this, lets check if there are any files in the folder data begining with 2

```
ls data_folder | grep 2*
```

No matches should be found.

## 5. Checkout only data but not code
```
git checkout <commit_hash> data_folder.dvc
dvc checkout data_folder.dvc
```