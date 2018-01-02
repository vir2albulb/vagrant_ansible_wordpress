# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'json'
schema = "#{Dir.pwd}/files/config.json"
instances = JSON.parse(File.read(schema))

Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'
  config.vm.provider 'virtualbox' do |vb|
    vb.name = 'vagrant'
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
        ansible.verbose = 'v'
        ansible.limit = 'all'
        ansible.groups = {}
        v['playbooks'].each do |i|
          ansible.groups[i] = [ (v['hostname']).to_s ]
        end
      end
    end
  end
end
