## 1、插入U盘查看盘符

```shell
# 命令
diskutil list

# 打印
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI ⁨EFI⁩                     209.7 MB   disk0s1
   2:                 Apple_APFS ⁨Container disk1⁩         500.1 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.1 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume ⁨Macintosh HD - 数据⁩     379.7 GB   disk1s1
   2:                APFS Volume ⁨Preboot⁩                 457.2 MB   disk1s2
   3:                APFS Volume ⁨Recovery⁩                622.1 MB   disk1s3
   4:                APFS Volume ⁨VM⁩                      1.1 GB     disk1s4
   5:                APFS Volume ⁨Macintosh HD⁩            15.3 GB    disk1s5
   6:              APFS Snapshot ⁨com.apple.os.update-...⁩ 15.3 GB    disk1s5s1

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     Apple_partition_scheme                        *31.0 GB    disk2
   1:        Apple_partition_map ⁨⁩                        4.1 KB     disk2s1
                    (free space)                         1.7 GB     -
   2:                  Apple_HFS ⁨⁩                        2.6 MB     disk2s2
```

得出 U盘的盘符是 

```shell
/dev/disk2
```

## 2、卸载U盘

```shell
# 命令
diskutil unmountDisk /dev/disk2

# 打印
Unmount of all volumes on disk2 was successful
```



## 3、然后转换ubuntu镜像：

```shell
# 命令
hdiutil convert -format UDRW -o /Users/cuiyue/tools/ubuntu/ubuntu-20.04.2.0-desktop-amd64.img /Users/cuiyue/tools/ubuntu/ubuntu-20.04.2.0-desktop-amd64.iso

# 打印
正在读取Driver Descriptor Map（DDM：0）…
正在读取Ubuntu 20.04.2.0 LTS amd64      （Apple_ISO：1）…
正在读取Apple（Apple_partition_map：2）…
正在读取Ubuntu 20.04.2.0 LTS amd64      （Apple_ISO：3）…
正在读取EFI（Apple_HFS：4）…
正在读取Ubuntu 20.04.2.0 LTS amd64      （Apple_ISO：5）…
.................................................................................................................................................................................................................................
已耗时： 7.134s
速度：384.6M字节/秒
节省：0.0%
created: /Users/cuiyue/tools/ubuntu/ubuntu-20.04.2.0-desktop-amd64.img.dmg
```



## 4、制作启动U盘 注意这里的disk2 变成了 rdisk2

```shell
# 命令
sudo dd if=/Users/cuiyue/tools/ubuntu/ubuntu-20.04.2.0-desktop-amd64.img.dmg of=/dev/rdisk2 bs=1m

# 打印
2743+1 records in
2743+1 records out
2877227008 bytes transferred in 162.134316 secs (17745947 bytes/sec)
```

