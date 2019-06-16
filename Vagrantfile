$ cd MeuAmbiente
$ vim vagrantfile
Vagrant.configure(2) do |config|
        config.vm.box = "ubuntu/trusty64" # Distribuição e Versão do SO
        config.vm.hostname = "linuxsrv01" # Hostname da VM
        config.vm.network "forwarded_port", guest: 80, host: 8081 # Encaminhamento de Porta de 80 para 8081
        config.vm.network "public_network", ip: "192.168.0.18" # Configuro IP público estático.
 
        config.vm.provider "virtualbox" do |vb|
                vb.memory = "1024" # 1GB de Memória RAM
                vb.cpus = "1" # Quantidade Core de CPU
                vb.name = "linuxsrv01" # Nome da máquina Virtual no VirtualBox
        end
  
    config.vm.provision "shell", inline: <<-SHELL
    wget http://repo.zabbix.com/zabbix/3.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.4-1+xenial_all.deb
    dpkg -i zabbix-release_3.4-1+xenial_all.deb;
    apt-get update;
    apt-get install zabbix-server-mysql zabbix-frontend-php php-bcmath php-mbstring php-xml -y;
    sed 's@;date.timezone =@date.timezone=Europe/Warsaw@' -i /etc/php/7.0/apache2/php.ini;
    echo -e "[mysqld]\ndefault-storage-engine = innodb" | sudo tee /etc/mysql/conf.d/mysqld.conf;
    service mysql restart;
    mysql -e "CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'zabbix';";
    mysql -e "create database zabbix character set utf8 collate utf8_bin;"
    mysql -e "grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';";
    zcat /usr/share/doc/zabbix-server-mysql/create.sql.gz | mysql -uzabbix -p"zabbix" zabbix;
    sed 's/# DBPassword=/DBPassword=zabbix/g' /etc/zabbix/zabbix_server.conf -i;
    service zabbix-server restart;
    service apache2 restart;
  SHELL
end
