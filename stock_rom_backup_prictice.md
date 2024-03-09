# 原厂固件导出实践

依赖固件有adb roo权限。

adb root

adb shell

```shell
GRDZ-K12:/ # gdisk
/system/bin/sh: gdisk: inaccessible or not found
127|GRDZ-K12:/ # sgdisk --print /dev/block/mmcblk2
Disk /dev/block/mmcblk2: 122142720 sectors, 58.2 GiB
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): BF670000-0000-4250-8000-061B00006721
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 122142686
Partitions will be aligned on 2048-sector boundaries
Total free space is 8189 sectors (4.0 MiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            8192           16383   4.0 MiB     FFFF  security
   2           16384           24575   4.0 MiB     FFFF  uboot
   3           24576           32767   4.0 MiB     FFFF  trust
   4           32768           40959   4.0 MiB     FFFF  misc
   5           40960           49151   4.0 MiB     FFFF  dtbo
   6           49152           51199   1024.0 KiB  FFFF  vbmeta
   7           51200          153599   50.0 MiB    FFFF  boot
   8          153600          350207   96.0 MiB    FFFF  recovery
   9          350208         1136639   384.0 MiB   FFFF  backup
  10         1136640         1923071   384.0 MiB   FFFF  cache
  11         1923072         1955839   16.0 MiB    FFFF  metadata
  12         1955840         1957887   1024.0 KiB  FFFF  baseparameter
  13         1957888        13395967   5.5 GiB     FFFF  super
  14        13395968       122142655   51.9 GiB    FFFF  userdata
```

## 方法1：按分区导出

```shell
GRDZ-K12:/ # cd /dev/block/by-name

GRDZ-K12:/dev/block/by-name # ls -al
total 0
drwxr-xr-x 2 root root  380 2024-03-09 20:48 .
drwxr-xr-x 6 root root 1260 2024-03-09 20:48 ..
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 backup -> /dev/block/mmcblk2p9
lrwxrwxrwx 1 root root   21 2024-03-09 20:48 baseparameter -> /dev/block/mmcblk2p12
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 boot -> /dev/block/mmcblk2p7
lrwxrwxrwx 1 root root   21 2024-03-09 20:48 cache -> /dev/block/mmcblk2p10
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 dtbo -> /dev/block/mmcblk2p5
lrwxrwxrwx 1 root root   21 2024-03-09 20:48 metadata -> /dev/block/mmcblk2p11
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 misc -> /dev/block/mmcblk2p4
lrwxrwxrwx 1 root root   18 2024-03-09 20:48 mmcblk2 -> /dev/block/mmcblk2
lrwxrwxrwx 1 root root   23 2024-03-09 20:48 mmcblk2boot0 -> /dev/block/mmcblk2boot0
lrwxrwxrwx 1 root root   23 2024-03-09 20:48 mmcblk2boot1 -> /dev/block/mmcblk2boot1
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 recovery -> /dev/block/mmcblk2p8
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 security -> /dev/block/mmcblk2p1
lrwxrwxrwx 1 root root   21 2024-03-09 20:48 super -> /dev/block/mmcblk2p13
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 trust -> /dev/block/mmcblk2p3
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 uboot -> /dev/block/mmcblk2p2
lrwxrwxrwx 1 root root   21 2024-03-09 20:48 userdata -> /dev/block/mmcblk2p14
lrwxrwxrwx 1 root root   20 2024-03-09 20:48 vbmeta -> /dev/block/mmcblk2p6

GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p12 of=/storage/self/primary/baseparameter.img
2048+0 records in
2048+0 records out
1048576 bytes (1.0 M) copied, 2.897290 s, 353 K/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p7 of=/storage/self/primary/boot.img
102400+0 records in
102400+0 records out
52428800 bytes (50 M) copied, 0.745011 s, 67 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p5 of=/storage/self/primary/dtbo.img
8192+0 records in
8192+0 records out
4194304 bytes (4.0 M) copied, 0.073100 s, 55 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p11 of=/storage/self/primary/metadata.img
32768+0 records in
32768+0 records out
16777216 bytes (16 M) copied, 0.240956 s, 66 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p4 of=/storage/self/primary/misc.img
8192+0 records in
8192+0 records out
4194304 bytes (4.0 M) copied, 0.059891 s, 67 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p8 of=/storage/self/primary/recovery.img
196608+0 records in
196608+0 records out
100663296 bytes (96 M) copied, 3.305174 s, 29 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p1 of=/storage/self/primary/security.img
8192+0 records in
8192+0 records out
4194304 bytes (4.0 M) copied, 0.067972 s, 59 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p3 of=/storage/self/primary/trust.img
8192+0 records in
8192+0 records out
4194304 bytes (4.0 M) copied, 0.069326 s, 58 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p2 of=/storage/self/primary/uboot.img
8192+0 records in
8192+0 records out
4194304 bytes (4.0 M) copied, 0.065934 s, 61 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p6 of=/storage/self/primary/vbmeta.img
2048+0 records in
2048+0 records out
1048576 bytes (1.0 M) copied, 0.031868 s, 31 M/s
GRDZ-K12:/dev/block/by-name # dd if=/dev/block/mmcblk2p13 of=/storage/self/primary/super.img
11438080+0 records in
11438080+0 records out
5856296960 bytes (5.4 G) copied, 114.479065 s, 49 M/s

GRDZ-K12:/ # exit
```


