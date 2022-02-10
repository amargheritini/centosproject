
disk_primary = './secondDisk1.vdi' #variable disk primary guest

disk_secondary = './secondDisk2.vdi' #variable disk secondary guest

Vagrant.configure("2") do |config|

  config.vm.define "guest_primary" do |guest_primary|
    guest_primary.vm.box = "centos/7"
    guest_primary.vm.disk :disk, name: "primary", size: "5GB"  
    #guest_primary.vm.disk :disk, name: "secoondary", size: "5GB"  
    #guest_primary.vm.network "public_network" , bridge: "Realtek PCIe GbE Family Controller" , ip: "dhcp"
    guest_primary.vm.network "private_network" ,  ip: "192.168.2.101"

  #config.vm.provision "ansible" , playbook: "centos_deploy.yml"
    
          
        guest_primary.vm.provider "virtualbox" do |workload_primary|
            workload_primary.name = "CentOS_WL1" 
            workload_primary.gui = true
            workload_primary.memory = "512"
            
            unless File.exist?(disk_primary)
              workload_primary.customize ['createhd', '--filename', disk_primary, '--variant', 'Fixed', '--size', 1 * 1024]
            end
            workload_primary.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_primary]


        end      

    guest_primary.vm.provision "shell", inline: <<-SHELL
      sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
      systemctl restart sshd.service
     SHELL
      
    end

  config.vm.define "guest_secondary" do |guest_secondary|
      guest_secondary.vm.box = "centos/7"
      guest_secondary.vm.disk :disk, name: "primary", size: "5GB"  
      #guest_secondary.vm.network "public_network" , bridge: "Realtek PCIe GbE Family Controller" , ip: "dhcp"
      guest_secondary.vm.network "private_network" ,  ip: "192.168.2.102"

    
       
        guest_secondary.vm.provider "virtualbox" do |workload_secondary|
          workload_secondary.name = "CentOS_WL2" 
          workload_secondary.gui = true
          workload_secondary.memory = "512"

          unless File.exist?(disk_secondary)
            workload_secondary.customize ['createhd', '--filename', disk_secondary, '--variant', 'Fixed', '--size', 1 * 1024]
          end
          workload_secondary.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_secondary]

             
         end    

   guest_secondary.vm.provision "shell", inline: <<-SHELL
      sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
      systemctl restart sshd.service
   SHELL
         
       
   end

 

  config.vm.define "controller" do |controller|

    controller.vm.box = "luminositylabsllc/ubuntu-20.04"
    #malcyon/ubuntu2004
    controller.vm.disk :disk, name: "primary", size: "10GB"  
    controller.vm.network "public_network" , bridge: "Realtek PCIe GbE Family Controller"  
    controller.vm.network "private_network" ,  ip: "192.168.2.100"
    
    
     controller.vm.provider "virtualbox" do |ctrlnodeUbuntu|
      
          ctrlnodeUbuntu.name = "UbuntuController"
          ctrlnodeUbuntu.gui = true
          ctrlnodeUbuntu.memory = "2048"
          ctrlnodeUbuntu.cpus = "4"
      end

     controller.vm.provision "shell", inline: <<-SHELL
     # apt-get update
      apt-get install -y python3.9
      apt-get install -y ansible
      apt-get install -y sshpass
      
      ansible-galaxy collection install community.general
      ansible-galaxy collection install ansible.posix
      ansible-galaxy install alexinthesky.secure-docker-daemon
      ansible-galaxy install atosatto.docker-swarm
      ansible-galaxy install bessonov.docker-remote-api
      wget http://192.168.2.1:8080/centos_deploy_final.yml
      wget http://192.168.2.1:8080/inventory.yml
      wget http://192.168.2.1:8080/ansible.cfg
      ansible-playbook centos_deploy_final.yml -i inventory.yml

      SHELL

   # config.ssh.insert_key = false

    #controller.vm.provision "ansible" do |ansible|
     # ansible.verbose = "v"
      #ansible.playbook = "centos_deploy_final.yml"

    #end



  end
   


  
 
end