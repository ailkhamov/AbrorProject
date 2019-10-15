# Install required plugins
required_plugins = ["vagrant-hostsupdater"]
required_plugins.each do |plugin|
    exec "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

# our app (JS) needs to speak with the db.
  # by looking at the code it is using DB_HOST (line18 on app.js)
  # In the provision file we want to create a environment variable for DB_HOST

  #.bashrc
    # define the variable here.
  #.profile
    # make sure it always reads .bashrc

  # objective is to make a function out of this
def set_vars(vars_dictionary)
  command = <<~HEREDOC
    echo "setting Environment Variables"
    sources ~/.bashec
  HEREDOC

  vars_dictionary.each do |key, value|
    command = <<~HEREDOC
      if [ -z "#{key}"]; then
        echo "export#{key}=#{value}" >> ~/.bashrc
      fi
    HEREDOC
  end

  return command
end

Vagrant.configure("2") do |config|
  config.vm.define "app" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "192.168.10.100"
    app.hostsupdater.aliases = ["development.local"]
    app.vm.synced_folder "app", "/home/ubuntu/app"
    app.vm.provision "shell", path: "environment/app/provision.sh", privileged: false
    # call the funciton tha set our variable
    app.vm.provision "shell", inline: set_vars({DB_HOST: "mongod://192.168.10.101:27017/posts"})


  end
  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/xenial64"
    db.vm.network "private_network", ip: "192.168.10.101"
    db.hostsupdater.aliases = ["database.local"]
    db.vm.synced_folder "environment/mongodb", "/home/ubuntu/environment"
    db.vm.provision "shell", path: "environment/mongodb/provision.sh", privileged: false
  end


end
