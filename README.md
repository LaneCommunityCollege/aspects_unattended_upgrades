# aspects_unattended_upgrades

Install `unattended-upgrades` via `aspects_packages`, and configure it via the `/etc/apt/apt.conf.d/{10periodic, 20auto-upgrades, 50unattended-upgrades}` files.

# Requirements
Set ```hash_behaviour=merge``` in your ansible.cfg file.

# Setup
If you chose not to use `aspects_packages`, you will need to install `unattended-upgrades` before running this role.

# Role Variables
What I consider reasonable defaults are configured in [defaults/main.yml](defaults/main.yml). Check the templates for how they are used. 

## aspects_unattended_upgrades_timer_override
If you don't like the default timing of the apt-daily tasks, you need to override
the `apt-daily.timer` file.

Just use something like:

```yaml
aspects_unattended_upgrades_timer_override:
  enabled: true
  block: |
    [Timer]
    OnCalendar=*-*-* 1:40
    RandomizedDelaySec=45m
```
If you change your mind, setting enabled to false will remove the override directory and file.

# Example Playbook

```yaml
- hosts:
    - aspects_unattended_upgrades
  #  - aspectscentos7
  #  - aspectstrusty
  #  - aspectsxenial
  #  - aspectsstretch
  #  - aspectsoraclelinux7
  #  - aspectbionic
  vars:
    ansible_become: True
    aspects_unattended_upgrades_enabled: True
    aspects_packages_enabled: True

    # Config in /etc/apt/apt.conf.d/10periodic
    # Template file: etc-apt-apt.conf.d-10periodic.j2
    aspects_unattended_upgrades_periodic_config:
      enable: # For APT::Periodic::Enable
        enabled: True
        value: "0"
      downloadupgradeablepackages:
        enabled: True
        value: "0"
      autocleaninterval:
        enabled: True
        value: "7"

    # Config in /etc/apt/apt.conf.d/20auto-upgrades
    # Template file: etc-apt-apt.conf.d-20auto-upgrades.j2
    aspects_unattended_upgrades_auto_upgrades_config:
      update_package_lists:
        enabled: True
        value: "1"
      unattended_upgrade:
        enabled: True
        value: "1"

    # Config in /etc/apt/apt.conf.d/50unattended-upgrades
    # template file: etc-apt-apt.conf.d-50unattendedupgrades.j2
    aspects_unattended_upgrades_main_config:
      allowed_origins:
        security: True
        updates: False
        proposed: False
        backports: False
      package_blacklist:
        enabled: False
        list: {}
      #    vim: "vim"
      #    libc6: "whitelist"
      auto_fix_interrupted_dpkg:
        enabled: False
        value: "true"
      minimal_steps:
        enabled: True
        value: "false"
      install_on_shutdown:
        enabled: True
        value: "false"
      notify_email:
        enabled: True
        email: "root@localhost"
        only_on_error: "false"
      remove_unused_kernel_packages:
        enabled: False
        value: "false"
      autoremove:
        enabled: True
        value: "false"
      autoreboot:
        enabled: True
        value: "false"
      automatic_reboot_time:
        enabled: True
        value: "now"
      automatic_reboot_with_users:
        enabled: False
        value: "true"
      bandwidth_limit:
        enabled: True
        value: "false"
      syslog_enable:
        enabled: False
        value: "false"
      syslog_facility:
        enabled: False
        value: "daemon"
    aspects_unattended_upgrades_timer_override:
      enabled: true
      block: |
        [Timer]
        OnCalendar=*-*-* 11:40
        RandomizedDelaySec=45m
  roles:
    - aspects_unattended_upgrades
```
# License

MIT