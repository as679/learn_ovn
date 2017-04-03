# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

machines = YAML.load_file(File.join(File.dirname(__FILE__), 'machines.yaml'))

ansible_host_vars = Hash.new

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vbguest.auto_update = false

  machines.each do |machine|
    config.vm.define machine['name'] do |srv|
      srv.vm.hostname = machine['name']
      machine['nics'].each do |net|
        srv.vm.network net['type'], ip: net['ip_addr']
      end
      srv.vm.provider "virtualbox" do |vb|
        vb.cpus = machine['vcpu']
        vb.memory = machine['ram']
        vb.linked_clone = true
      end
    end
    if (defined?(machine['host_vars']))
      ansible_host_vars[machine['name']] = machine['host_vars']
    end
  end

  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'provision/provision.yml'
    ansible.extra_vars = {
      ovn_packages_dir: "/vagrant/files",
      ovn_central_ip: "10.0.69.11"
    }
    ansible.host_vars = ansible_host_vars
  end
end
