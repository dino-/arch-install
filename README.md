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

On the target machine boot the Arch installer. There will not be enough disk
space to install the tools we need. At the Arch install media boot menu, press
`e` to edit the kernel parameters and add this to the end:

    cow_spacesize=1G

It should connect itself to the internet if it can see a DHCP server. If not,
you'll need to figure that out using Arch wiki.

Once it's up and networked, we need to install some tools

    # pacman -Sy ansible wget

Download the arch-install project's files for the corresponding arch version

    # wget https://github.com/dino-/arch-install/archive/refs/tags/20210401-2.tar.gz

Unpack it and enter the directory

    # tar xzvf 20210401-2.tar.gz
    # cd arch-install-20200401-2

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

    # wget https://github.com/dino-/arch-install/archive/refs/tags/20210401-2.tar.gz

Unpack it and enter the directory

    # tar xzvf 20210401-2.tar.gz
    # cd arch-install-20200401-2

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
- [dhcpcd](https://wiki.archlinux.org/index.php/Dhcpcd)

#### What next?

- Reboot the new system
- Finish network configuration
- Set the root user's password if you want
- Change the other user's password
- Add other users
- Install software
- Use your machine


## Development

Much of this work was done by beating up a VirtualBox VM booted with the Arch
installation media. Here are the critical specs of the machine I was using:

System

- Base Memory: 4096 MB
- Processors: 2 (or 1 is fine, whatever you want)
- EFI: Enabled (IMPORTANT, the installer is expecting this to be an EFI/GPT
  system)
- Acceleration: VT-x/AMD-V, Nested Paging, PAE/NX, KVM Paravirt

Display

- Video Memory: 128 MB
- Graphics Controller: VMSVGA (VBoxVGA is rumored to work also but I've
  experienced bugs)

Storage

- Controller: SATA Port 0, 12 GB VDI

Network

- Bridged

When using the VM, I often take snapshots during development to re-use some of
the state. Here are some handy starting snapshot labels:

    Before first boot
    |- Booted live disk
       |- Set root pw and started sshd
          |- Current State

In this ansible playbook there are lots of tags on tasks and roles that can be
used for development/debugging. Use the `--tags` and `--skip-tags` switches
with `ansible-playbook` to assist.


## Contact

Dino Morelli <dino@ui3.info>
