# Ubuntu Git

## Install 

1. Install Git

`sudo apt install  git`

2. Checkout version

`git --version`

## Config

1. `git config --global user.name "Imperater"`

2. `git config --global user.email "bing.peng@lifesense.com"

## SSH

1. check that SSH already exists.

``` shell
cd ~/.ssh
```

2. Otherwise, create.

```shelll
ssh-keygen -t rsa -C "bing.peng@lifesense.com"
```

3. get file content information

``` shell
cat ~/.ssh/id_ras.pub
```
