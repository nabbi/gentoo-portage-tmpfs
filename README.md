# Gentoo Portage on tmpfs (with persistent snapshot)

This OpenRC init script mounts `/var/db/repos` on **tmpfs** for fast local access while keeping a **persistent snapshot on disk** across reboots.

It is intended for systems that:

* Serve Portage via **rsync and/or NFS**
* Have **at least ~2 GB of free RAM** available for tmpfs
* Want fast metadata access without repeated disk I/O

At shutdown, the current repo state is snapshotted to disk.
At startup, tmpfs is mounted and the snapshot is restored automatically.

---

## How It Works

1. On first setup:

   * Creates a tar snapshot of `/var/db/repos`
   * Replaces the directory with a tmpfs mount via `/etc/fstab`
   * Adds a sentinel file to prevent unsafe snapshot operations

2. On service start:

   * Mounts tmpfs at `/var/db/repos`
   * Restores the snapshot into tmpfs if empty

3. On service stop:

   * Creates a new snapshot from tmpfs
   * Unmounts tmpfs

4. On removal:

   * Unmounts tmpfs
   * Removes the fstab entry
   * Restores the on-disk repository
   * Deletes the snapshot archive

---

## Requirements

* OpenRC
* tmpfs support
* ~2 GB free RAM (configurable in `/etc/fstab` after setup)
* Existing valid Gentoo repo at `/var/db/repos/gentoo`

---

## Installation & Setup

Copy the init script into place, run setup once, then start and enable it:

```sh
wget https://raw.githubusercontent.com/nabbi/gentoo-portage-tmpfs/master/portage-tmpfs -O /etc/init.d/portage-tmpfs
chmod +x /etc/init.d/portage-tmpfs

/etc/init.d/portage-tmpfs setup
/etc/init.d/portage-tmpfs start
rc-update add portage-tmpfs default
```

Reboot is **not required** after setup.

---

## Removal (Restore Normal Disk-Based Repo)

This fully reverts the system to standard on-disk `/var/db/repos`:

```sh
/etc/init.d/portage-tmpfs stop
/etc/init.d/portage-tmpfs remove
rc-update del portage-tmpfs default
rm /etc/init.d/portage-tmpfs
```

After removal, `/var/db/repos` will contain the restored snapshot.

---

## Notes & Warnings

* If the system crashes before `stop`, the last snapshot may be stale.
* tmpfs contents are volatile until snapshotted.
* If `/var/db/repos` is already a mountpoint, setup will refuse to run.

---

## Typical Use Cases

* Internal rsync mirrors
* NFS-exported Portage trees
* CI systems repeatedly scanning metadata
* Large package sets with heavy eix/equery usage
