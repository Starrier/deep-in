# Ubuntu VS Code

## Install Ubuntu make through offical PPA

1. `sudo add-apt-repository ppa:ubuntu-desktop/ubuntu-make`

2. `sudo apt-get update`

3. `sudo apt-get install ubuntu-make`

## Install VS Code using the command.

1. `umake ide visual-studio-code`, add input `a` will be confirmed in the middle.

**note**:

If the installation is complete,the icon does not appear,indicating the installation error.

- open the terminal
- dconf reset -f /org/compiz/
- setsid unity
- unity --reset-icons
- sudo shutdown -r now
