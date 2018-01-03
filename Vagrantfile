# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'json'
schema = "#{Dir.pwd}/files/config.json"
instances = JSON.parse(File.read(schema))

# set up seboolean network acess
allow_connect_remote_db = 'yes'
# get db ip to configure MariaDB and Wordpress
db_ip = web_ip = ''
instances['instances'].each do |v|
  v['playbooks'].each do |p|
    if p.to_s == 'database'
      db_ip =  (v['ip_address']).to_s
    elsif p.to_s == 'webserver'
      web_ip = (v['ip_address']).to_s
    end
  end
end

if db_ip == '' || db_ip == web_ip
  db_ip = web_ip = 'localhost'
  allow_connect_remote_db = 'no'
end

Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'
  config.vm.provider 'virtualbox' do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  instances['instances'].each do |v|
    config.vm.define (v['hostname']).to_s do |k|
      k.vm.hostname = (v['hostname']).to_s
      k.vm.network 'private_network', ip: (v['ip_address']).to_s
      k.vm.provision 'shell',
        inline: "nmcli con add con-name hostOnly type ethernet ifname eth1 \
        ip4 #{v['ip_address']}/24; ifup eth1"

      k.vm.provision 'ansible' do |ansible|
        ansible.playbook = 'provisioning/playbook.yml'
        ansible.limit = 'all'
        ansible.vault_password_file = "#{File.expand_path('~')}/.vault_pass"
        ansible.groups = {}
        v['playbooks'].each do |i|
          ansible.groups[i] = [ (v['hostname']).to_s ]
        end
        ansible.extra_vars = {
          'mariadb_ip' => db_ip,
          'webserver_ip' => web_ip,
          'can_connect_network_db' => allow_connect_remote_db
        }
      end
    end
  end
end
