Vagrant.configure("2") do |config|
    check_prerequisites

    config.vm.box = "ubuntu/xenial64"

    (0..2).each do |index|
        type = 'controller'

        config.vm.define machine_name(type, index) do |machine|
            setup(type, index, machine)
        end
    end

    (0..2).each do |index|
        type = 'worker'

        config.vm.define machine_name(type, index) do |machine|
            setup(type, index, machine)
        end
    end
end

def setup(type, index, machine)
    machine.vm.network "private_network", ip: machine_ip(type, index)
    setup_dns(machine_name(type, index), machine)
    define_resources(machine_name(type, index), machine)
end

def machine_ip(type, index)
    base_ip = {
        'controller' => '172.16.2.1',
        'worker' => '172.16.2.10'
    }

    "#{base_ip[type]}#{index}"
end

def machine_name(type, index)
    "#{type}-#{index}"
end

def setup_dns(name, machine)
    machine.dns.tld = 'k8s'
    machine.vm.hostname = name

    machine.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
end

def check_prerequisites
    required_plugins = [
        'vagrant-dns',
        'vagrant-vbguest']

    required_plugins.each do |plugin|
        unless Vagrant.has_plugin?(plugin)
            raise "Plugin #{plugin} is not installed"
        end
    end
end

def define_resources(name, machine)
    machine.vm.provider :virtualbox do |vb|
        vb.name = name
        vb.memory = 1024
        vb.cpus = 1
    end
end