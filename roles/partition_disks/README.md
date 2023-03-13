## partition_disks

Partition the drive you're booted into in your ISO. Two partitions are created; a boot drive on the first, and a "root" drive on the second. The root partition takes the entirety of the drive, less the size of your boot partition, and will later get encrypted and split up into an LVM group.

### Variables

- disk_scheme.device:
- disk_scheme.boot_part.{size,partition}
- disk_scheme.crypt_part.{size,partition}
