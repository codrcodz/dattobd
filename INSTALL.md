# dattobd INSTALL

## Installing From Repositories
We recommend that you install the kernel module from Datto's repositories. Datto provides repos for x86-64 editions of RHEL/CentOS, Fedora, openSUSE, SUSE Linux Enterprise, Debian, and Ubuntu LTS.

### RHEL/CentOS
The repository install package `datto-el-rpm-release` is available for EL6+.
```bash
sudo yum localinstall https://cpkg.datto.com/datto-rpm/repoconfig/datto-el-rpm-release-$(rpm -E %rhel)-latest.noarch.rpm
sudo yum install kernel-headers-$(uname -r) dkms-dattobd dattobd-utils
```
### Fedora
The repository install package `datto-fedora-rpm-release` is available for F26+, excluding Rawhide.
```bash
sudo yum install https://cpkg.datto.com/datto-rpm/repoconfig/datto-fedora-rpm-release-$(rpm -E %fedora)-latest.noarch.rpm
sudo yum install kernel-devel-$(uname -r) dkms-dattobd dattobd-utils
```
### openSUSE
The repository install package `datto-opensuse-rpm-release` is available for openSUSE Leap 42.x and 15.x.
#### openSUSE Leap 42.x
Due to the DKMS software not being present in openSUSE's default repositories, the `X11:Bumblebee` or other similar repository providing
DKMS is required. The steps below assume no providers of DKMS are available, so it may not be necessary to add `X11:Bumblebee` if you can
already get DKMS through an already-installed repository.
```bash
sudo zypper addrepo http://download.opensuse.org/repositories/X11:Bumblebee/openSUSE_Leap_42.1/X11:Bumblebee.repo
sudo zypper install https://cpkg.datto.com/datto-rpm/repoconfig/datto-opensuse-rpm-release-42.1-latest.noarch.rpm
sudo zypper install dkms-dattobd dattobd-utils
```
#### openSUSE Leap 15.x
```bash
sudo zypper install https://cpkg.datto.com/datto-rpm/repoconfig/datto-opensuse-rpm-release-15.0-latest.noarch.rpm
sudo zypper install dkms-dattobd dattobd-utils
```
### SUSE Linux Enterprise
The repository install package `datto-sle-rpm-release` is available for SUSE Linux Enterprise 11 SP4 and higher.
#### SUSE Linux Enterprise 11 SP4
Due to the DKMS software not being present in the default repositories and most versions for SLE 11 are too old, we provide a
usable copy in our repositories.
```bash
ktype=$(uname -r | awk -F '-' '{ print $NF }')
kver=$(uname -r | sed "s/-${ktype}//")
kbuild=$(rpm -qa kernel-${ktype} | grep ${kver} | awk -F '.' '{ print $NF }')
sudo zypper install https://cpkg.datto.com/datto-rpm/repoconfig/datto-sle-rpm-release-11-latest.noarch.rpm
sudo zypper install -C "kernel-syms = ${kver}.${kbuild}"
sudo zypper install dkms-dattobd dattobd-utils
```
#### SUSE Linux Enterprise 12
Due to the DKMS software not being present in SLE's default repositories, the `X11:Bumblebee` or other similar repository providing
DKMS is required. The steps below assume no providers of DKMS are available, so it may not be necessary to add `X11:Bumblebee` if you can
already get DKMS through an already-installed repository.
```bash
ktype=$(uname -r | awk -F '-' '{ print $NF }')
kver=$(uname -r | sed "s/-${ktype}//")
sudo zypper addrepo http://download.opensuse.org/repositories/X11:/Bumblebee/SLE_12_SP2_Backports/X11:Bumblebee.repo
sudo zypper install https://cpkg.datto.com/datto-rpm/repoconfig/datto-sle-rpm-release-12-latest.noarch.rpm
sudo zypper install -C "kernel-syms = ${kver}"
sudo zypper install dkms-dattobd dattobd-utils
```
#### SUSE Linux Enterprise 15
Due to the DKMS software not being present in SLE's default repositories, [PackageHub](https://packagehub.suse.com/) or other similar
repository providing DKMS is required. The steps below assume PackageHub is not enabled, so it may not be necessary to enable
PackageHub if you can already get DKMS through an already-installed repository.
```bash
ktype=$(uname -r | awk -F '-' '{ print $NF }')
kver=$(uname -r | sed "s/-${ktype}//")
sudo SUSEConnect -p PackageHub/15/x86_64
sudo zypper install https://cpkg.datto.com/datto-rpm/repoconfig/datto-sle-rpm-release-15-latest.noarch.rpm
sudo zypper install -C "kernel-syms = ${kver}"
sudo zypper install dkms-dattobd dattobd-utils
```
### Debian/Ubuntu LTS
```bash
sudo apt-key adv --recv-keys --keyserver keys.fedoraproject.org 370C85D709D26407
echo "deb [arch=amd64] https://cpkg.datto.com/datto-deb/public/$(lsb_release -sc) $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/datto-linux-agent.list
sudo apt-get update
sudo apt-get install linux-headers-$(uname -r) dattobd-dkms dattobd-utils
```

These packages will install and configure the kernel module to start during the boot process. No further configuration should be required when installing using this method. 

## Installing From Source

### Dependencies

Note that this build process, while it _should_ work with any distribution, has only been tested with the distributions below. The lowest supported kernel is 2.6.18.

#### Debian/Ubuntu
```
sudo apt-get install linux-headers-$(uname -r) build-essential
```

#### RHEL/CentOS/Fedora
```
sudo yum install kernel-devel-$(uname -r)
sudo yum groupinstall "Development Tools"
```

#### openSUSE/SLE
```
sudo zypper install kernel-default-devel
sudo zypper install -t pattern devel_C_C++
```

### Getting, Building, and Installing
To retrieve the sources from our Git repository, clone the Git repository to your local computer.

`cd` into the directory created by git, and then run the following commands:
```bash
sudo make
sudo make install
```

### Configuring the Kernel Module
To start the kernel module immediately, run:
```bash
sudo modprobe dattobd
```

If you would like to have the module be loaded automatically during boot, consult the documentation for your distribution.

## Installation Troubleshooting
On some systems, it may be necessary to let the system know of the location of the shared libraries. If you are having trouble getting `dbdctl` to run, run these two commands:
```
echo /usr/local/{lib,lib64} | sed 's/ /\n/g' | sudo tee /etc/ld.so.conf.d/dattobd.conf
sudo ldconfig
```

If you are getting errors that may indicated that the module is not installed, you can ensure the module installed correctly by running the following commands. 

This should return no output if the module was installed:
```
modprobe dattobd 
```

This should return a single line of output if the kernel module was loaded correctly:
```
lsmod | grep dattobd
```

Refer to your distrobution's instructions for persistent kernel module loading to ensure the module is loaded at boot time each time you restart the system.

If the module was not loaded, you may have installed the `dkms-dattobd` package without first ensuring that your system's kernel headers were installed; it is also possible that your kernel headers package version does not match your running kernel version. This can be verified using your package manager to compare the kernel headers version to the output of `uname -r` to ensure the version numbers match. If you do not, you will have to upgrade/downgrade your kernel and/or headers until the two match and ensure the two stay synced in the future.

## Next Steps / Usage
The kernel module is primarily controlled through `dbdctl(8)`, which was installed previously. For usage instructions, see [dbdctl.8.md](doc/dbdctl.8.md).
