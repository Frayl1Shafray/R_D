## Lecture 4 Vagrant

### Завдання: створити Vagrantfile, який запускає кілька віртуальних машин, із такими вимогами:

#### 1. VM1 (загальнодоступний вебсервер):
- Операційна система: на ваш вибір, особисто порекомендував би bento/ubuntu-24.04 або  generic/debian12
- Мережеве під’єднання до загальнодоступної мережі з динамічним IP
- Спільна папка між хостом і VM
- Провізіонінг: використовуйте shell-команду для оновлення та інсталювання пакетів (на цьому  етапі — будь-яка проста команда, bash вивчатимемо в наступних лекціях)

#### 2. VM2 (приватний сервер):
- Операційна система: на ваш вибір, особисто порекомендував би bento/ubuntu-24.04 або generic/debian12
- Під’єднання до приватної мережі зі статичним IP
- Спільна папка між хостом і VM
- Провізіонінг: використовуйте зовнішній bash-скрипт, який встановлює потрібні пакети  (на цьому етапі — будь-яка проста команда, bash вивчатимемо в наступних лекціях)

#### VM3 (загальнодоступний сервер зі статичним IP):
- Операційна система: на ваш вибір, особисто порекомендував би bento/ubuntu-24.04 або generic/debian12
- Під’єднання до загальнодоступної мережі зі статичним IP — використовуйте мережевий  інтерфейс хоста для мостового під’єднання (наприклад, Wi-Fi)
- Окрема спільна папка між хостом і VM


### Vagrantfile config
``` ruby
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-24.04"
  config.vm.box_check_update = false
  config.vm.synced_folder "./shared", "/home/vagrant/shared"

  config.vm.define "vm1" do |vm1|
      vm1.vm.hostname = "vagrant-ubuntu-vm1"
      vm1.vm.network "public_network", type: "dhcp"
      vm1.vm.provision "shell", inline: <<-SHELL
        sudo apt update -y
        sudo apt install -y net-tools
        echo "My new vm1 from Vagrant" > /home/vagrant/shared/hello.txt
      SHELL
  end

  config.vm.define "vm2" do |vm2|
      vm2.vm.hostname = "vagrant-ubuntu-vm2"
      vm2.vm.network "private_network", type: "static", ip: "192.168.60.108"
      vm2.vm.provision "shell", path: "install_packages.sh"
  end

  config.vm.define "vm3" do |vm3|
    vm3.vm.box = "generic/debian12"
    vm3.vm.box_check_update = false
    vm3.vm.hostname = "vagrant-ubuntu-vm3"
    vm3.vm.network "public_network", bridge: "Wi-Fi", type: "static", ip: "192.168.60.118"
    vm3.vm.synced_folder "./shared3", "/home/vagrant/shared"
    vm3.vm.provision "shell", inline: <<-SHELL
        echo "My new vm3 from Vagrant" > /home/vagrant/shared/hello3.txt
      SHELL
  end
end
```

#### 4. Додаткове завдання:
○ Створити три віртуальні машини з тією самою конфігурацією, що й VM1 (ті самі налаштування 
мережі, синхронізація папок, провізіонінг), але без дублювання коду :D

