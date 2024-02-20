ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'
Vagrant.configure("2") do |config|
  config.vm.network "public_network"
  
   config.vm.define "sf-centos-pg84" do | pg84 |
     pg84.vm.hostname = "sf-cenots8-pg84"
     pg84.vm.disk :disk, size: "30GB", primary: true
     pg84.vm.box = "centos/8"
     pg84.vm.provision "shell", inline: <<-SHELL
       sed -i 's/^mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
       sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
       sed -i s/^SELINUX=.*$/SELINUX=disabled/ /etc/selinux/config
       setenforce 0
       yum update -y
       yum install -y wget vim gcc readline readline-devel  zlib zlib-devel make
       useradd postgres; echo "postgres ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/postgres
       yes postgres | passwd postgres
       mkdir /opt/data; sudo chown -R postgres:postgres /opt/data
       mkdir /var/log/pgsql; sudo chown -R postgres:postgres /var/log/pgsql;
       wget https://ftp.postgresql.org/pub/source/v8.4.22/postgresql-8.4.22.tar.gz
       tar -zxvf postgresql-8.4.22.tar.gz
       cd postgresql-8.4.22; ./configure; gmake; gmake install; gmake clean;
       su -c 'echo "PATH=/usr/local/pgsql/bin:\$PATH; export PATH; LD_LIBRARY_PATH=/usr/local/pgsql/lib; export LD_LIBRARY_PATH" >> ~/.bashrc; source ~/.bashrc; /usr/local/pgsql/bin/initdb -D /opt/data;' postgres
       sed -i "s/^#listen_addresses =.*/listen_addresses='*'/" /opt/data/postgresql.conf 
       su -c 'echo "host    all         all         0.0.0.0/0             md5" >> /opt/data/pg_hba.conf' postgres
       su -c 'source ~/.bashrc; pg_ctl start -D /opt/data -l /var/log/pgsql/pg.log' postgres
        
     SHELL
       pg84.vm.provider :virtualbox do |v|
         v.memory = "4096"
         v.cpus = "2"
         v.name = "sf-cenots8-pg84"
       end
   end

end
