# Vagrantfile
# Vagrant 2.x, VirtualBox provider

# Configurable number of worker nodes
WORKER_COUNT = ENV.fetch("WORKER_COUNT", 2).to_i

# Base box
BASE_BOX = "bento/ubuntu-24.04"

# Network base (adjust as you like, just stay in a private range)
NETWORK_BASE = "192.168.56"

# Should be fixed, assignment 2 requires it
CTRL_IP_SUFFIX = 100
WORKER_IP_START = 101

Vagrant.configure("2") do |config|
  config.vm.box = BASE_BOX

  # Global SSH settings
  config.ssh.insert_key = false

  # Controller node
  config.vm.define "ctrl" do |ctrl|
    ctrl.vm.hostname = "ctrl"

    ctrl.vm.network "private_network", ip: "#{NETWORK_BASE}.#{CTRL_IP_SUFFIX}"

    ctrl.vm.provider "virtualbox" do |vb|
      vb.name   = "k8s-ctrl"
      vb.cpus   = 1
      vb.memory = 4096   # 4 GB
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
        vb.cpus   = 2
        vb.memory = 6144  # 6 GB
      end
    end
  end
end