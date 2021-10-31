# Forensics/sysadmin1

> Our sysadmin has disappeared and we can no longer access our sysadmin web portal.
>
> We managed to get an image of his harddrive, but we don't know which bitlocker password corresponds to his drive. Can you help us unlock it?
>
> sysadmin.zip

## Downloading and unzipping
Start by downloading and unzipping `sysadmin.zip`:
```
❯ unzip sysadmin.zip
Archive:  sysadmin.zip
  inflating: bitlocker-passwords.txt
  inflating: sysadmin.vhd
```

## bitlocker2john and john
Using `bitlocker2john` and the provided `bitlocker-passwords.txt` to find the correct Bitlocker password for `sysadmin.vhd`:
```
❯ bitlocker2john -i sysadmin.vhd >> sysadmin.hash
[...]
❯ john --wordlist=bitlocker-passwords.txt sysadmin.hash
[...]
❯ john --wordlist=bitlocker-passwords.txt sysadmin.hash
Using default input encoding: UTF-8
[...]
Loaded 2 password hashes with 2 different salts (BitLocker, BitLocker [SHA-256 AES 32/64])
Press 'q' or Ctrl-C to abort, almost any other key for status
ayRTBNm6kWG28FZVDgzuwxfj (?)
```

## Mounting image
So, trying to use the password `ayRTBNm6kWG28FZVDgzuwxfj` to mount `sysadmin.vhd`. This guide came in pretty handy at this point:
https://medium.com/@kartik.sharma522/mounting-bit-locker-encrypted-vhd-files-in-linux-4b3f543251f0

The following packages are needed:
```
sudo apt install qemu-utils crytpsetup
```

And then insert the nbd-modules in kernel:
```
sudo modprobe nbd
```

Starting to mount the disk image:
```
❯ sudo qemu-nbd -c /dev/nbd1 sysadmin.vhd
[...]
```

Using the `lsblk` command to locate the name of the mountpoint:

```
❯ lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINTS
[...]
nbd1          43:32   0  250M  0 disk
└─nbd1p1      43:33   0  247M  0 part
[...]
```

Here it is the `nbd1p1`. So, using the `cryptsetup` and `ayRTBNm6kWG28FZVDgzuwxfj` as the passphrase:
```
❯ sudo cryptsetup bitlkOpen /dev/nbd1p1 sysadmin
Enter passphrase for /dev/nbd1p1:
```

Now it's possible to mount the disk as normal:
```
❯ sudo mkdir /mnt/vhd
❯ sudo mount /dev/mapper/sysadmin /mnt/vhd
```

Check that the disk has been mounted with `df -h`:
```bash
❯ df -h
Filesystem            Size  Used Avail Use% Mounted on
[...]
/dev/mapper/sysadmin  247M  175M   73M  71% /mnt/vhd
```

And now it's just a regular folder:
```
❯ cd /mnt/vhd && pwd
/mnt/vhd

❯ ls -1
'$RECYCLE.BIN'
flag.txt
ProgramData
'System Volume Information'
Users
Windows

❯ wc -c flag.txt
21 flag.txt
```
