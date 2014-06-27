# aspects_unattended_upgrades

Install and configure the unattended upgrades utility.

Note: This role will stomp any configuration in the /etc/apt/apt.conf.d/10periodic file. I have never run into anything other than unattended-upgrades that uses that file, but you should be aware it isn't necessarily a file only used by unattended-upgrades. Pull requests to make this role not stomp non-unattended-upgrade configuration would be awesome.

# Requirements
Set ```hash_behaviour=merge``` in your ansible.cfg file.

# Role Variables

## aspects_unattended_upgrades_periodic_config
Configure periodic apt tasks.
### unattendedupgrade
APT::Periodic::Unattended-Upgrade "0";

- Run the "unattended-upgrade" security upgrade script every n-days (0=disabled)

Role Default: 1
### updatepackagelists
APT::Periodic::Update-Package-Lists "0";

- Do "apt-get update" automatically every n-days (0=disable)

Role Default: 1
### downloadupgradeablepackages
APT::Periodic::Download-Upgradeable-Packages "0";

- Do "apt-get upgrade --download-only" every n-days (0=disable)

Role Default: 0
### autocleaninterval
APT::Periodic::AutocleanInterval "0";

- Do "apt-get autoclean" every n-days (0=disable)

Role Default: 7
## aspects_unattended_upgrades_main_config
### allowed_origins:
####   security
Enable or disable security updates.

Values: yes|no

Role Default: yes
####   updates
Enable or disable non-security updates.

Values: yes|no

Role Default: no
####   proposed
Enable or disable proposed updates.

Values: yes|no

Role Default: no
####   backports
Enable or disable backport updates.

Values: yes|no

Role Default: no
### package_blacklist:
A dictionary of packages that should not be automatically upgraded.

Values: key: "packagename"

Use a unique key so that you can override individual packages in your vars.

Role Default: {}

See the defaults/main.yml file for commented out examples.
### notify_email
Send emails when upgrades run.
#### enable
Values: yes|no

Role Default: no
#### email
Values: Any valid email address.

Role Default: root@localhost
#### only_on_error
Values: true|false

Role Default: false

### Other variables
For the following, check defaults/main.yml for the default values, and the template file for what options are available.
* auto_fix_interrupted_dpkg
* minimal_steps
* install_on_shutdown
* autoremove
* autoreboot
* bandwidth_limit

# Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.aspects_unattended_upgrades }
      vars:
        aspects_unattended_upgrades_periodic_config:
          unattendedupgrade: 1
        aspects_unattended_upgrades_main_config:
          allowed_origins:
            security: yes
            updates: yes
          notify_email:
              enable: yes
              email: "admin@example.org"

# License

MIT