# dynhost-ovh
A simple (cron) script to update DynHost on OVH hosting

# Prerequisites
This script works with two linux commands : curl and dig.
If you do not have the dig you must install it from the package "dnsutils" :
```sh
sudo apt-get update
sudo apt-get install dnsutils
```

# How to use
1. Clone to your remote machine:

```bash
mkdir ~/scripts && cd ~/scripts
git clone https://github.com/silentbugs/dynhost-ovh

# create symlink to locally cloned script
mkdir -p /usr/local/bin/ovh/
ln -s /home/$USERNAME/scripts/dynhost-ovh/dynhost.sh /usr/local/bin/ovh/dynhost.sh
```

2. Create cron entry `/etc/cron.d/dynhost` with the following:

```bash
# /etc/cron.d/dynhost: update local dynamic ip to be used by remotes
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# update IP every minute
*/1 * * * * root /usr/local/bin/ovh/dynhost.sh
```

3. Modify the configuration file `dynhost.sh.config` with the fllowing available variable overrides:

```bash
HOST='dynhost.example.com'
LOGIN='example.com-username'
PASSWORD='password'
```

4. Ensure `logrotate` is installed and add the following logrotate entry to `/etc/logrotate.d/dynhost`:

```bash
/var/log/dynhost.log {
  rotate 12
  monthly
  compress
  delaycompress
  missingok
  notifempty
}
```

# How it works
1. The command dig is used to retrieve the IP address of your domain name.
2. The command curl (with the website ifconfig.co) is used to retrieve the current public IP address of your machine.
3. The two IPs are compared and if necessary a curl command to OVH is used to update your DynHost with your current public IP address.
4. Log file is on ```/var/log/dynhost.log```
