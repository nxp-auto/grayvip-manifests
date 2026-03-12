> **Note**
> All the steps described below have been run and validated on Ubuntu 20.04 LTS (native or through a virtual machine). It is then recommended to install Ubuntu 20.04 LTS before going through the following sections.

# Installing the dependencies for Linux build

To get and build the GrayVIP Linux image, the `repo` utility needs to be
installed along its dependencies. This only needs to be done once.

- Update the package manager:
  ```bash
  sudo apt-get update
  ```
- Install the `repo` tool dependencies:
    - **python 3.6** or newer
      ```bash
      sudo apt-get install python3
      ```
    - **git 1.8.3** or newer
      ```bash
      sudo apt-get install git
      ```
    - **curl**
      ```bash
      sudo apt-get install curl
      ```
- Install the `repo` utility:
  ```bash
  mkdir ~/bin
  curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
  chmod a+x ~/bin/repo
  PATH=${PATH}:~/bin
  ```

- Configure the git environment (this step can be skipped if the git environment already configured):
  ```bash
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
  ```

---
# Building the GrayVIP Linux images

## Download the Yocto Project Environment into your machine

```bash
mkdir ~/grayvip_yocto
cd ~/grayvip_yocto
repo init -u https://github.com/nxp-auto/grayvip-manifests -b release/GrayVIP-1.0.6.0 -m default.xml
repo sync
```

> **Note:** For a specific GrayVIP release or engineering build, please use the proper branch (`-b <branch>`) and manifest file (`-m <manifest>`).

> **Note:** To fetch internal development repositories use Bitbucket URL:
```bash
repo init -u ssh://git@bitbucket.sw.nxp.com/grayvip/grayvip-manifests.git -b develop -m default-bitbucket.xml
```

## Preparing Your Environment

Install all the prerequisites before starting the Yocto build (first time only):
```bash
./sources/meta-alb/scripts/host-prepare.sh
```

## Build the Cohort 6 image

### Setting up the Yocto Build Directory

Create a build directory in the root directory by running:
```bash
cd ~/grayvip_yocto
source nxp-setup-alb.sh -D fsl-auto -m s32n79rdb -p hw-partition-1.1.6
```

### Building the Linux Images

Start the build process:
```bash
bitbake fsl-image-base
```

Once the build is finalized, the artifacts will be present in `tmp/deploy/images`.

## Build the Cohort 5 image

### Setting up the Yocto Build Directory
Create a build directory in the root directory by running:
```bash
cd ~/grayvip_yocto
source nxp-setup-alb.sh -D fsl-grayvip -m s32n79rdb -p hw-partition-1.1.5
```

### Configure the eIQ Auto demos

> **Note:** Skip this step if `meta-grayvip-dev` layer is included in the Yocto Project Environment.

Download the eIQ Auto tarball (e.g., `eIQAuto_S32N7_PRM_x.xx.x.tar.gz`) and append the
following line to the `conf/local.conf` file:
```
EIQA_RUNTIME_PREBUILT_TAR_PATH = "<the path to eIQAuto_S32N7_PRM_x.xx.x.tar.gz>"
```

### Configure the CARV Linux demos

> **Note:** Skip this step if `meta-grayvip-dev` layer is included in the Yocto Project Environment.

Download the CARV Linux Library tarball (e.g., `CARV_Linux_Library_x.x.x.tar.gz`) and append the
following line to the `conf/local.conf` file:

```
CARV_LINUX_LIBRARY_TAR_PATH = "<the path to CARV_Linux_Library_x.x.x.tar.gz>"
```

### Building the Linux Images

Start the build process:
```bash
bitbake fsl-image-grayvip
```

Once the build is finalized, the artifacts will be present in `tmp/deploy/images`.
