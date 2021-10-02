# Gentoo Portage ebuild tmpfs
If you are hosting a busy portage rsync or nfs server, and have 2GB free non-swappable memory, this initd script migrates portage into fast tmpfs which persists server reboots.

## setup
copy into /etc/init.d/ and run setup and start
```
cp portage-tmpfs /etc/init.d/
/etc/init.d/portage-tmpfs setup
/etc/init.d/portage-tmpfs start
rc-update add portage-tmpfs
```

## removal
revert back to normal non-tmpfs portage
```
/etc/init.d/portage-tmpfs stop
/etc/init.d/portage-tmpfs remove
rc-update del portage-tmpfs
rm /etc/init.d/portage-tmpfs
```
