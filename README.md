Debian 6.x 7.x 8.x
apt-get update
apt-get install wget
apt-get install nano
apt-get install gcc
apt-get install --reinstall build-essential
wget https://github.com/z3APA3A/3proxy/archive/0.8.11.tar.gz
tar -xvzf 3proxy-0.8.11.tar.gz
Переименуем папку в 3proxy
cd 3proxy
echo '#define ANONYMOUS 1' >> src/proxy.h
     или nano src/proxy.h  и добавляем строчку #define ANONYMOUS 
make -f Makefile.Linux
mkdir /usr/local/etc/3proxy
mkdir /usr/local/etc/3proxy/bin
mkdir /usr/local/etc/3proxy/logs
mkdir /usr/local/etc/3proxy/stat
cp src/3proxy /usr/local/etc/3proxy/bin
cp ./scripts/rc.d/proxy.sh /etc/init.d/3proxy
chmod +x /etc/init.d/3proxy
insserv 3proxy
touch /usr/local/etc/3proxy/3proxy.cfg
nano /usr/local/etc/3proxy/3proxy.cfg
     
              Редактируем файл
     
              # Путь к файлу 3proxy.pid
              pidfile /usr/local/etc/3proxy/3proxy.pid
              # Серверы имен DNS, посмотрев их на своем VDS в nano /etc/resolv.conf
              nserver xxx.xxx.xxx.xxx
              # Размер кэша для запросов DNS
              nscache 65536
              # Таймауты
              timeouts 1 5 30 60 180 1800 15 60
              # Запускаем в режиме daemon
              daemon
              # Для соединения по паролю.
              auth strong
              # Логин 3proxy, Пароль 12345 - Заменить на свои
              users 3proxy:CL:12345
              # Запрещаем внутренний интерфейс
              deny * * 127.0.0.1,192.168.1.1
              # Разрешаем HTTP и HTTPS трафик
              allow * * * 80-88,8080-8088 HTTP
              allow * * * 443,8443 HTTPS
              # ограничение - не более (х) подключений
              maxconn 5
              # Здесь укажем после ключа "-i" и "-e"  IP сервера , ключа "-p" Порт прокси
              # У вас должно получится примерно такое 
              # proxy -n -a -i222.222.222.222 -e222.222.222.222 -p33333
              proxy -n -a -ixxx.xxx.xxx.xxx -exxx.xxx.xxx.xxx -pxxxxx
              # Очищаем список access-листов которые остались от http-proxy
              flush
              # Если нужен  socks5-proxy добавляем строчку
              # Для соединения по паролю.
              auth strong
              # Логин 3proxy, Пароль 12345 - Заменить на свои
              users 3proxy:CL:12345
              # Разрешаем любые подключения
              allow * * * *
              # Здесь укажем после ключа "-i" и "-e"  IP сервера , ключа "-p" Порт socks5
              socks  -n -a -ixxx.xxx.xxx.xxx -exxx.xxx.xxx.xxx -pxxxxx
              # Очищаем список access-листов
              flush
              # Конец конфига (  все что после "end" читаться прокси не будет)
              end



chown -R nobody:nogroup /usr/local/etc/3proxy
/etc/init.d/3proxy start
netstat -an | grep -i listen
reboot

