# Gentoo Portage ebuild tmpfs

If you are hosting a busy portage rsync or nfs server, and have 2GB free non-swappable memory, this initd script migrates portage into fast tmpfs which persists server reboots.

## setup

copy portage-tmpfs into /etc/init.d/, run setup, then start

```shell
wget https://raw.githubusercontent.com/nabbi/gentoo-portage-tmpfs/master/portage-tmpfs -O /etc/init.d/portage-tmpfs
chmod +x /etc/init.d/portage-tmpfs

/etc/init.d/portage-tmpfs setup
/etc/init.d/portage-tmpfs start
rc-update add portage-tmpfs
```

## removal

revert back to normal non-tmpfs portage

```shell
/etc/init.d/portage-tmpfs stop
/etc/init.d/portage-tmpfs remove
rc-update del portage-tmpfs
rm /etc/init.d/portage-tmpfs
```

