Ubuntu JDK

## Install JDK using ppa.

1. `sudo add-apt-repository ppa:webupd8team/java`, `suod apt-get update`

2. `sudo apt-get install oracle-java8-installer`

3. set system default jdk.

``` shell
sudo update-java-alternatives -s java-8-oracle
```

4. check the jdk version

``` shell
java -version
```