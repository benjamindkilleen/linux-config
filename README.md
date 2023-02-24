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

   2. Set up the shell with zsh.

   ```bash
   sudo apt install zsh
   ```

   and oh-my-zsh

   ```
   sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   ```

   Log out and log back in.

6. Install Brave browser and add the device to my sync chain.

   ```bash
   sudo apt install apt-transport-https curl

    curl -s https://brave-browser-apt-release.s3.brave.com/brave-core.asc | sudo apt-key --keyring /etc/apt/trusted.gpg.d/brave-browser-release.gpg add -

    echo "deb [arch=amd64] https://brave-browser-apt-release.s3.brave.com/ stable main" | sudo tee /etc/apt/sources.list.d/brave-browser-release.list

    sudo apt update

    sudo apt install brave-browser
   ```

7. Install github command line:

   ```bash
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0
   sudo apt-add-repository https://cli.github.com/packages
   sudo apt update
   sudo apt install gh
   ```

   And authenticate yourself with:

   ```
   gh auth login
   ```

   [Generate ssh key](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

   ```
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

   and [add it to Github](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

   ```
   sudo apt-get install xclip
   xclip -sel clip < ~/.ssh/id_rsa.pub
   ```

   You will also need to tell github who you are:

   ```bash
   git config --global user.email "you@example.com"
   git config --global user.name "Your Name"
   ```

8. Download [powerlevel10k theme](https://github.com/romkatv/powerlevel10k).
   ```bash
   git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
   ```
   We will set the theme after downloading and linking config files.
9. Download [conda-zsh-completion](https://github.com/esc/conda-zsh-completion)

   ```
   git clone --depth=1 https://github.com/esc/conda-zsh-completion.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/conda-zsh-completion
   ```

   This plugin is loaded by the .zshrc

10. Download Emacs. TODO: update for doom emacs.

11. Clone and link config files

    ```bash
    cd
    git clone git@github.com:benjamindkilleen/linux-config.git
    for FILE in $HOME/linux-config/home/.*
    do
        f="$(basename -- $FILE)"
        echo "Linking $FILE -> $(pwd)/$f"
        rm $f
        ln -s $FILE $f
    done

    cd .config
    for FILE in $HOME/linux-config/.config/*
    do
        f="$(basename -- $FILE)"
        echo "Linking $FILE -> $(pwd)/$f"
        rm $f
        ln -s $FILE $f
    done
    ```

    And open a new terminal window. You will be prompted with the `powerlevel10k` config options.

12. Install byobu. Useful for running processes in the background, and better than tmux.

    ```
    sudo apt-get install byobu
    ```

13. Change terminal shortcuts to stop zoom with `Ctrl+-` behavior. Make it `Ctrl+_`

14. Install [Anaconda](https://www.anaconda.com/products/individual) for Linux / Python 3.8.

    ```
    cd ~/Downloads
    wget https://repo.anaconda.com/archive/Anaconda3-2020.07-Linux-x86_64.sh
    sh Anaconda3-2020.07-Linux-x86_64.sh
    cd
    ```

    (Link may not be up to date.)

    Hopefully the part in `home/.zshrc`, which is now soft-linked to your home directory, matches
    what anaconda writes to .bashrc automatically, but you can check to make sure.

15. Other random pip insalls that I like:

    ```
    python3 -m pip install -U pip
    pip install gpustat
    ```

16. Allow incoming ssh connections:

    ```
    sudo apt update
    sudo apt install openssh-server
    ```

    and, if necessary, disable the firewall for ssh

    ```
    sudo ufw allow ssh
    ```

    If you like, you can add the workstation's public key

    - Generate the key if you don't have one on your remote machine (i.e. a laptop, not the new ubuntu install):
      ```
      ssh-keygen -t rsa
      ```
    - Make ssh dir.
      ```
      ssh user@workstation.address mkdir -p .ssh
      ```

17. Set up X-forwarding

Using [this](https://unix.stackexchange.com/questions/12755/how-to-forward-x-over-ssh-to-run-graphics-applications-remotely) answer.

On the ubuntu machine, make sure `/etc/ssh/sshd_config` contains

```
X11Forwarding yes
X11DisplayOffset 10
```

most likely the second line is commented out.

To adopt these changes, run:

```
cat /var/run/sshd.pid | xargs kill -1
```

12. Install Docker with GPU:
    ```
    sudo apt-get install docker.io nvidia-container-toolkit
    ```

### Other Items

- Reliable remote desktop: https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-20-04
