## create_filesystem

Opens the LUKS container, creates a volume group, then creates swap, root, and home logical volumes. After creation, mount them on the chroot /mnt.

### Variables

- crypt_mount_name
- crypt_root_size
- crypt_swap_size
- crypt_volume_group_name
- crypto_become_pass
- disk_scheme.device.crypt_part.partition
