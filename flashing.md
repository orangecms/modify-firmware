## Externally

### flashrom

Full image:

```sh
sudo flashrom -p ft2232_spi:type=2232H,port=A \
  -w image.rom
```

Partition only:

```sh
sudo flashrom -p ft2232_spi:type=2232H,port=A \
  -l layout.txt -i mtdblock4 -w mtd4-mod.bin
```

Layout file example:

```
00000000:0004ffff mtdblock0-u-boot
00050000:0042ffff mtdblock1-romfs
00430000:00b0ffff mtdblock2-user
00b10000:00c9ffff mtdblock3-web
00ca0000:00f5ffff mtdblock4-custom
00f60000:00f7ffff mtdblock5-logo
00f80000:00ffffff mtdblock6-mtd
```

## Internally

### mtd-utils

To flash, e.g., partition `/dev/mtd4`:

```sh
/usr/bin/flash_erase /dev/mtd4 0 0
/usr/bin/nandwrite -m -p /dev/mtd4 mtd4-mod.bin
```
