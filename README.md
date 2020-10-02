# New Machine Setup

This is the fastest way I know how to get a new Ubuntu workstation up and runnning for **personal**
use, according to my preferences.

1. Install Ubuntu 20.04 using a [USB boot drive](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview).
2. When given the option, **DO NOT** install 3rd party software. 

    ![](https://ubuntucommunity.s3.dualstack.us-east-2.amazonaws.com/original/2X/3/32da76fd45eb5300065df0491ec85a0ab3e6e380.png)

    There is an ubuntu bug that causes installation to fail when an NVIDIA device is on board. We will install drivers next from the Lambda stack.

3. Install the [Lambda stack](https://lambdalabs.com/lambda-stack-deep-learning-software).
```bash
LAMBDA_REPO=$(mktemp) && \
wget -O${LAMBDA_REPO} https://lambdalabs.com/static/misc/lambda-stack-repo.deb && \
sudo dpkg -i ${LAMBDA_REPO} && rm -f ${LAMBDA_REPO} && \
sudo apt-get update && sudo apt-get install -y lambda-stack-cuda
```
    You may have to agree to terms and conditions from NVIDIA.

4. Reboot.
```bash
sudo reboot
```

5. Open a terminal and install desired software
   1. [Make Caps-lock act as Control](https://askubuntu.com/questions/33774/how-do-i-remap-the-caps-lock-and-ctrl-keys):
   ```bash
   sudo apt-get install gnome-tweaks
   ```
   and use the Tweaks software to configure the keyboard as desired. Also, turn on "Emacs input."
   
   Alternatively, run
   ```bash
   setxkbmap -layout us -option ctrl:nocaps
   ```
   without having to install software.
   
   2. Set up the shell.
   ```bash
   sudo apt install zsh
   sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   ```
   
   3. Install github command line:
   ```bash 
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0
   sudo apt-add-repository https://cli.github.com/packages
   sudo apt update
   sudo apt install gh
   ```
