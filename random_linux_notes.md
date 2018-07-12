# SSH forwarding doesn't work
* Check /etc/ssh/sshd_config and ensure it has the following line 'AddressFamily inet'

# Install nodejs on Ubuntu 16.x
* curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
* sudo apt-get install -y nodejs

# Reverting to legacy Network Naming Scheme
* Edit **/etc/default/grub** and add **net.ifnames=0 biosdevname=0** to line **GRUB_CMDLINE_LINUX**
..* For example **GRUB_CMDLINE_LINUX=" crashkernel=auto net.ifnames=0 biosdevname=0 rhgb quiet"**
* Regenerate GRUB configuration file and overwrite existing one:
..* # grub2-mkconfig -o /boot/grub2/grub.cfg
* Create **/etc/sysconfig/network-scripts/ifcg-eth0**
* Disable NetworkManager by **systemctl disable NetworkManager**
* Reboot system by **shutdown -r now**

# Enable git 2.x on RHEL 7
* You will need to use Git 2.9 by Software Collections
* On RHEL OS
..* sudo yum-config-manager --enable rhel-server-rhscl-7-rpms
..* sudo yum install rh-git29
..* scl enable rh-git29 bash
* For detail, see https://www.softwarecollections.org/en/scls/rhscl/rh-git29/
