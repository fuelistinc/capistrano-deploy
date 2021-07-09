# -*- mode: ruby -*-
# vi: set ft=ruby :
# REQUIRED_PLUGINS = %w(vagrant-librarian-chef-nochef)

# plugins_to_install = REQUIRED_PLUGINS.select { |plugin| not Vagrant.has_plugin? plugin }
# if not plugins_to_install.empty?
#   puts "Installing required plugins: #{plugins_to_install.join(' ')}"
#   if system "vagrant plugin install #{plugins_to_install.join(' ')}"
#     exec "vagrant #{ARGV.join(' ')}"
#   else
#     abort "Installation of one or more plugins has failed. Aborting."
#   end
# end

Vagrant.configure("2") do |config|
  config.vm.box = "fuelist"
  config.vm.box_url = "https://fuelist.s3-us-west-1.amazonaws.com/fuelist.box"
  # Configure the virtual machine to use 1.5GB of RAM
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1536"]
    vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant", "1"]
  end

  # Forward the Rails server default port to the host
  # config.vm.network :forwarded_port, guest: 3000, host: 3000
  # Sync dev folder
  config.vm.synced_folder ".", "/home/ubuntu/capistrano-deploy", mount_options:["dmode=755,fmode=644"]


  # Set up nameservers
  cmd = 'echo -e "nameserver 0.0.0.0\nnameserver 127.0.0.1\nnameserver localhost\nnameserver 8.8.8.8\nnameserver 8.8.4.4" | tee /etc/resolv.conf'
  config.vm.provision :shell, :inline => cmd

  cmd2 = 'echo "cd /home/ubuntu/capistrano-deploy" >> /home/vagrant/.bashrc'
  config.vm.provision :shell, :inline => cmd2

  # Set up mysql and a user w/ password matching config/database.yml
  # Move this to ENV_VARIABLE
  $setup = <<-SCRIPT
    sudo DEBIAN_FRONTEND=noninteractive apt-get install mysql-server mysql-client libmysqlclient-dev libssl-dev -y
  SCRIPT

  $node = <<-SCRIPT
    cd /home/ubuntu/capistrano-deploy
    npm install
  pwd
  SCRIPT

  # Install prerequisets (redis, mysql, java runtime)
  config.vm.provision :shell, inline:<<-SHELL
    sudo apt-get update
    sudo apt-get install redis-server -y
    sudo apt-get install default-jdk -y
  SHELL

  config.vm.provision :shell, :inline => $node
 
end
