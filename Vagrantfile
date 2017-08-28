#Vagrant.require_plugin "vagrant-vmware-workstation"

File.open("version.txt", 'w') do |f|
  f.write(Vagrant::VERSION)
end


nodes = ENV['NODES'] || 2

esxi_mem = ENV['ESXI_MEM'] || 4096
esxi_cpu = ENV['ESXI_CPU'] || 2

Vagrant.configure("2") do |config|
  config.vm.box = "vmware_esxi65"
  config.vm.boot_timeout = 500
  config.vm.box_url = "./vmware_esxi65.box"
  config.vm.synced_folder ".", "/vagrant", nfs: true

  (1..(nodes.to_i)).each do |i|
    hostname = "esxi-%02d" % (i - 1)
    #cfg = NODE_YAML
    memory = esxi_mem
    cpus = esxi_cpu
    puts "Node #{i}: hostname #{hostname}"
    config.ssh.default.username = "root"
    config.ssh.shell = "sh"

    config.vm.define vmName = hostname do |esxi|
      esxi.vm.hostname = vmName

      if vmName == "esxi-%02d" % (i - 1)
        config.vm.hostname = hostname
        
        [:vmware_fusion, :vmware_workstation].each do |name|
          config.vm.provider name do |v,override|
            v.vmx["memsize"] = memory
            v.vmx["numvcpus"] = cpus
          end
        end
        config.vm.provision "shell", privileged: false, path: "provision.sh"
      end
    end
  end
end