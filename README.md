# TWRP device tree for Reno 5 Pro

# Specifications

| Basic | Spec Sheet |
|---|---|
| Processor | MediaTek Dimensity 1000 + |
| RAM | 8GB |
| Storage | 128GB/256GB |
| Operating System | Android 10, upgradable to Android 12 |



# Status

| Feature | Status |
|---|---|
| adb | Working |
| otg | Working |
| mtp | not Working |
| data decryption | configured for Android 13 FBE; device test required |
| fastbootd | Working |

## Debugging

The recovery image enables verbose `vold`, `fs_mgr`, `libfscrypt`, Keymaster,
GateKeeper, and TWRP tags. Capture the following immediately after boot when
testing decryption:

```sh
adb shell "getprop > /tmp/getprop.txt"
adb shell "dmesg > /tmp/dmesg.txt"
adb shell "logcat -b all -v threadtime -d > /tmp/logcat.txt"
adb pull /tmp/recovery.log
adb pull /tmp/getprop.txt
adb pull /tmp/dmesg.txt
adb pull /tmp/logcat.txt
```

If `/data` is not decrypted, also record the exact error shown in
`/tmp/recovery.log` and the output of `adb shell ls -l /dev/block/by-name`.

For this tree, the Keymaster startup log must report `os_version = 130000`.
Also verify that `Store_1.tf` is opened from an ext4-mounted `/persist`, not
from `tmpfs`, and that both `rpmb_gp_open_session end` and
`rpmb_gp_listenDci: DCI listener` appear before
`fscrypt_mount_metadata_encrypted`. Keymaster should report a non-zero
`boot_patchlevel` matching the installed ROM.
