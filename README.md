<p align="center">
  <img src="https://avatars.githubusercontent.com/u/66854612?s=200&v=4">
</p>

### How to Build?
You can always refer to [Git and Repo](https://source.android.com/source/using-repo.html) as well as [How to build a GSI](https://github.com/phhusson/treble_experimentations/wiki/How-to-build-a-GSI%3F).

As a newbie building GSI ROMS.

1. I will be leaving you my own recap of [How to build a GSI]
<details>
  <summary>Beginners guide</summary>

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
</details>
   
2. I will also be recording my [build times](https://github.com/eduardovicente/compilingEvoGsi/blob/main/README.md#using-same-internet-bandwidth-speed---100mbs) for you guys to get an estimate.
   
<details>
  <summary>My experience building, my setup and timings</summary>
  
  ## ***Using same internet bandwidth speed - 100mbs***
  
  ### Lenovo legion y530 laptop, i7 8th gen, 32gb ram ddr4 (2666mhz), nvme 2.0
  - Jobs: 16
  - Repo Sync time : 2:10 hrs
  - Build time : 5:50 hrs
    
  ### Lenovo legion 5 Pro laptop, i7 12th gen, 20gb ram ddr5 (4800mhz), nvme 3.0
  - Jobs: 20
  - Repo Sync time : 2:10 hrs
  - Build time : 2:05 hrs
    
</details>


### Once you did above / have your setup prepared

## Create Directories
As a first step, you'll have to create and enter a folder with the appropriate name.
To do that, run these commands:

```bash
mkdir evo
cd evo
```

## Initalise the Treble Evolution-X repo
```bash
repo init -u https://github.com/Evolution-X/manifest -b udc
```

## Clone the Manifest
This adds necessary dependencies for the Evolution-X GSI.
```bash
git clone https://github.com/eduardovicente/treble_manifest.git -b evo-udc .repo/local_manifests
```

## Sync the repository
```bash
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
```
If you see any issue here let it do his work, after its done, run it again, you should get no errors

### Apply the patches
Copy the patches folder to the ROM folder, and run this in the ROM folder:
```bash
patches/apply-patches.sh . trebledroid
patches/apply-patches.sh . personal
patches/apply-patches.sh . ponces
```
#### NOTE: It is recommended to apply patches manually by going to the respective directory of the patches, and applying them by using `git am`, as the script often skips applying critical patches for some reason.

### ***If you came from Legion y700 2022 forum, apply below one as well.***
```bash
patches/apply-patches.sh . vicente
```

### ***If you're just building a GSI***
Clone this repository and then copy evo.mk to device/phh/treble in the ROM folder. 

### Then run the following commands:
```bash
 cd device/phh/treble
 bash generate.sh evo
```

### Turn On Caching
You can speed up subsequent builds by adding these lines to your `~/.bashrc` OR `~/.zshrc` file:

```bash
export USE_CCACHE=1
export CCACHE_COMPRESS=1
export CCACHE_MAXSIZE=50G # 50 GB
```

## Compilation 
In the ROM folder, run this to start compilation:

```bash
source build/envsetup.sh
ccache -M 50G -F 0
lunch treble_arm64_bgN-userdebug 
make systemimage -j$(nproc --all)
```

## Compression
After compiling the GSI, you can run this to reduce the `system.img` file size:
> Warning<br>
> You will need to decompress the output file to flash the `system.img`. In other words, you cannot flash this file directly.

```bash
cd out/target/product/tdgsi_arm64_ab
xz -9 -T0 -v -z system.img 
```

## Troubleshooting
If you face any conflicts while applying patches, apply the patch manually.

## Credits
These people have helped this project in some way or another, so they should be the ones who receive all the credit:
- [Ahnet](https://github.com/ahnet-69) ***Was the only one open to resolve questions! huge thank you!***
- [Evolution-X Team](https://github.com/Evolution-X)
- [Phhusson](https://github.com/phhusson)
- [AndyYan](https://github.com/AndyCGYan)
- [Ponces](https://github.com/ponces)
- [Peter Cai](https://github.com/PeterCxy)
- [Iceows](https://github.com/Iceows)
- [ChonDoit](https://github.com/ChonDoit)
- [Nazim](https://github.com/naz664)
- [UniversalX](https://github.com/orgs/UniversalX-devs/)
- [TQMatvey](https://github.com/TQMatvey)
