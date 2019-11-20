# Инсталляция ZeroNet

* Скачайте архив ZeroBundle: [Microsoft Windows](https://github.com/HelloZeroNet/ZeroNet-win/archive/dist/ZeroNet-win.zip), [Apple macOS](https://github.com/HelloZeroNet/ZeroNet-mac/archive/dist/ZeroNet-mac.zip), [Linux 64bit](https://github.com/HelloZeroNet/ZeroBundle/raw/master/dist/ZeroBundle-linux64.tar.gz), [Linux 32bit](https://github.com/HelloZeroNet/ZeroBundle/raw/master/dist/ZeroBundle-linux32.tar.gz)
* Распакуйте где хотите
* Запустите `ZeroNet.exe` (win), `ZeroNet(.app)` (macOS), `ZeroNet.sh` (linux)

### Инсталляция вручную на Debian Linux

* `sudo apt-get update`
* `sudo apt-get install msgpack-python python-gevent`
* `wget https://github.com/HelloZeroNet/ZeroNet/archive/master.tar.gz`
* `tar xvpfz master.tar.gz`
* `cd ZeroNet-master`
* Запустите `python zeronet.py`
* Откройте http://127.0.0.1:43110/ в Вашем браузере

### [Vagrant](https://www.vagrantup.com/)

* `vagrant up`
* Поключитесь к ВМ `vagrant ssh`
* `cd /vagrant`
* Запустите `python zeronet.py --ui_ip 0.0.0.0`
* Откройте http://127.0.0.1:43110/ в Вашем браузере

### [Docker](https://www.docker.com/)
* `docker run -d -v <local_data_folder>:/root/data -p 15441:15441 -p 127.0.0.1:43110:43110 nofish/zeronet`
* Этот образ Docker включает в себя Tor прокси, отключенный изначально. Учтите, что некоторые 
провайдеры хостинга не позволяют запускать Tor на их серверах. Если Вы хотите включить его, установите
переменную окружения `ENABLE_TOR` в `true` (Изначально: `false`). Например:

 `docker run -d -e "ENABLE_TOR=true" -v <local_data_folder>:/root/data -p 15441:15441 -p 127.0.0.1:43110:43110 nofish/zeronet`
* Откройте http://127.0.0.1:43110/ в Вашем браузере

### [Virtualenv](https://virtualenv.readthedocs.org/en/latest/)

* `virtualenv env`
* `source env/bin/activate`
* `pip install msgpack-python gevent`
* `python zeronet.py`
* Откройте http://127.0.0.1:43110/ в Вашем браузере
