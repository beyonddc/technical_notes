# SSH forwarding doesn't work
* Check /etc/ssh/sshd_config and ensure it has the following line 'AddressFamily inet'

# Install nodejs on Ubuntu 16.x
* curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
* sudo apt-get install -y nodejs
