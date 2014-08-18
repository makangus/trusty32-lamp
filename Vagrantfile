# -*- mode: ruby -*-
# vi: set ft=ruby :

# NETWORKING
# Set the hostname of the VM. This will almost always need to be changed to
# match your project.
HOSTNAME = "trusty32-lamp"

# Set a static IP for this box in addition to the DHCP IP.
# STATIC_IP = "192.168.100.100"

# FILE SYNCING
# Choose between "vbox", "nfs", "rsync", or "none" sync types.
#   - vbox is the simplest, but also the slowest.
#   - NFS doesn't work on Windows, but is decently fast.
#   - rsync is the fastest, but doesn't automatically run. It also has some
#     performance issues in Vagrant 1.6. See
#     https://github.com/smerrill/vagrant-gatling-rsync for a temporary
#     solution.
SYNC_TYPE = "vbox"

# By default, sync a "www" directory within this project. This can be changed
# to an absolute path. By default, Apache is configured to look for a docroot
# directory within this directory to serve from.
SYNC_DIRECTORY = "www"

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # PRIVATE NETWORK
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", type: "dhcp"

  # Use this instead to hard code a specific IP address.
  if defined? STATIC_IP
    config.vm.network "private_network", ip: STATIC_IP
  end

  # Use this to allow access to the VM from outside of your host machine. This
  # is useful for testing with phones or tablets. However, it often does not
  # work on public Wifi networks, so it's disabled by default.
  # config.vm.network "public_network"

  # HOSTNAME
  # Set the hostname.
  config.vm.hostname = HOSTNAME

  # FILE SYNCING
  case SYNC_TYPE
    when "vbox"
      config.vm.synced_folder SYNC_DIRECTORY, "/var/www"

    when "nfs"
      config.vm.synced_folder SYNC_DIRECTORY, "/var/www", type: "nfs"

    when "rsync"
      config.vm.synced_folder SYNC_DIRECTORY, "/var/www", type: "rsync", rsync__exclude: ".git/", group: "www-data", rsync__args: ["--verbose", "--archive", "--delete", "-z", "--chmod=g+rwX"]

  end

  #
  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
      # Don't boot with headless mode
      # vb.gui = true

      # RESOURCE SETTINGS
      # Uncomment and modify these lines to allocate more resources to your VM.
      # vb.customize ["modifyvm", :id, "--memory", "2048"]
      ## ioapic is required for > 1 CPU core.
      # vb.customize ["modifyvm", :id, "--ioapic", "on"]
      # vb.customize ["modifyvm", :id, "--cpus", "4"]

      # This fixes DNS lag issues with Virtualbox.
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "trusty32-lamp"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  # config.vm.box_url = "http://domain.com/path/to/above.box"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Vagrant sets the hostname after the VM has run services. Restart Avahi to
  # ensure that it is broadcasting an updated hostname.
  config.vm.provision "shell",
    inline: "service avahi-daemon restart",
    run: "always"

  # Fix eth1 routing that vagrant adds. It just slows down boot time (A LOT!)
  config.vm.provision "shell",
    inline: "sed -i '/post-up route del default dev/d' /etc/network/interfaces",
    run: "always"

  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # Enable provisioning with Puppet stand alone.  Puppet manifests
  # are contained in a directory path relative to this Vagrantfile.
  # You will need to create the manifests directory and a manifest in
  # the file base.pp in the manifests_path directory.
  #
  # An example Puppet manifest to provision the message of the day:
  #
  # # group { "puppet":
  # #   ensure => "present",
  # # }
  # #
  # # File { owner => 0, group => 0, mode => 0644 }
  # #
  # # file { '/etc/motd':
  # #   content => "Welcome to your Vagrant-built virtual machine!
  # #               Managed by Puppet.\n"
  # # }
  #
  # config.vm.provision "puppet" do |puppet|
  #   puppet.manifests_path = "manifests"
  #   puppet.manifest_file  = "site.pp"
  # end

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

  # Enable provisioning with chef server, specifying the chef server URL,
  # and the path to the validation key (relative to this Vagrantfile).
  #
  # The Opscode Platform uses HTTPS. Substitute your organization for
  # ORGNAME in the URL and validation key.
  #
  # If you have your own Chef Server, use the appropriate URL, which may be
  # HTTP instead of HTTPS depending on your configuration. Also change the
  # validation key to validation.pem.
  #
  # config.vm.provision "chef_client" do |chef|
  #   chef.chef_server_url = "https://api.opscode.com/organizations/ORGNAME"
  #   chef.validation_key_path = "ORGNAME-validator.pem"
  # end
  #
  # If you're using the Opscode platform, your validator client is
  # ORGNAME-validator, replacing ORGNAME with your organization name.
  #
  # If you have your own Chef Server, the default validation client name is
  # chef-validator, unless you changed the configuration.
  #
  #   chef.validation_client_name = "ORGNAME-validator"
end
