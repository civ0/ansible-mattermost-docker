Vagrant.configure("2") do |config|
    config.vm.box = "generic/debian11"

    config.vm.define 'debian'

    # Prevent SharedFoldersEnableSymlinksCreate errors
    config.vm.synced_folder ".", "/vagrant", disabled: true

    config.vm.provider :virtualbox do |vb|
        vb.name = 'debian'
        vb.memory = 4096
        vb.cpus = 2
    end
end
