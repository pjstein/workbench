#
# Workbench Vagrant File
# 
# @date          12.29.2013
# @author        PJ$
# @description   Vagrant file to drop into any project, which will spin up a 
#                box with sensible defaults and derive the configuration for 
#                some of the provisioning
# @repository    https://github.com/pjstein/workbench
# @version       0.1.0
#

V_API_VERSION   = '2'
VM_IMAGE_URL    = 'http://chickenandwaffl.es/boxes/ubuntu-12.04-x86_64.box'

COOKBOOKS_DIR   = './cookbooks'
RUNLIST_CONFIG  = '.workbench.runlist'
JSON_CONFIG     = '.workbench.config.json'

PROJECT_DIR     = File.dirname(File.symlink?(__FILE__) ? File.readlink(__FILE__) : __FILE__)
PROJECT_NAME    = File.basename(PROJECT_DIR)
COOKBOOKS_PATH  = File.expand_path(File.join(PROJECT_DIR, COOKBOOKS_DIR))

# Helpers for Provisioning

def has_default_recipe?(directory)
  File.exists? File.join(directory, 'recipes', 'default.rb')
end

# Actual Vagrant Stuff

Vagrant.configure(V_API_VERSION) do |config|

  config.vm.define PROJECT_NAME.to_sym do |node|
    node.vm.box      = 'workbench-ubuntu-12.04-x86_64'
    node.vm.box_url  = "#{VM_IMAGE_URL}"
    node.vm.hostname = "#{PROJECT_NAME}.local"
    node.vm.network    :private_network, type: :dhcp
  
    node.vm.provider :virtualbox do |driver, override|
      driver.gui  = false
      driver.name = PROJECT_NAME
  
      driver.customize ['modifyvm', :id, '--cpus'               , '4'   ]
      driver.customize ['modifyvm', :id, '--memory'             , '1024']
      driver.customize ['modifyvm', :id, '--natdnshostresolver1', 'on'  ]

      override.ssh.forward_agent = true
    end
  
    node.vm.synced_folder '.', "/opt/#{PROJECT_NAME}", {
      :id        => "#{PROJECT_NAME}-root",
      :disabled  => false,
      :group     => 'daemon',
      :owner     => 'nobody'
    }
  end

  if File.directory? COOKBOOKS_PATH and not Dir["#{COOKBOOKS_PATH}/*/"].empty?
    config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path   = COOKBOOKS_PATH
      chef.node_name        = PROJECT_NAME
      chef.verbose_logging  = true

      runlist_path = File.expand_path(File.join COOKBOOKS_PATH, RUNLIST_CONFIG)
      config_path  = File.expand_path(File.join COOKBOOKS_PATH, JSON_CONFIG   ) 

      if File.exists? runlist_path
        runlist = IO.read(runlist_path).split("\n").map{ |x| x.strip }.select{ |x| not x.empty? }
      else
        runlist = Dir["#{COOKBOOKS_PATH}/*/"].select{ |d| has_default_recipe? d }.map{ |d| "#{File.basename(d)}::default" }.sort
      end
    
      runlist.each{ |r| chef.add_recipe r }

      config = {}
      config.merge! JSON.parse(IO.read(config_path)) if File.exists? config_path

      chef.json = config
    end
  end

end
