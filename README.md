    ##########################
    ##    ___        ___    ##
    ##   //  \      //  \   ##
    ##  // __ \    // __ \  ##
    ## //      \  //      \ ##
    ##########################


Welcome to AA, the Ansible Arch-installer. My name's TDT.

Use these playbooks to deploy an idempotent and repeatable arch installation

This README is _not_ a guide on using Ansible, there are lots of other resources for that and trying to include that here would likely do more of a dis-service than good.

Additionally these roles will not work out of the box, you'll need to configure them to your specific usecase. Configurations occur in only two files, I'll hopefully spell these out easily enough to make it straight forward for you.

Please report issues, mistakes/bugs, or suggestions via PR or email me at electronictelegrams@protonmail.com.

Some standards and conventions ->

VARIABLES:

Variables are defined in two files, host_vars/<your_host>/vars.yml and host_vars/<your_host>/vault.yml. The later, after setting all your variables, should be encrypted with ansible_vault to protect sensitive information such as passwords. I'll note how to do that in the vault file. For more information on Ansible variable precendence, please see [this page](https://docs.ansible.com/ansible/latest/reference_appendices/general_precedence.html).

Table of Content - What to expect:

There are two main playbooks, install.yml and post_install.yml

The install.yml playbook is responsible for installing arch linux on the device your ISO is mounted and booted into. The following configurations are created:

- Disk Partitioning, LUKS setup, and filesystem creation and mounting
- Pacstrap
- Generate the fstab
- Set the timezone and hostname
- Set the mkinitcpio HOOKS and make the config
- Install GRUB and set the CMDLINE variable
- Enable NetworkManager and SSHD services
- Define an admin user and give it sudo privs

The post_install.yml playbook is all userspace configuration. By this point you already have a functional encrypted Arch installation, but for a graphical system and addition of other users post_install.yml sets this up. The following configurations are created:

- Create additional system users, one that's a non-sudo user and another that's a build user for package building
- Create user-independent directories housing scripts and dotfiles, then clone some repos into them
- Install the rest of the packages
- Use GNU stow to symlink dotfiles into place


## How To Run These Plays

Get your [arch linux ISO](https://archlinux.org/download/) and put it onto a drive. There are many ways to do this, and that page also links to some directions if you don't know how. Connect the drive to some networked device, this could be the device you're running the ansible install from.

We need to have passwordless SSH access to that machine. With physical access to the booted ISO, change the root password. It can be something trivial, this bootable iso session is ephemeral.

    passwd

Then, copy your ssh key to the remote host:

    ssh-copy-id root@<ip of machine>

With that set, open first the host_vars vars.yml file for your host and set the variables there. Some are already filled out if they are sensible defaults.

    vim host_vars/<your_host>/vars.yml

Open the vault.yml file and change your secrets

    vim host_vars/<your_host>/vault.yml

Encrypt the vault file

    ansible-vault encrypt host_vars/<your_host>/vault.yml

Add your host to the inventory file

    echo "<your_host>" > inventory

Run the install

    ansible-playbook -i inventory -l <your_host> -e '{ansible_user: root}' --ask-vault-pass install.yml

This should complete without errors. When it does, run the post-install if you want a more configured system. At this point you have a functional Arch install, however.

    ansible-playbook -i inventory -l <your_host> --ask-vault-pass post_install.yml

Package installation takes the longest, the roles will provide some output to help you follow along if you want to ensure things are progressing.
