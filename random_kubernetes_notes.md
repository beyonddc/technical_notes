# Install microk8s on RHEL 7.x
* yum install yum-plugin-copr
* yum copr enable ngompa/snapcore-el7
* yum install libzstd squashfuse  squashfuse-libs
* yum install snapd
* ln -s /var/lib/snapd/snap /snap
* systemctl enable snapd.socket
* systemctl start snapd.socket
* setenforce 0
* snap install microk8s --classic
* export PATH=${PATH}:/var/lib/snapd/snap/bin
