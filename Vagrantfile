# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # This works with virtualbox and vmware.
  # A box for parallels is defined below.
  config.vm.box = "chef/centos-6.5"

  # Enabling the Berkshelf plugin. To enable this globally, add this configuration
  # option to your ~/.vagrant.d/Vagrantfile file
  config.berkshelf.enabled = true

  # Make sure Chef is the latest version.
  config.omnibus.chef_version = :latest

  config.vm.hostname = "jrdev"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.network :private_network, ip: "192.168.47.100"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder "..", "/sb4dev"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Don't boot with headless mode
  #   vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # Provider config
  config.vm.provider "virtualbox" do |v, override|
    v.name = "jrdev"
    v.memory = 1024
    v.cpus = 2
  end

  config.vm.provider "vmware_fusion" do |v, override|
    v.name = "jrdev"
    v.memory = 1024
    v.cpus = 2
    override.vm.network :private_network, ip: "172.16.145.136"
  end

  config.vm.provider "parallels" do |v, override|
    v.name = "jrdev"
    v.memory = 1024
    v.cpus = 2
    override.vm.box = "parallels/centos-6.5"
  end

  # Enable provisioning with chef solo, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  #
  # config.vm.provision "chef_solo" do |chef|
  #   chef.cookbooks_path = "../my-recipes/cookbooks"
  #   chef.roles_path = "../my-recipes/roles"
  #   chef.data_bags_path = "../my-recipes/data_bags"
  #   chef.add_recipe "mysql"
  #   chef.add_role "web"
  #
  #   # You may also specify custom JSON attributes:
  #   chef.json = { :mysql_password => "foo" }
  # end

  # Provisoning with chef solo
  config.vm.provision "chef_solo" do |chef|
    chef.log_level = :debug if !(ENV['CHEF_DEBUG']).nil?
    chef.data_bags_path = "data_bags" if File.directory?(File.expand_path(File.dirname(__FILE__)) + "/data_bags")
    chef.run_list = [
      "recipe[apache2]",
      "recipe[avahi]",
      "recipe[database]",
      "recipe[drush]",
      "recipe[git]",
      "recipe[htpasswd]",
#      "recipe[jr-php]",
      "recipe[mysql::server]",
      "recipe[mysql::client]",
      "recipe[php]",
      "recipe[yum-ius]"
    ]
    chef.json = {
      "avahi" => {
        "deny_interfaces" => [ "eth0" ]
      },
      "mysql" => {
        "bind_address" => "0.0.0.0",
        "server_root_password" => "root",
        "server_debian_password" => "debpass",
        "server_repl_password" => "replpass",
        "allow_remote_root" => true,
        "tunable" => {
          "innodb_buffer_pool_size" => "128M",
          "key_buffer_size" => "16M"
        }
      }
    }
  end

end
