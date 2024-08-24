# Setting up
## Raid with mdadm
- Can do raid easily while setting up the Ubuntu boot
- Need to populate config files for mdadm - `/etc/mdadm/mdadm.conf` and `/etc/fstab` to avoid issues when rebooting
	- https://arstechnica.com/civis/threads/raid-array-timing-out-at-boot.1433939/
```
# Blow away the superblocks on all the drives

sudo sgdisk -Z /dev/sda
sudo sgdisk -Z /dev/sdb
sudo sgdisk -Z /dev/sdc
sudo sgdisk -Z /dev/sdd

# Confirm that the drives are totally bare

fdsik -l /dev/sda
fdsik -l /dev/sdb
fdsik -l /dev/sdc
fdsik -l /dev/sdd

# Confirm the drives to use (just to be safe):

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT

# Create the array

# sudo mdadm --create --verbose /dev/md0 --level=6 --raid-devices=4 /dev/sda /dev/sdb /dev/sdc /dev/sdd

# Confirm the array is building

cat /proc/mdstat

# Create and mount the filesystem

sudo mkfs.ext4 -F /dev/md0
sudo mount /dev/md0 /srv

# Confirm the new drive is mounted

df -h -x devtmpfs -x tmpfs

# Wait for the array to be fully assembled.  This takes hours.  You can watch the progress with this:

watch cat /proc/mdstat

# When the array is all the way built, save the array configuration

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u
echo '/dev/md0 /srv ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
```
# Hosting over Public Network
## Get a domain and connect to cloudflare
https://www.namecheap.com/support/knowledgebase/article.aspx/9607/2210/how-to-set-up-dns-records-for-your-domain-in-a-cloudflare-account/
## Set up DDNS 
- IP Changes sometimes, so need to set up DDNS so cloudflare is updated on what your IP is
## Set up Static Internal IP
- https://tecadmin.net/how-to-configure-static-ip-address-on-ubuntu-22-04/
## Things to configure in router
1. Make sure you have dedicated IP
	1. Login PLDT Admin 192.168.1.1/fh
		1. Creds- adminpldt:Admin12345!!
		2. Remove optic cable, then restart so you can login
	2. Check IP under DCHP and compare to who.is
	3. If none, can follow this guide https://www.youtube.com/watch?v=JOyQwBb07S0
2. Port forward 80 and 443 from Router. Should be under Applications
	1. Use the Internal IP of your server as private IP
## Use Reverse Proxy
- I used Traefik, many guides, but this helped
	- Video https://www.youtube.com/watch?v=b83S_N1kkJM&ab_channel=DBTech
	- Blog https://dbtechreviews.com/2020/03/18/how-to-install-and-setup-traefik-with-cloudflare-using-your-own-domain-name/
- Cloudflare Zero Trust for oauth
	- https://www.youtube.com/watch?v=wdmbAo02ktQ&ab_channel=DBTech
# Services
## 1 Samba file sharing
1. Make Ubuntu User
2. Create folder for User
3. Using Samba, create user
4. On windows, 
	1. Right click PC > Map network drive > put in credentials
5. Make sure to click reconnect on sign-in

## 2 Immich
1. Google Photos to Immich
	1. Guide https://www.youtube.com/watch?v=rf25QQaFj0M&ab_channel=ChuckBuilds
	2. Repo https://github.com/simulot/immich-go
	3. Command
		1. `./immich-go.exe -server=http://192.168.1.7:2283 -key=<key> upload -create-albums -google-photos takeout-*.zip`
		1. `./immich-go.exe -server=http://192.168.1.7:2283 -key=<key> upload -create-albums -google-photos takeout-*.zip`
2. Oauth
	1. https://immich.app/docs/administration/oauth/#mobile-redirect-uri


## 3 Minecraft server
1. Just start a container for minecraft
2. Expose port 25565 on your router

# Setting up CICD
- Docker network
- Docker containers
- Check out ansible

## Todos
1. https://acouvreur.github.io/sablier/#/plugins/traefik
2. 

