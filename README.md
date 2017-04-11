# tools
tools for helping in dev.

# deploypkg
Script to deploy a package to a device through a ssh connection

There is no installation script for the moment so you need to setup some thing
by hands first.

## prerequisite
The package should have a configuration PKGDEPLOY file.

The directory `/tmp/deploypkg` should exist with w permissions, or the default
should be changed via the configuration files.

The following variables must be made available to the tool through the configuration files:
- **target**: SSH name of the target, for example root@192.168.0.1
- **pkg_target_dest**:  destination directory for the 
- **pkg_files**: files to package (default: get all files tracked in git)
- **pkg_tmp_dir**: local temporary directory to save the package to (default `/tmp/deploypkg`) 

These can be set and modified using config files. A config file is a sh script run after setting default values,
so you can run custom code in it if you wish. They are run in the following order:
- the optional script indicated by `PKGDEPLOY_CONFIG` environment variable
- the local `PKGDEPLOY` script

## troubleshoot

### The script fails when checking for local temporary directory
Make sure pkg_tmp_dir exists and you can write to it.
To create and set permission for default directory, use:

```sh
sudo (mkdir /tmp/deploypkg && chown $USER:$USER /tmp/deploypkg)
```

### The directory is not recognized as a valid package
Make sure the directory contains a valid PKGDEPLOY file. You can do that by running

```sh
source PKGDEPLOY && echo "target='$target' pkg_target_dest='$pkg_target_dest' pkg_files='$pkg_files' pkg_tmp_dir='$pkg_tmp_dir'"
```
and manually checking for any strange value

### The tools asks for my SSH password several times in a row
The tools performs some of its sanity check by connecting to the target using the 
SSH credential you specify. In order to reduce the hassle of entering your SSH password several
times in a row, you can add you public key to the device, using the on-liner:
```sh
ssh $target "echo $(cat ~/.ssh/id_rsa.pub) >> ~/.ssh/authorized_keys"
```
