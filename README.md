Centos 7 DISA STIG
================

Configure Centos 7 machine to be DISA STIG compliant. CAT I CAT II and CAT III findings will be corrected by default. Findings can be enabled/disabled by setting the appropriate variable to enable those playbooks.

### WARNING
 There is no STIG for RHEL/Centos 7 yet, it is still in draft form... this playbook is based off the published RHEL6 STIG, and attempts to apply that to a Centos 7 system.
### WARNING

This role **will make changes to the system** that could break things. This is not an auditing tool but rather a remediation tool to be used after an audit has been conducted.

The role tries to be helpful and pause to let you know it found something. You can disable this behaviour if you want to run it unattended by setting `cent7stig_fullauto` to `true`.

## IMPORTANT INSTALL STEP

If you want to install this via the `ansible-galaxy` command you'll need to run it like this: 

`ansible-galaxy install -p roles dsmorse.STIG-cent7,devel`

Based on [Red Hat Enterprise Linux 6 STIG Version 1 Release 6 - 2015-01-23](http://iase.disa.mil/stigs/os/unix-linux/Pages/index.aspx).

Inspiration and some config files taken from [RedHatGov](https://github.com/RedHatGov) [stig-fix-el6](https://github.com/RedHatGov/stig-fix-el6).

This repo was created by [David Morse](https://github.com/dsmorse) but is derived from work previously done by [Sam Doran](https://github.com/samdoran/ansible-role-stig)

Requirements
------------

You should carefully read through the tasks to make sure these changes will not break your systems before running this playbook.

For example: The scripts expect certain packages to be installed.  It can update all yum packages that are installed and will
 attempt to install ntp, postfix, tmux, openswan if they are not present. If you enable the variables

Role Variables
--------------
There are many role variables defined in defaults/main.yml. This list shows the most important.

**cent7stig_cat1**:           Correct CAT I findings (Default: true)
**cent7stig_cat2**:           Correct CAT II findings (Default: false)
**cent7stig_cat3**:           Correct CAT III findings (Default: false)
**cent7stig_fullauto**:       Run the role without pausing (Default: true)
**cent7stig_install_packages**:      Allow the script to install new packages available via yum (Default: false)
**cent7stig_ipv6_in_use**       Whether or not ipv6 is in use of the target system. This is set automatically to 'true' if ipv6 is found to be in use. (Default: false)
**cent7stig_root_email_address**:          Address where system email is sent (Default: foo@baz.com)
**cent7stig_system_is_router** Whether on not the target system is acting as a router. Disables settings that would break the system if it is a acting as a router. (Default: false)
**cent7stig_tftp_required**  Whether or not TFTP is required. This will prevent the removal of `tftp` and `tftp-server` packages. It will also  reconfigure the `tftp-server` to run securely. (Default: false) 
**cent7stig_update_packages**:       Allow the script to install patches and updates to all packages available via yum (Default: false)
**cent7stig_use_dhcp**:       Whether the system should use DHCP or Static IPs. **Setting this false is dangerous.** (Default: true)
**cent7stig_xwindows_required**:           Whether or not X Windows is is use on taregt systems. Disables some changes if X Windows is not in use. (Default: false)

#### Dependencies
**cent7stig_bootloader_password**: The new grub password to use if cent7stig_change_grub_password is **TRUE** (Default: randomly generated and encrypted string)
**cent7stig_change_grub_password**: Whether or not to update the grub password even if a hash already exists in `/boot/grub/grub.conf`. (Default: false)


Dependencies
------------

Ansible > 1.8

Example Playbook
-------------------------

Make sure to include the vars_prompt section in your playbook. It is needed for the tasks that set the grub password.

    - hosts: servers
      sudo: yes

      roles:
         - { role: cent7-STIG, cent7stig_cat1: true, cent7stig_cat2: true, cent7stig_cat3: false, cent7stig_update_packages: false, cent7stig_install_packages: true  }


Tags
----
Many tags are available for precise control of what is and is not changed. When running this playbook with tags, always include the `prelim_tasks` tag. This will run all the setup tasks that gather information and set variables used by subsequest tasks. If run without `prelim_tasks`, certain tasks may fail.

Some examples of using tags:

    # Don't run any unneeded audit tests (do it fast)
    ansible-playbook site.yml --skip-tags audit  

    # Only remediate ssh
    ansible-playbook site.yml --tags= prelim_tasks,ssh 

    # Don't change SNMP or postfix
    ansible-playbook site.yml --skip-tags= audit,postfix,mail,snmp


License
-------

MIT

