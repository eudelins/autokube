NB_WORKER = 2
IMAGE = "generic/ubuntu2004"

NODES = [
  { :hostname => "kubm1", :ip => "192.168.69.11", :cpus => 2, :mem => 2048, :type => "kube_master" },
  { :hostname => "kubw1", :ip => "192.168.69.12", :cpus => 2, :mem => 2048, :type => "kube_worker" },
  { :hostname => "kubw2", :ip => "192.168.69.13", :cpus => 2, :mem => 2048, :type => "kube_worker" }
]

HOSTS = ""
NODES.each do |node|
  HOSTS += "echo '" + node[:ip] + "   " + node[:hostname] + "'>> /etc/hosts" + "\n"
end


COMMON_PKG = <<-SHELL
sudo apt update -qq 2>&1 >/dev/null
sudo apt install -y -qq unzip iftop curl software-properties-common git vim tree net-tools telnet git 2>&1 >/dev/null
sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
sudo systemctl restart sshd
SHELL

Vagrant.configure("2") do |config|
  NODES.each do |node|
    config.vm.define node[:hostname] do |cfg|
      cfg.vm.box = IMAGE
      cfg.vm.hostname = node[:hostname]
      cfg.vm.network "private_network", ip: node[:ip]
      cfg.vm.provider "libvirt" do |prov|
        prov.cpus = node[:cpus]
        prov.memory = node[:mem]
      end

      # Provisionning of basic stuff + setting up SSH
      cfg.vm.provision :shell, :inline => HOSTS
      cfg.vm.provision :shell, :inline => COMMON_PKG
      config.vm.provision "shell" do |shell|
        ssh_pub_key = File.readlines(".ssh/id_rsa.pub").first.strip
        shell.inline = <<-SHELL
          echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
          mkdir -p /root/.ssh && echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
        SHELL
      end
    end
  end
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessable to the vagrant box.
  # If you use this you may want to enable additional shared subfolders as
  # shown above.
  # config.vm.synced_folder ".", "/vagrant", disabled: true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
