[![Build Status](https://travis-ci.org/pmbauer/ansible-tarsnap.svg?branch=master)](https://travis-ci.org/pmbauer/ansible-tarsnap)

# ansible-tarsnap

Is ...

  - An ansible role for operating tarsnap backups
  - An [ansible-pull] compatible playbook for standalone installations.

The role downloads sources for, verifies the gpg-encrypted sha signature, compiles, and installs [Tarsnap].  
**Batteries included**: [tarsnapper], cron job, shell wrapper, and [logrotate] policy.

## version

1.0

## requirements
- [Tarsnap] account
- [Tarsnap] machine key file
    - instructions to generate one are in the [standalone](#standalone) section
    - **important** this role assumes you are responsible for copying this key to `tarsnap_keyfile`.  See [role variables](#role variables) 

## role variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    tarsnap_version: 1.0.35
    tarsnap_keyfile: "/root/tarsnap.key"
    tarsnap_cache: "/usr/local/tarsnap-cache"

The role-installed `.tarsnaprc` file will automatically exclude the `tarsnap_cache` folder and reference the `tarsnap_keyfile` for backups.  Your play or an out-of-band process is responsible for creating the keyfile and placing it at this location.  See the [standalone](#standalone) section.

    tarsnap_cron_minute: "0"
    tarsnap_cron_hour: "3"
    tarsnap_cron_day: "*"
    tarsnap_cron_month: "*"

Controls the period at which the backup job wrapper `/root/tarsnap.sh` is executed.  
The period should be at least as frequent as the smallest generation defined by your `tarsnapper.conf` deltas (e.g. if your smallest delta is 1h, you should set all these vars to `*` except for `tarsnap_cron_minute`).  
These variable map to parameters in the [ansible cron module].

    tarsnap_tarsnapper_conf: "tarsnapper.default.conf"

[tarsnapper] configuration. A default and example are provided ([tarsnapper.default.conf]) but you should over-ride this.

    tarsnap_global_exclusions: # not defined by default

List of string glob patterns that will be added to the `.tarsnaprc` file.  For an example list, see `personal/defaults.yml`

## standalone

The `local.yml` play uses the role to install a local backup policy, either via manual execution or [ansible-pull].

You should fork this repo before executing locally since the exclusion patterns and `tarsnapper.conf` as defined in the `personal` directory need to be customized.

```bash
# installs role locally, using the exclusion patterns and tarsnapper.conf
# defined in the "personal" directory
ansible-playbook -i /dev/null local.yml

# Now that tarsnap is installed, generate a tarsnap machine key.
# This will prompt for your tarsnap.com account password
sudo tarsnap-keygen --keyfile /root/tarsnap.key --user me@example.com --machine `hostname`

# alternatively, copy your pre-registered machine key to the location defined in the tarsnap_keyfile var
```

## license
Source Copyright © 2014 Paul Bauer. Distributed under the GNU General Public License v3, the same as Ansible uses. See the file COPYING.

[Ansible]:http://www.ansible.com/home
[ansible-pull]:http://linux.die.net/man/1/ansible-pull
[ansible cron module]:http://docs.ansible.com/cron_module.html
[logrotate]:http://linuxcommand.org/man_pages/logrotate8.html
[Tarsnap]:https://www.tarsnap.com/
[sovereign]:https://github.com/al3x/sovereign
[tarsnapper]:https://github.com/miracle2k/tarsnapper
[tarsnapper.default.conf]:https://github.com/pmbauer/ansible-tarsnap/tree/master/files/tarsnapper.default.conf
