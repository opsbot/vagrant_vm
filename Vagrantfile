ssh_key_name = 'devvm'
required_plugins = %w( vagrant-vbguest vagrant-disksize vagrant-hostmanager )
$environment_variables = <<SCRIPT
tee "/etc/profile.d/vars.sh" > "/dev/null" <<EOF
export GITHUB_API_TOKEN=#{ENV['GITHUB_API_TOKEN']}
EOF
SCRIPT

Vagrant.configure("2") do |config|
  _retry = false
  required_plugins.each do |plugin|
    unless Vagrant.has_plugin? plugin
      system "vagrant plugin install #{plugin}"
      _retry=true
    end
  end

  if (_retry)
    exec "vagrant " + ARGvb.join(' ')
  end

  config.vm.box = "opsbot/ubuntu2004-desktop"
  config.vm.box_check_update = true
  # config.vm.box_version = "20210117.0"
  config.disksize.size = "128GB"

  config.vm.define "ubuntu2004-desktop"

  config.vm.provider :virtualbox do |v, override|
      v.gui = true
      v.customize ["setextradata", "global", "GUI/MaxGuestResolution", "any"]
      v.customize ["setextradata", :id, "CustomVideoMode1", "1680x1050x32"]
      v.customize ["modifyvm", :id, "--accelerate3d", "on"]
      v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      v.customize ["modifyvm", :id, "--cpus", 6]
      v.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
      v.customize ["modifyvm", :id, "--hwvirtex", "on"]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
      v.customize ["modifyvm", :id, "--memory", 12288]
      v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
      v.customize ["modifyvm", :id, "--nictype1", "virtio"]
      v.customize ["modifyvm", :id, "--rtcuseutc", "on"]
      v.customize ["modifyvm", :id, "--vram", "256"]
      v.customize ["modifyvm", :id, "--vrde", "off"]
  end

  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder "provision", "/vagrant/provision"

  config.vm.provision "shell", inline: $environment_variables, run: "always"

  config.vm.provision "system", type: "shell", path: "provision/bin/system", upload_path: "/vagrant/provision/bin/system", privileged: true
  config.vm.provision "user", type: "shell", path: "provision/bin/user", upload_path: "/vagrant/provision/bin/user", privileged: false

  # config.vm.provision "file", source: "#{ENV['HOME']}//.ssh//#{ssh_key_name}", destination: "/tmp/#{ssh_key_name}", run: "always"
  # config.vm.provision "file", source: "#{ENV['HOME']}//.ssh//#{ssh_key_name}.pub", destination: "/tmp/#{ssh_key_name}.pub", run: "always"

  # config.vm.provision "shell", run: "always", inline: <<-SHELL
  #   set -o errexit -o pipefail -o nounset
  #   mv /tmp/#{ssh_key_name}* /home/vagrant/.ssh
  #   chmod 0400 /home/vagrant/.ssh/#{ssh_key_name}
  #   apt-get update
  #   DEBIAN_FRONTEND=noninteractive apt-get -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confnew" dist-upgrade -y
  # 	sudo add-apt-repository main
  # 	sudo add-apt-repository universe
  # 	sudo add-apt-repository restricted
  # 	sudo add-apt-repository multiverse
  # 	sudo apt-get update
  # 	sudo apt-get install -y software-properties-common
  # 	sudo apt-get update -y
  # 	sudo apt-get install -y python-setuptools python-dev build-essential
  # 	sudo apt-get install -y python-pip
  # 	sudo pip install ansible
  #   DEBIAN_FRONTEND=noninteractive apt-get -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confnew" dist-upgrade -y
  #   PYTHONUNBUFFERED=1 ANSIBLE_NOCOLOR=true /vagrant/provisioning/run.sh development_environment.yml
  #   apt-get autoremove -y
  #   apt-get clean
  # SHELL
end
