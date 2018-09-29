# ganeti-hook-grow-disk

ganeti hook for growing (resize) a qemu/KVM disk online

  !!!!! use at your own risk !!!!!

please note: due to needed naming conventions of disks inside qemu, this hook needs at least ganeti-2.10 or newer

## potential data lose
For file based instances (disk template file or sharedfile) there is a risk of losing data (shrinking the disk). The reason is, that the qemu HMP command block_resize sets the size of the disk/file on its own (after ganeti has grown the disk/file). If something in this hook went wrong, you may end up with a shrunken disk.

The same may be true for rbd template (can't test this).

Host block devices like DRBD, LVM or blockdev template are safe, because qemu does nothing expect to get the new size from the device and notify the guest.

To prevent users from overlooking this warning you have to place a confirmation variable into this script. See the script for howto. 

# Installation instrunctions
* copy this script into `/etc/ganeti/hooks/disk-grow-post.d/` on every node
* make sure it is executable

You can use the following commands on the master node to install this hook in your cluster

```bash
gnt-cluster command mkdir -p /etc/ganeti/hooks/disk-grow-post.d/
install -o root -g root -m 0700 /source/path/to/this/script /etc/ganeti/hooks/disk-grow-post.d/00-grow-disk-online
gnt-cluster copyfile /etc/ganeti/hooks/disk-grow-post.d/50-grow-disk-online
```

This hook will log into the instance's primary nodes syslog (i.e.  /var/log/syslog). Look there for general actions and errors.

In order to determine the instance's primary node make sure that "hostname -f" is identical to ganeti's node name. 

