    ##########################
    ##    ___        ___    ##
    ##   //  \      //  \   ##
    ##  // __ \    // __ \  ##
    ## //      \  //      \ ##
    ##########################


Welcome to AA, the Ansible Arch-installer. My name's TDT.

Use these playbooks to deploy an idempotent arch installation

This README is _not_ a guide on using Ansible, there are lots of other resources for that and trying to include that here would likely do more of a dis-service than good.

Additionally these roles will not work out of the box, you'll need to configure them to your specific usecase. Configurations occur in only two files, I'll hopefully spell these out easily enough to make it straight forward for you.

Please report issues, mistakes/bugs, or suggestions via PR or email me at electronictelegrams@protonmail.com.

Some standards and conventions ->

VARIABLES:

Variables are defined in two files, host_vars/<your_host>/vars.yml and host_vars/<your_host>/vault.yml. The latter, after setting all your variables, should be encrypted with ansible_vault to protect sensitive information such as passwords. I'll note how to do that in the vault file. For more information on Ansible variable precendence, please see [this page](https://docs.ansible.com/ansible/latest/reference_appendices/general_precedence.html).

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

The post_install.yml playbook is all userspace configuration. By this point you already have a functional encrypted Arch installation but for a graphical system and addition of other users, post_install.yml sets this up. The following configurations are created:

- Create additional system users, one that's a non-sudo user and another that's a build user for package building
- Create user-independent directories housing scripts and dotfiles, then clone some repos into them
- Install the rest of the packages
- Use GNU stow to symlink dotfiles into place


## How To Run These Plays

Two approaches:

- You can use any standard [arch linux ISO](https://archlinux.org/download/), put it onto a drive, and boot into it. Then, from another device with the ability to run Ansible you can run these plays. This works well if you're installing onto a VM.
- Download the [arch linux TDTLIVE ISO](https://topdowntom.space/tech) from the linked page, add it to a drive, then boot into it. This works best if you're running the install on the same device you're installing onto. When you're booted in, run the script get-repo.sh.

We need to have passwordless SSH access to that machine. From the booted ISO, change the root password. It can be something trivial, this bootable iso session is ephemeral:

    passwd

Then, copy your ssh key to the remote host. Note, if you're using the TDTLIVE iso the "IP address" is localhost:

    ssh-copy-id root@<ip of machine>

With that set, open the host_vars vars.yml file for your host and set the undefined variables there. Some are already filled out if they are sensible defaults:

    vim host_vars/<your_host>/vars.yml

Open the vault.yml file and change your secrets:

    vim host_vars/<your_host>/vault.yml

Encrypt the vault file:

    ansible-vault encrypt host_vars/<your_host>/vault.yml

Add your host to the inventory file:

    echo "<your_host>" > inventory

Run the install:

    ansible-playbook -i inventory -l <your_host> -e '{ansible_user: root}' --ask-vault-pass install.yml

This should complete without errors. Congrats, you have a complete encrypted arch install.

- If you're installing on a remote host, i.e. VM or some other device on your network, continue below and run the post_install.yml play if you want to continue installing a graphical environment.
- If you're installing this via option two, reboot your machine without the bootable drive. Unencrypt your drive, login as your admin user, then continue below.

    ansible-playbook -i inventory -l <your_host> --ask-vault-pass post_install.yml

Package installation takes the longest, the roles will provide some output to help you follow along if you want to ensure things are progressing.
