# Bioinformatics development servers

## Deploying

These variables can vary in deploy:
- ${your-rstudio-port}
- ${your-netapp-address}
- ${your-netapp-share}
- ${admin-uid} (from IT)
- ${admin-gid} (from IT)

There are 2 VMs from KTH, which connect via SMB to a storage share for data on storage3. Since homedirs and docker are tricky to run on SMB,
there is a 200G LVM disk on each VM. Note that there is thus no sharing of homedirs (or cores) between the two VMs. Homedirs are not supposed to
be used for largish data storage.

### To set it up per VM:
- Login to VM
- Create group as we are not syncing groups with e.g. LDAP: `sudo addgroup --gid ${admin-gid} storagegroup`
- Create smb credentials file, `/root/.smbcredentials` containing `user=XXXX, password=XXXXX, domain=SCILIFELAB` lines for your admin user
- Install SMB `sudo apt install cifs-utils`
- put in fstab:
  `//${your-netapp-address}/${your-netapp-share} 	/srv/storage	cifs    uid=${admin-uid},gid=${admin-gid},seal,dir_mode=0770,file_mode=0774,noperm,credentials=/root/.smbcredentials,iocharset=utf8,vers=3.0,_netdev,mfsymlinks 0 0`
- Install rstudio (according to rstudio webpage)
- Write in /etc/rstudio/rserver.conf the line: `www-port=${your-port}`
- Install docker (according to docker website)
- Enable host networking so docker wont interfere with firewall, in `/etc/docker/daemon.json` put the key `{ "iptables": false }`
- Open firewall to rstudio, for each VPN IP: `sudo ufw allow from ${vpn-ip} to any port ${your-rstudio-port}`


### Storage dir for a new user:
```
mkdir /srv/storage/${username}
```

### For a new user on each VM:
```
sudo adduser ${username} # set password etc
sudo adduser ${username} docker
sudo adduser ${username} storagegroup
sudo ln -s /srv/storage/username /home/${username}/storage
```
