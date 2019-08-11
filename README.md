# Install Arch Linux

## Description

This is an Ansible playbook for installing Arch Linux on a system with the
following features and choices:

- UEFI bios and GPT partitioning
- An unencrypted ESD boot partition
- Full drive encryption of the root partition with dm-crypt/luks
- No swap drive and no LVM
- systemd-boot for the boot loader

## Using this playbook

### Pre-Ansible

On the target machine

- Boot the Arch USB
- Connect to the internet
- Set the root user's password to `arch1` with `passwd`
- Start sshd `# systemctl start sshd`
- Get the machine's IP for the ansible inventory

On the controller system

- Edit the `inventory.yml` file, changing the IP to the one above and many of
  the other variables. All configuration is in this file.
- If this isn't the first run, you may need to remove the target system's IP
  from the controller system's `.ssh/known_hosts` file for your user

### Running it

    $ ansible-playbook -i inventory.yml install.yml

For development and debugging, use `--tags` to limit things:

    $ ansible-playbook -i inventory.yml install.yml --tags="pacstrap,genfstab"

### Post-installation

- Reboot the new system
- Set the root user's password
- Add users
- Install software
- Use your machine


## Contact

Dino Morelli <dino@ui3.info>
