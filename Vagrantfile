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
end
