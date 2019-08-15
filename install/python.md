# Ubuntu Python

**Note**：

**Ubuntu 16.03 + has own default python.Do not try to uninstall them.**

## Install Python 3.6 version

1. `sudo add-apt-repository ppa:jonathonf/python-3.6`

2.  Enter

3.  `sudo apt-get update`

4. `sudo apt-get install python3.6`

5.  Adjust the priority of Python3 os that it has a higher priority of 3.6

``` shell
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2
```

Change the default value,Python2 ,default is now  modified to Python3.

```shell
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150
```

6. check the python version

``` python
python
```