# device/xiaomi/beryllium (AOSP device config for Xiaomi Poco F1)

# How to unlock and root Poco F1?
```
IMPORTANT NOTICE -->

UNLOCKING AND ROOTING MAY VOID YOUR PHONE WARRANTY AND
MAY BRICK YOUR DEVICE AS WELL. I'M NOT RESPONSIBLE FOR
EITHER OF THAT.
```

Here is a reasonable guide to get you started on
unlocking and rooting Poco F1 -->
https://forum.xda-developers.com/poco-f1/how-to/xiaomi-poco-f1-unlock-bootloader-custom-t3839405

Just for the records I downloaded and installed following
external packages to unlock and root my device-->
* miflash_unlock-en-3.3.525.23.zip (MS Windows only tool for unlocking)
* twrp-3.3.0-0-beryllium.img (Recovery)
* beryllium-9.6.10-9.0-vendor-firmware.zip (LineageOS dependency)
* lineage-16.0-20190612-nightly-beryllium-signed.zip
* Magisk-v19.3.zip (Root)
* MagiskManager-v7.2.0.apk

Also Dont forget to take a backup of your images from
TWRP and copy them to your Host machine. It will come
very handy. Believe me :)

# How to build and flash beryllium AOSP images?

* Download source and build AOSP images for Poco F1 (Beryllium) -->

```
mkdir aosp-repo
cd aosp-repo
repo init -u https://android.googlesource.com/platform/manifest -b master
git clone git@github.com:pundiramit/android-local-manifests.git .repo/local_manifests -b master
repo sync -j$nproc
source build/envsetup.sh
lunch beryllium-userdebug
make -j$nproc
```

  NOTE: To get display working on PocoF1, we need supported Adreno
        firmware binaries, otherwise PocoF1 will not boot to UI.

        Adreno binaries are shipped with non-distributable license,
        hence I'm not shipping them in my build setup. You can
        extract Adreno a630_* firmware binaries from a working
        device build. I extracted mine from
        lineage-16.0-20190612-nightly-beryllium-signed.zip ;)

        Then copy the binaries to out vendor directory
        i.e. out/target/product/beryllium/vendor/firmware,
        and run "make -j$nproc" to create vendor.img again.

* Flash and boot AOSP images -->

```
fastboot flash system system.img
fastboot flash vendor vendor.img
fastboot flash userdata userdata.img
fastboot flash boot boot.img
fastboot reboot
```

# How to run custom kernels?

* Run following commands to clone the kernel source and
  prebuilt Android toolchains and build scripts:

```
mkdir kernel-repo
cd kernel-repo
repo init -u https://android.googlesource.com/kernel/manifest -b common-android-mainline
git clone git@github.com:pundiramit/android-local-manifests.git .repo/local_manifests -b kernel
repo sync -j$nproc
BUILD_CONFIG=beryllium/build.config.beryllium ./build/build.sh
```

Delete all objects in aosp-repo/device/xiaomi/beryllium/prebuilt-kernel/android-mainline/
then copy build artifacts from kernel-repo/out/beryllium-android-mainline/dist/ to
aosp-repo/device/xiaomi/beryllium/prebuilt-kernel/android-mainline/ build
AOSP images again.

```
cd aosp-repo
source build/envsetup.sh
lunch beryllium-userdebug
make TARGET_KERNEL_USE=mainline -j$nproc
```

Now flash and boot AOSP images again with your custom kernel.

ToDo -->
* Brightness Control
* Modem / RIL / Voice Call
* Battery Stats
