# How To: Change the IP Address of a Proxmox Clustered Node
### If changing entire subnet:

(e.g. 192.168.1.0/24 to 192.168.2.0/24)

```
# Stop the cluster services
systemctl stop pve-cluster
systemctl stop corosync

# Mount the filesystem locally
pmxcfs -l

# Edit the network interfaces file to have the new IP information
# Be sure to replace both the address and gateway
vi /etc/network/interfaces

# Replace any host entries with the new IP addresses
vi /etc/hosts

# Change the DNS server as necessary
vi /etc/resolv.conf

# Edit the corosync file and replace the old IPs with the new IPs for all hosts
# :%s/192\.168\.1\./192.168.2./g   <- vi command to replace all instances
# BE SURE TO INCREMENT THE config_version: x LINE BY ONE TO ENSURE THE CONFIG IS NOT OVERWRITTEN
vi /etc/pve/corosync.conf

# Edit the known hosts file to have the correct IPs
# :%s/192\.168\.1\./192.168.2./g   <- vi command to replace all instances
/etc/pve/priv/known_hosts

# If using ceph, edit the ceph configuration file to reflect the new network
# (thanks u/FortunatelyLethal)
# :%s/192\.168\.1\./192.168.2./g   <- vi command to replace all instances
vi /etc/ceph/ceph.conf

# If you want to be granular... fix the IP in /etc/issue
vi /etc/issue

# Verify there aren't any stragglers with the old IP hanging around
cd /etc
grep -R '192\.168\.1\.' *
cd /var
grep -R '192\.168\.1\.' *

# Reboot the system to cleanly restart all the networking and services
reboot

# Referenced pages:
# - https://forum.proxmox.com/threads/change-cluster-nodes-ip-addresses.33406/
# - https://pve.proxmox.com/wiki/Cluster_Manager#_remove_a_cluster_node
```
### If changing only the IP:

(e.g. 192.168.1.10/24 to 192.168.1.20/24)

```
# Stop the cluster services
systemctl stop pve-cluster
systemctl stop corosync

# Mount the filesystem locally
pmxcfs -l

# Edit the network interfaces file to have the new IP information
vi /etc/network/interfaces

# Replace any host entries with the new IP addresses
vi /etc/hosts

# Edit the corosync file and replace the old IPs with the new IPs for any changed hosts
# BE SURE TO INCREMENT THE config_version: x LINE BY ONE TO ENSURE THE CONFIG IS NOT OVERWRITTEN
vi /etc/pve/corosync.conf

# Edit the known hosts file to have the new IP(s)
/etc/pve/priv/known_hosts

# If using ceph, edit the ceph configuration file to reflect the new network
# (thanks u/FortunatelyLethal)
# :%s/192\.168\.1\./192.168.2./g   <- vi command to replace all instances
vi /etc/ceph/ceph.conf

# If you want to be granular... fix the IP in /etc/issue
vi /etc/issue

# Verify there aren't any stragglers with the old IP hanging around
cd /etc
grep -R '192\.168\.1\.' *
cd /var
grep -R '192\.168\.1\.' *

# Reboot the system to cleanly restart all the networking and services
reboot

# Referenced pages:
# - https://forum.proxmox.com/threads/change-cluster-nodes-ip-addresses.33406/
# - https://pve.proxmox.com/wiki/Cluster_Manager#_remove_a_cluster_node
```