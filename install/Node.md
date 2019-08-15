# Ubuntu Node.js

1. Install 

``` shell 
`sudo apt-get update`
`sudo apt-get install -y python-software-properties software-properties-common`
`sudo add-apt-repository ppa:chris-lea/node.js`
`sudo apt-get update`
```

2. Install Node.js

``` shell
`sudo apt-get install nodejs`
`sudo apt install nodejs-legacy`
`sudo apt install npm`
```

3. Update npmm repository.

``` shell
`sudo npm config set registry https://registry.npm.taobao.org`
`sudo npm config list`
```

4. Global installation of n Manager(for managing nodejs versions)

``` shell
`sudo npm install n -g`
```

5. Install the latest nodejs and check the node version.

``` shell
`sudo n stable`
`sudo node -v`
```