```bat
C:\Users\User\adb>adb pull /storage/self/primary/*.img .
adb: error: failed to stat remote object '/storage/self/primary/*.img': No such file or directory

C:\Users\User\adb>adb pull /storage/self/primary/*img .
adb: error: failed to stat remote object '/storage/self/primary/*img': No such file or directory

C:\Users\User\adb>adb pull /storage/self/primary/super.img .
/storage/self/primary/super.img: 1 file pulled, 0 skipped. 32.0 MB/s (5856296960 bytes in 174.425s)

C:\Users\User\adb>adb pull /storage/self/primary/vbmeta.img .
/storage/self/primary/vbmeta.img: 1 file pulled, 0 skipped. 28.4 MB/s (1048576 bytes in 0.035s)

C:\Users\User\adb>adb pull /storage/self/primary/uboot.img .
/storage/self/primary/uboot.img: 1 file pulled, 0 skipped. 31.7 MB/s (4194304 bytes in 0.126s)

C:\Users\User\adb>adb pull /storage/self/primary/trust.img .
/storage/self/primary/trust.img: 1 file pulled, 0 skipped. 32.2 MB/s (4194304 bytes in 0.124s)

C:\Users\User\adb>adb pull /storage/self/primary/security.img .
/storage/self/primary/security.img: 1 file pulled, 0 skipped. 32.6 MB/s (4194304 bytes in 0.123s)

C:\Users\User\adb>adb pull /storage/self/primary/recovery.img .
/storage/self/primary/recovery.img: 1 file pulled, 0 skipped. 34.4 MB/s (100663296 bytes in 2.794s)

C:\Users\User\adb>adb pull /storage/self/primary/misc.img .
/storage/self/primary/misc.img: 1 file pulled, 0 skipped. 31.7 MB/s (4194304 bytes in 0.126s)

C:\Users\User\adb>adb pull /storage/self/primary/metadata.img .
/storage/self/primary/metadata.img: 1 file pulled, 0 skipped. 33.3 MB/s (16777216 bytes in 0.480s)

C:\Users\User\adb>adb pull /storage/self/primary/dtbo.img .
/storage/self/primary/dtbo.img: 1 file pulled, 0 skipped. 31.7 MB/s (4194304 bytes in 0.126s)

C:\Users\User\adb>adb pull /storage/self/primary/boot.img .
/storage/self/primary/boot.img: 1 file pulled, 0 skipped. 33.4 MB/s (52428800 bytes in 1.495s)

C:\Users\User\adb>adb pull /storage/self/primary/baseparameter.img .
/storage/self/primary/baseparameter.img: 1 file pulled, 0 skipped. 29.1 MB/s (1048576 bytes in 0.034s)
```


## 方法2：导出userdata之前的整块数据


```shell
GRDZ-K12:/ # dd if=/dev/block/mmcblk2 of=/storage/self/primary/GRDZ-K12_lean.img bs=512 count=13395968
13395968+0 records in
13395968+0 records out
6858735616 bytes (6.3 G) copied, 123.872862 s, 53 M/s
GRDZ-K12:/ # exit
```

```bat
C:\Users\User\adb>adb pull /storage/self/primary/GRDZ-K12_lean.img .
/storage/self/primary/GRDZ-K12_lean.img: 1 file pulled, 0 skipped. 36.0 MB/s (6858735616 bytes in 181.662s)
```