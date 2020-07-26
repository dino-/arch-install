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

There's a role in this project to swap the caps lock and left control keys in
the kernel keymap. This behavior isn't desired by many people. To suppress it,
add this to your ansible-playbook commands: `--skip-tags=capsctrl`

### localhost execution

*Be very careful what you run this on!* This playbook will destroy the hard
drive set in the `drive` variable in `inventory/group_vars/all.yml`

On the target machine boot the Arch installer. It should connect itself to the
internet if it can.

There should be just barely enough space on the booted drive to install
ansible, do that now.

    # pacman -Sy ansible

Download this project's files for the corresponding arch version

    # wget https://github.com/dino-/arch-install/archive/arch-install-archlinux-2020-04-01.tar.gz

Unpack it and enter the directory

    # tar xzvf arch-install-archlinux-2020-04-01.tar.gz
    # cd arch-install-archlinux-2020-04-01

Edit the file `inventory/group_vars/all.yml`. There are many things here that
will be specific to your system's hardware and how you want it set up.

Perform the installation

    # ansible-playbook -i inventory/localhost.yml install.yml

### Remote execution

Using a second, existing system (controller) to set up your new one (target).

#### On the target system

Boot the Arch installer. It should connect itself to the internet if it can.

Set the root user's password to `arch1` with `passwd`

Start sshd

    # systemctl start sshd

Get the machine's IP for the ansible inventory file below

    # ip addr

#### On the controller system

Download this project's files for the corresponding arch version

    $ wget https://github.com/dino-/arch-install/archive/arch-install-archlinux-2020-04-01.tar.gz

Unpack it and enter the directory

    $ tar xzvf arch-install-archlinux-2020-04-01.tar.gz
    $ cd arch-install-archlinux-2020-04-01

Edit the file `inventory/remote.yml`, changing the IP to what we discovered
above.

Edit the file `inventory/group_vars/all.yml`. There are many things here that
will be specific to your system's hardware and how you want it set up.

If this isn't the first run, you may also need to remove the target system's IP
from the controller system's `$HOME/.ssh/known_hosts` file for your user.

Perform the installation

    $ ansible-playbook -i inventory/remote.yml install.yml

### Post-installation

#### Configure networking

I personally like using Arch's netctl for mobile machines that change networks
often as it can handle both wired and wireless with the same UI, has excellent
cli support and can generate profiles.

On that note, my installation role installs `netcl`, `wpa_supplicant`,
`dialog`, and `dhcpcd` which can be configured after the first boot. If you
need something different, feel free to comment that stuff out in
`roles/installation/tasks/main.yml` and do your own thing.

Some helpful links on archwiki:

- [Network configuration](https://wiki.archlinux.org/index.php/Network_configuration)
- [netctl](https://wiki.archlinux.org/index.php/Netctl)

#### Add a user to log in with

You'll probably want one user other than root to log in with on the new system

    # useradd -m -g users -G wheel,games,network,audio,optical,scanner -s /bin/bash archie
    # passwd archie

#### What next?

- Reboot the new system
- Finish network configuration
- Set the root user's password if you want
- Add other users
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
- Graphics Controller: VMSVGA

Storage

- Controller: SATA Port 0, 8 GB VDI

Network

- Bridged

When using the VM, I often take snapshots during development to re-use some of
the state. Here are some handy starting snapshot labels:

    Before first boot
    |- Booted live disk
       |- Set root pw and started sshd
          |- Current State

In this ansible playbook there are lots of tags on tasks and roles that can be
used for development/debugging.


## Contact

Dino Morelli <dino@ui3.info>
