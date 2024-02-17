Hello everyone, this is a remake of all the guides out there.

### Prerequisites
- Use Ubuntu or [WSL2](https://github.com/eduardovicente/compilingEvoGsi/tree/main#using-wsl)
- Good laptop (12 cores and 16gb RAM at least) and patience (This is my experience)

In other words

1. If you're going with Ubuntu via dual boot, main OS or VM go directly to [Steps for Begginers](https://github.com/eduardovicente/compilingEvoGsi/tree/main#Steps-for-begginers)

2. If you're using windows and would like to try WSL2, go [Using WSL](https://github.com/eduardovicente/compilingEvoGsi/tree/main#using-wsl)

### Using WSL
1. Open powershell window with Admin privileges.
2. Run below command.
```bash
wsl --install
```
3. After done, reboot your laptop
***Note:*** by default Ubuntu LTS is installed, this is what we need

## Steps for begginers
### 0. Before you start, update and upgrade your ubuntu/ubuntu wsl
```bash
 sudo apt update && apt upgrade
```

### 1. Install SDK
*In your HOME directory*
```bash
sudo apt install unzip
mkdir platformtools
cd platformtools/
wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip
unzip platform-tools-latest-linux.zip -d ~
```
Now we have to add `adb` and `fastboot` to our path. Open ~/.profile (```nano  ~/.profile```) and add the following:
```
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
```
Then, run this to update your environment. ```source ~/.profile```

### 2. Install build packages
```
sudo apt-get install bc bison build-essential curl flex g++-multilib gcc-multilib git gnupg gperf libxml2 -y
sudo apt-get install lib32z1-dev liblz4-tool libncurses5-dev libsdl1.2-dev libwxgtk3.0-gtk3-dev imagemagick git -y
sudo apt-get install lunzip lzop schedtool squashfs-tools xsltproc zip zlib1g-dev openjdk-8-jdk python-is-python3 perl -y
sudo apt-get install xmlstarlet virtualenv xz-utils rr jq libncurses5 pngcrush lib32ncurses5-dev git-lfs libxml2 -y
sudo apt-get install openjdk-11-jdk-headless -y
sudo apt-get install ccache -y
```
### 3. Install the repo command

Enter the following to download the repo binary and make it executable (runnable):

```
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

Put the ~/bin directory in your path of execution
In recent versions of Ubuntu, ~/bin should already be in your PATH. You can check this by opening ~/.profile with a text editor and verifying the following code exists (add it if it is missing):

#### How to review this?
``` bash
nano  ~/.profile
```
Verify that below lines exists otherwise add them.
```
# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```
Then, use this to update your environment.

```source ~/.profile```

### 4. Configure git

You’ll need to set up your git identity in order to sync the source, run these commands:

```
git config --global user.name "your username"
git config --global user.email yourmail@example.com
```

### 5. Turn on caching to speed up build

You can speed up subsequent builds by adding these lines to your ~/.bashrc OR ~/.zshrc file:

```
export USE_CCACHE=1
export CCACHE_COMPRESS=1
export CCACHE_MAXSIZE=50G # 50 GB
```