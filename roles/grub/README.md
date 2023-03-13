## grub

Install grub on the drive, then make the grub config. Grabs the UUID for the crypt partition where our encrypted LV are installed, and modifies GRUB_CMDLINE_LINUX in the grub config.

### Variables

- crypt_mount_name
- cryptlvm_uuid
- disk_scheme.crypt_part.partition
- disk_scheme.device
