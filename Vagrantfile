Vagrant.configure("2") do |config|

# versions available : https://app.vagrantup.com/peru 
  boxes = [
    { :name => "kingslanding", :ip => "192.168.56.10", :box => "peru/windows-server-2019-standard-x64-eval", :box_version => "20220510.01", :os => "windows", :memory => 2048, :cpus => 1,
      :forwarded_port => [
        {:guest => 3389, :host => 23389, :id => "msrdp"},
        {:guest => 5985, :host => 25985, :id => "winrm"}
      ]
    },
    { :name => "dragonstone", :ip => "192.168.56.11", :box => "peru/windows-server-2016-standard-x64-eval", :box_version => "20220510.01", :os => "windows", :memory => 2048, :cpus => 1,
      :forwarded_port => [
        {:guest => 3389, :host => 33389, :id => "msrdp"},
        {:guest => 5985, :host => 35985, :id => "winrm"}
      ]
    },
    { :name => "winterfell", :ip => "192.168.56.20", :box => "peru/windows-server-2019-standard-x64-eval", :box_version => "20220510.01", :os => "windows", :memory => 2048, :cpus => 1,
      :forwarded_port => [
        {:guest => 3389, :host => 43389, :id => "msrdp"},
        {:guest => 5985, :host => 45985, :id => "winrm"}
      ] 
    },
    { :name => "elk", :ip => "192.168.56.50", :box => "generic/ubuntu2004", :os => "linux", :memory => 1024, :cpus => 1,
      :forwarded_port => [
        {:guest => 22, :host => 2210, :id => "ssh"}
      ]
    }
  ]
#  ,
#    { :name => "highgarden", :ip => "192.168.56.30", :box => "peru/windows-10-enterprise-x64-eval", :box_version => "", :os => "windows", :memory => 1024, :cpus => 1,
#      :forwarded_port => [
#        {:guest => 3389, :host => 33389, :id => "msrdp"},
#        {:guest => 5985, :host => 35985, :id => "winrm"}
#      ]
#    }
#  ]


  config.vm.boot_timeout = 600
  config.vm.graceful_halt_timeout = 600
  config.winrm.retry_limit = 30
  config.winrm.retry_delay = 10

  boxes.each do |box|
    config.vm.define box[:name] do |target|


      # PROVIDER
      target.vm.provider "libvirt" do |v|
        v.memory = box[:memory]
        v.cpus = box[:cpus]
        v.default_prefix = "goad_"
      end

      # BOX
      target.vm.box = box[:box]
      if box.has_key?(:box_version)
        target.vm.box_version = box[:box_version]
      end
      
      # IP
      target.vm.network :private_network,
                        :ip => box[:ip],
                        :libvirt__network_name => "goad_private",
                        :libvirt__forward_mode => "none",
                        :libvirt__host_ip => "192.168.56.1",
                        :libvirt__dhcp_enabled => false

      # OS specific
      if box[:os] == "windows"
        target.vm.guest = :windows
        target.vm.communicator = "winrm"
        target.vm.provision :shell, :path => "vagrant/Install-WMF3Hotfix.ps1", privileged: false
        target.vm.provision :shell, :path => "vagrant/ConfigureRemotingForAnsible.ps1", privileged: false
      else
        target.vm.communicator = "ssh"
      end

      # forwarded port
      if box.has_key?(:forwarded_port)
        box[:forwarded_port] do |forwarded_port|
          target.vm.network :forwarded_port, guest: forwarded_port[:guest], host: forwarded_port[:host], id: forwarded_port[:id]
        end
      end

    end
  end
end
