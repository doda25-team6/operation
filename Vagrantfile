# Vagrantfile
# Vagrant 2.x, VirtualBox provider

# Configurable number of worker nodes
WORKER_COUNT = ENV.fetch("WORKER_COUNT", 2).to_i

# Configurable controller
CTRL_CPUS   = ENV.fetch("CTRL_CPUS",   2).to_i
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

  # Generate Ansible inventory before provisioning
  config.trigger.before :up, :provision do |trigger|
    trigger.name = "Generate Ansible inventory"
    trigger.ruby do |env, machine|
      inventory_path = File.join(File.dirname(__FILE__), "ansible", "inventory.cfg")
      
      File.open(inventory_path, "w") do |f|
        f.puts "[ctrl]"
        f.puts "ctrl ansible_host=#{NETWORK_BASE}.#{CTRL_IP_SUFFIX} ansible_user=vagrant"
        f.puts ""
        f.puts "[workers]"
        (1..WORKER_COUNT).each do |i|
          worker_ip = "#{NETWORK_BASE}.#{WORKER_IP_START + i - 1}"
          f.puts "node-#{i} ansible_host=#{worker_ip} ansible_user=vagrant"
        end
        f.puts ""
        f.puts "[all:vars]"
        f.puts "ansible_ssh_private_key_file=~/.vagrant.d/insecure_private_key"
        f.puts "ansible_ssh_common_args='-o StrictHostKeyChecking=no'"
      end
      
      puts "Generated Ansible inventory at #{inventory_path}"
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/site.yml"
    ansible.inventory_path = "ansible/inventory.cfg"
    
    # Pass IP addresses as extra_vars so the template can use them
    ansible.extra_vars = {
      "ctrl_ip" => "#{NETWORK_BASE}.#{CTRL_IP_SUFFIX}",
      "worker_ips" => (1..WORKER_COUNT).map { |i| "#{NETWORK_BASE}.#{WORKER_IP_START + (i - 1)}" }
    }
  end

end
