$hostnamepuppetserver = "xpuakhaw0014t1"
$ipaddresspuppetserver = "192.168.99.101"
Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
    config.vm.define "puppetserver" do |puppetserver|
    puppetserver.vm.hostname = $hostnamepuppetserver
    puppetserver.vm.network "private_network", ip: $ipaddresspuppetserver
    puppetserver.vm.provider :virtualbox do |server|
      server.name = "PuppetServer"
      server.memory = 3096
      server.cpus = 2
    end
    puppetserver.vm.provision "shell", inline: <<-SHELL
      yum update
      yum install -y ntp
      timedatectl set-timezone Europe/Kiev
      ntpdate pool.ntp.org
      sed -i 's/server 0.centos.pool.ntp.org/server 0.ua.pool.ntp.org/g' /etc/ntp.conf
      sed -i 's/server 1.centos.pool.ntp.org/server 1.ua.pool.ntp.org/g' /etc/ntp.conf
      sed -i 's/server 2.centos.pool.ntp.org/server 2.ua.pool.ntp.org/g' /etc/ntp.conf
      sed -i 's/server 3.centos.pool.ntp.org/server 3.ua.pool.ntp.org/g' /etc/ntp.conf
      systemctl restart ntpd
      systemctl enable ntpd
      rpm -ivh https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
      yum -y install puppetserver
#      sed -i 's/-Xms2g -Xmx2g/-Xms3g -Xmx3g/g' /etc/sysconfig/puppetserver
      systemctl start puppetserver
      systemctl enable puppetserver
    SHELL
  end
  config.vm.define "puppetagent" do |puppetagent|
    puppetagent.vm.hostname = "xpuakhaw0014t2"
    puppetagent.vm.network "private_network", ip: "192.168.99.102"
    puppetagent.vm.provider :virtualbox do |agent|
      agent.name = "PuppetAgent1"
      agent.memory = 1024
      agent.cpus = 2
    end
    puppetagent.vm.provision "shell", inline: <<-SHELL
      yum update
      yum install -y ntp mc
      timedatectl set-timezone Europe/Kiev
      ntpdate pool.ntp.org
      sed -i 's/server 0.centos.pool.ntp.org/server 0.ua.pool.ntp.org/g' /etc/ntp.conf
      sed -i 's/server 1.centos.pool.ntp.org/server 1.ua.pool.ntp.org/g' /etc/ntp.conf
      sed -i 's/server 2.centos.pool.ntp.org/server 2.ua.pool.ntp.org/g' /etc/ntp.conf
      sed -i 's/server 3.centos.pool.ntp.org/server 3.ua.pool.ntp.org/g' /etc/ntp.conf
      systemctl restart ntpd
      systemctl enable ntpd
      rpm -ivh https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
      yum -y install puppet-agent
      echo "#{$ipaddresspuppetserver} #{$hostnamepuppetserver}" >> /etc/hosts
      if  grep -Fxq "[main]" /etc/puppetlabs/puppet/puppet.conf
      then
      echo "Already addded"
      else
      echo """
[main]
certname = #{puppetagent.vm.hostname}
server = #{$hostnamepuppetserver}
environment = production
runinterval = 1h
""" >> /etc/puppetlabs/puppet/puppet.conf
      fi
      /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true
    SHELL
    end
end
