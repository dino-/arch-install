## Pre-ansible

- Boot the Arch USB
- Connect to the internet
- Set the root user's password to `arch1` with `passwd`
- Start sshd `# systemctl start sshd`
- Get machine's IP for the ansible inventory
- If this isn't the first run, may need to remove this IP from the controller system's `.ssh/known_hosts` file

## Ansible roles

Pre-installation

- Verify the boot mode
  - This is that efivars thing
- Update the system clock
- Partition the disks
  - Can we drive gdisk from shell comments? Not interactive? If not, what alternative partitioning tool should be used?
- Format the partitions
- Mount the filesystems

Installation

- Select the mirrors
- Install the base packages
- Configure fstab
- Chroot into the new system
  - Set the time zone, ans: `timezone`
  - Set the locale, ans: `locale_gen`
  - Set the hostname and hosts
  - Configure initramfs
  - Set the root password
  - Install and configure the boot loader
    - We are interested in systemd-boot

Post-installation

- Add users
  - Including sudoers where applicable
- Install software
  - vim
