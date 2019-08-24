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

You can run this on a different machine than you're setting up (remote
inventory) or on the new system itself (localhost inventory). This is
determined by which inventory file you choose on the command line, examples
below.

Note also that there are lots of tags on tasks and roles that can be used for
debugging.

There's a role in this project to swap the caps lock and left control keys in
the kernel keymap. This behavior isn't desired by many people. To suppress it,
add this to your ansible-playbook commands: `--skip-tags=capsctrl`

### Using it on a remote system

#### Pre-Ansible

On the target machine

- Boot the Arch installer
- Connect to the internet
- Set the root user's password to `arch1` with `passwd`
- Start sshd: `# systemctl start sshd`
- Get the machine's IP for the ansible inventory file

On the controller system

- Edit the `inventory/remote.yml` file, changing the IP to what we discovered
  above.
- If this isn't the first run, you may also need to remove the target system's
  IP from the controller system's `.ssh/known_hosts` file for your user
- Edit the file `inventory/group_vars/all.yml`. There are many things here that
  will be specific to your system's hardware and how you want it set up.

Running it

    $ ansible-playbook -i inventory/remote.yml install.yml

### Using it on localhost

#### Pre-Ansible

On the target machine

- Boot the Arch installer
- Connect to the internet
- Install git and ansible: `pacman -Sy git ansible`
- Clone this project: `git clone https://github.com/dino-/arch-install` and
  enter the directory: `cd arch-install`
- Edit the file `inventory/group_vars/all.yml`. There are many things here that
  will be specific to your system's hardware and how you want it set up.

Running it

    # ansible-playbook -i inventory/localhost.yml install.yml

*Be very careful what you run this on!* This playbook will destroy the hard
drive set in the `drive` variable in `inventory/group_vars/all.yml`

### Post-installation

- Reboot the new system
- Set the root user's password
- Add users
- Install software
- Use your machine


## Development

Much of this work was done by beating up a VirtualBox VM booted with the Arch
installation media. Here are the critical specs of the machine I was using:

System

- Base Memory: 4096 MB
- Processors: 2
- EFI: Enabled
- Acceleration: VT-x/AMD-V, Nested Paging, PAE/NX, KVM Paravirt

Display

- Video Memory: 128 MB
- Graphics Controller: VBoxVGA

Storage

- Controller: SATA Port 0, 8 GB VDI

Network

- Bridged


## Contact

Dino Morelli <dino@ui3.info>
