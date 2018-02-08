# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "genebean/centos-7-puppet5"
  config.vm.hostname = "vmwarestats.localdomain"
  config.vm.network "forwarded_port", guest: 3000, host: 3000

  config.vm.provision "shell", inline: <<-SCRIPT
    # InfluxDB setup
    puppet resource yumrepo influxdb ensure=present descr='InfluxDB Repository - RHEL \$releasever' baseurl='https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable' enabled=1 gpgcheck=1 gpgkey='https://repos.influxdata.com/influxdb.key'
    yum -y install influxdb
    systemctl enable influxdb
    systemctl start influxdb
    sleep 5

    /usr/bin/influx -execute 'CREATE DATABASE vmware_performance'
    /usr/bin/influx -execute 'CREATE DATABASE telegraf'

    # Stats grabbing program
    curl -L -O $(curl -s https://api.github.com/repos/Oxalide/vsphere-influxdb-go/releases | grep browser_download_url | grep '64[.]rpm' | head -n 1 | cut -d '"' -f 4)
    rpm -i vsphere-influxdb-go*.rpm
    puppet resource file '/etc/vsphere-influxdb-go.json' ensure='link' target='/vagrant/vsphere-influxdb-go.json'
    puppet resource cron get-vmware-stats ensure=present command='/usr/local/bin/vsphere-influxdb-go' target='root' user='root'
    /usr/local/bin/vsphere-influxdb-go

    # Grafana setup
    puppet resource yumrepo grafana ensure=present baseurl='https://packagecloud.io/grafana/stable/el/7/$basearch' descr='grafana' enabled=1 gpgcheck=1 gpgkey='https://packagecloud.io/gpg.key https://grafanarel.s3.amazonaws.com/RPM-GPG-KEY-grafana' repo_gpgcheck=1 sslcacert='/etc/pki/tls/certs/ca-bundle.crt' sslverify=1
    yum -y install grafana
    cp /vagrant/grafana.db /var/lib/grafana/grafana.db
    chown -R grafana:grafana /var/lib/grafana/
    /sbin/grafana-cli plugins install grafana-piechart-panel
    systemctl enable grafana-server
    systemctl start grafana-server

    yum -y install fontconfig freetype* urw-fonts

    # Telegraf
    yum -y install telegraf
    puppet resource file '/etc/telegraf/telegraf.conf' ensure='link' target='/vagrant/telegraf.conf'
    systemctl enable telegraf
    systemctl start telegraf
  SCRIPT

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 2
  end
end
