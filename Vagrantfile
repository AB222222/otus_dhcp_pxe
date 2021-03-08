Vagrant.configure("2") do |config|

Vagrant.configure("2") do |config|
    config.vm.box = 'centos/7'
    config.disksize.size = '30GB'
    config.vm.network "private_network", type: "dhcp", name: "PXEnetwork"
end

config.vm.define "webserver" do |webserver|
    webserver.vm.box = "centos/7"
    webserver.vm.network "private_network", ip: "192.168.10.22"
    webserver.vm.hostname = "webserver"
    webserver.vm.provider :virtualbox do |vb|
      end

    webserver.vm.provision "shell", inline: <<-SHELL
       yum install -y epel-release nano wget httpd
       systemctl start httpd; systemctl enable httpd
       wget https://mirror.yandex.ru/centos/8.3.2011/isos/x86_64/CentOS-8.3.2011-x86_64-dvd1.iso
       mkdir /mnt/centos8-install/
       mount -o loop,ro -t iso9660 CentOS-8.3.2011-x86_64-dvd1.iso /mnt/centos8-install/
       cp -r /mnt/centos8-install/ /var/www/html/
       systemctl restart httpd.service
       chmod -R 755 /var/www/html/centos8-install/
    SHELL

end

 

config.vm.define "dhcpserver" do |dhcpserver|
    dhcpserver.vm.box = "centos/7"
    dhcpserver.vm.network "private_network", ip: "192.168.10.23"
    dhcpserver.vm.hostname = "dhcpserver"
    dhcpserver.vm.provider :virtualbox do |vb|
       end


    dhcpserver.vm.provision "shell", inline: <<-SHELL
       yum install -y epel-release wget nano dracut-network xinetd tftp tftp-server dhcp
       firewall-cmd --add-service=tftp --permanent
       cp /vagrant/dhcpd.conf /etc/dhcp/
       cp /vagrant/tftp /etc/xinetd.d/
       mkdir -p /var/lib/tftpboot/pxelinux.cfg
       chmod 755 /var/lib/tftpboot/pxelinux.cfg
       cp /vagrant/default /var/lib/tftpboot/pxelinux.cfg/       
       wget http://192.168.10.22/centos8-install/BaseOS/Packages/syslinux-tftpboot-6.04-4.el8.noarch.rpm
       rpm2cpio /home/vagrant/syslinux-tftpboot-6.04-4.el8.noarch.rpm | cpio -dimv
       cp /home/vagrant/tftpboot/ldlinux.c32 /home/vagrant/tftpboot/libutil.c32 /home/vagrant/tftpboot/menu.c32 /home/vagrant/tftpboot/pxelinux.0 /var/lib/tftpboot/
       wget http://192.168.10.22/centos8-install/images/pxeboot/initrd.img
       wget http://192.168.10.22/centos8-install/images/pxeboot/vmlinuz
       cp /home/vagrant/initrd.img /home/vagrant/vmlinuz /var/lib/tftpboot/
       sudo setenforce 0 
       systemctl start dhcpd
       systemctl enable dhcpd
       systemctl start tftp
       systemctl enable tftp
       systemctl start xinetd
       systemctl enable xinetd
    SHELL

end

end
