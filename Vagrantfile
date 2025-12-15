# Vagrantfile
# Vagrant 2.x, VirtualBox provider

# Configurable number of worker nodes
WORKER_COUNT = ENV.fetch("WORKER_COUNT", 2).to_i

# Configurable controller
CTRL_CPUS   = ENV.fetch("CTRL_CPUS",   1).to_i
CTRL_MEMORY = ENV.fetch("CTRL_MEMORY", 4096).to_i  # MB

# Configurable worker
WORKER_CPUS   = ENV.fetch("WORKER_CPUS",   2).to_i
WORKER_MEMORY = ENV.fetch("WORKER_MEMORY", 6144).to_i

# Base box
BASE_BOX = "bento/ubuntu-24.04"

# Network base
NETWORK_BASE = "192.168.56"

# Should be fixed, assignment 2 requires it
CTRL_IP_SUFFIX = 100
WORKER_IP_START = 101

Vagrant.configure("2") do |config|
  config.vm.box = BASE_BOX

  # Define shared folder for volume mounting the model
  config.vm.synced_folder "./model", "/mnt/model", type: "virtualbox"

  # Global SSH settings
  config.ssh.insert_key = false

  # Controller node
  config.vm.define "ctrl" do |ctrl|
    ctrl.vm.hostname = "ctrl"

    ctrl.vm.network "private_network", ip: "#{NETWORK_BASE}.#{CTRL_IP_SUFFIX}"

    ctrl.vm.provider "virtualbox" do |vb|
      vb.name   = "k8s-ctrl"
      vb.cpus   = CTRL_CPUS
      vb.memory = CTRL_MEMORY   # 4 GB
    end
  end

  # Worker nodes
  (1..WORKER_COUNT).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.hostname = "node-#{i}"
      
      # Fixed IP of worker nodes      
      worker_ip_suffix = WORKER_IP_START + (i - 1) 

      node.vm.network "private_network", ip: "#{NETWORK_BASE}.#{worker_ip_suffix}"

      node.vm.provider "virtualbox" do |vb|
        vb.name   = "k8s-node-#{i}"
        vb.cpus   = WORKER_CPUS
        vb.memory = WORKER_MEMORY  
      end
    end
  end

  # Copy Vagrant's auto-generated inventory to inventory.cfg
  config.trigger.after :up, :provision do |trigger|
    trigger.name = "Generate inventory.cfg"
    trigger.ruby do |env, machine|
      vagrant_inventory = ".vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory"
      if File.exist?(vagrant_inventory)
        # Read Vagrant's auto-generated inventory
        content = File.read(vagrant_inventory)
        
        # Write to inventory.cfg
        File.write("ansible/inventory.cfg", content)
        puts "Generated ansible/inventory.cfg from Vagrant's auto-generated inventory"
      end
    end
  end

  # Provision each VM as it comes up
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/site.yml"
    ansible.groups = {
      "ctrl" => ["ctrl"],
      "workers" => (1..WORKER_COUNT).map { |i| "node-#{i}" }
    }
    
    ansible.extra_vars = {
      "ctrl_ip" => "#{NETWORK_BASE}.#{CTRL_IP_SUFFIX}",
      "worker_ips" => (1..WORKER_COUNT).map { |i| "#{NETWORK_BASE}.#{WORKER_IP_START + (i - 1)}" }
    }
  end

end
