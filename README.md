#Samsung Galaxy Tab S2 T719 (Codename gts28velte)Build manifest for Lineage 18.1

#Instructions for latest security patch:

mkdir -p ~/bin
mkdir -p ~/android/lineage

curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo

git config --global user.email "you@example.com"
git config --global user.name "Your Name"

cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b lineage-18.1

#Get the device manifest file and put it in ~/android/lineage/.repo/local_manifests/
git clone https://github.com/Schlumpf7/local_manifests.git -b lineage-18.1 ~/android/lineage/.repo/local_manifests

#Turn on caching to speed up build.
mkdir ~/ccache
export CCACHE_DIR=~/ccache
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
ccache -M 50G

repo sync

#Get Patches from https://github.com/retiredtab/LineageOS-build-manifests/tree/main/18.1 and put it in the right folder

#Apply patches from https://review.lineageos.org/q/status:open+branch:lineage-18.1 like below

cd packages/modules/NetworkStack
git stash --include-untracked
croot

cd build/core
git stash --include-untracked
croot

cd .repo/repo
git pull
croot
repo sync --force-sync

repopick -t R_asb_2024-03

repopick -t R_asb_2024-04

repopick 392208

cp  android/default.xml .repo/manifests/default.xml
repo sync --force-sync external/sonivox/

repopick -t R_asb_2024-05

repopick 399744

cp  android/default.xml .repo/manifests/default.xml
repo sync --force-sync system/libfmq

repopick -t R_asb_2024-06

repopick -t R_asb_2024-07

repopick -t R_asb_2024-08

repopick -t R_asb_2024-09

repopick -t R_asb_2024-10

repopick -f 408436
cd .repo/manifests
git stash --include-untracked
patch -p1 <  ~/Downloads/local_manifests/Patches/181-nov-2024-mani.diff
croot

repopick -t R_asb_2024-11
repo sync --force-sync external/skia

repopick -t R_asb_2024-12

repopick -f 415707
cd .repo/manifests
git stash --include-untracked
patch -p1 < ~/Downloads/local_manifests/Patches/181-jan-2025-mani.diff
croot

repopick -t R_asb_2025-01
repo sync --force-sync external/giflib prebuilts/abi-dumps/vndk

repopick -t R_asb_2025-02

repopick -f 421145
cd .repo/manifests
git stash --include-untracked
patch -p1 < ~/Downloads/local_manifests/Patches/181-mar-2025-mani.diff
croot

repopick -t R_asb_2025-03
repo sync --force-sync external/dng_sdk

#Edit the kernel

cd kernel/samsung/msm8976
patch -p1 <  ~/Downloads/local_manifests/.repo/local_manifests/Patches/msm8976-kernel-april-15.diff
croot

#Optinal include microg and fdroid

export WITH_GMS="true"

#finally build the thing.

source build/envsetup.sh
lunch
#select lineageos_gts28velte...

make clean
make apache-xml
make ims-common

brunch gts28velte
