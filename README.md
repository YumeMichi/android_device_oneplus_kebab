# Device Tree for 9R (lemonades) for TWRP

## Setup repo tool
Setup repo tool from here https://source.android.com/setup/develop#installing-repo

## Compile

Sync TWRP manifest:

```
repo init --depth=1 -u https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp.git -b twrp-12.1

```

Make a directory named local_manifest under .repo, and create a new manifest file, for example local_manifests.xml
and then paste the following

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <remove-project name="platform/external/erofs-utils" />
  <project path="external/erofs-utils" name="platform/external/erofs-utils" remote="aosp" revision="75d73335d25d52afd6a09c116207cd09bece16ab" />

  <remove-project name="android_build" />
  <project path="build/make" name="YumeMichi/android_build" remote="github" revision="android-12.1-erofs">
    <copyfile src="core/root.mk" dest="Makefile"/>
    <linkfile src="CleanSpec.mk" dest="build/CleanSpec.mk"/>
    <linkfile src="buildspec.mk.default" dest="build/buildspec.mk.default"/>
    <linkfile src="core" dest="build/core"/>
    <linkfile src="envsetup.sh" dest="build/envsetup.sh"/>
    <linkfile src="target" dest="build/target"/>
    <linkfile src="tools" dest="build/tools"/>
  </project>

  <project path="device/oneplus/lemonades" name="YumeMichi/twrp_device_oneplus_lemonades" remote="github" revision="android-12.1-oplus-erofs" />
</manifest>
```
You might need to pick few patches from gerrit.twrp.me to get some stuff working.

Sync the sources with

```
repo sync -j$(nproc --all)
```

To build, execute this command:

```
. build/envsetup.sh; export ALLOW_MISSING_DEPENDENCIES=true; export LC_ALL=C; lunch twrp_lemonades-eng; mka recoveryimage
```

To test it:

```
# To temporarily boot it
fastboot boot out/target/product/lemonades/recovery.img 

# Since 9R has a separate recovery partition, you can flash the recovery with
fastboot flash recovery recovery.img
```

Kernel: https://github.com/YumeMichi/kernel_oneplus_sm8250

##### Credits
- The-Incognito For Recovery Trees of Oneplus 8T
- YumeMichi For Implementing Erofs and Other Misc
- bigbiff for decryption
- Systemad for original tree
- CaptainThrowback for original tree
- mauronofrio for original tree
- TWRP team
- Qnorsten for OOS fix
