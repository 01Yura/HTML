# Настройка брандмауэра

Этот `README.md` предоставляет инструкции по настройке и конфигурации брандмауэра с использованием `iptables` на сервере Linux. Брандмауэр разрешает доступ к вашему веб-серверу только определенным сетям, блокируя все остальные соединения.

## Предварительные требования

Прежде чем начать, убедитесь, что у вас есть:

- Сервер Linux с установленным `iptables`.

## Настройка

### Определение диапазонов IP-адресов

Задайте диапазоны IP-адресов, которым разрешен доступ к вашему серверу. Измените эти переменные в соответствии с вашей конкретной сетевой конфигурацией:

```bash
MyWebServerIP="192.168.56.4/32"
MyMonitoringServerIP="5.101.102.99/32"
MyHomeNetwork="1.1.1.0/24"
MyHomeNetwork2="1.0.0.0/24"
MyWorkNetwork="2.2.2.0/24"
MyLANips="10.10.1.0/23"

Очистка существующих правил

Удалите все ранее настроенные правила iptables:

bash

sudo iptables --flush

Разрешение доступа к собственным интерфейсам

Разрешите доступ к интерфейсам собственной сети (loopback):

bash

sudo iptables -A INPUT -i lo -j ACCEPT

Разрешение установленных соединений

Разрешите любые уже установленные соединения:

bash

sudo iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

Разрешение доступа к веб-серверу

Разрешите доступ к вашему веб-серверу на портах 80 и 443 для любых IP-адресов:

bash

sudo iptables -A INPUT -s 0.0.0.0/0 -d 0.0.0.0/0 -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -s 0.0.0.0/0 -d 0.0.0.0/0 -p tcp --dport 443 -j ACCEPT

Разрешение доступа из дома и офиса

Разрешите доступ по SSH из указанных сетей:

bash

sudo iptables -A INPUT -s $MyHomeNetwork -d $MyWebServerIP -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -s $MyHomeNetwork2 -d $MyWebServerIP -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -s $MyWorkNetwork -d $MyWebServerIP -p tcp --dport 22 -j ACCEPT

Разрешение доступа по SNMP

Разрешите доступ по SNMP с мониторингового сервера:

bash

sudo iptables -A INPUT -s $MyMonitoringServerIP -d $MyWebServerIP -p udp --dport 161 -j ACCEPT

Разрешение ICMP (ping)

Разрешите ICMP-трафик (ping):

bash

sudo iptables -A INPUT -p icmp -j ACCEPT

Разрешение доступа к FTP

Разрешите доступ к FTP из указанных сетей:

bash

sudo iptables -A INPUT -s $MyHomeNetwork -d $MyWebServerIP -p tcp -m tcp --dport 21 -j ACCEPT
sudo iptables -A INPUT -s $MyHomeNetwork2 -d $MyWebServerIP -p tcp -m tcp --dport 21 -j ACCEPT
sudo iptables -A INPUT -s $MyWorkNetwork -d $MyWebServerIP -p tcp -m tcp --dport 21 -j ACCEPT

Разрешение диапазона портов

Разрешите диапазон портов (1030-1040) из указанных сетей:

bash

sudo iptables -A INPUT -s $MyHomeNetwork -d $MyWebServerIP -p tcp -m tcp --dport 1030:1040 -j ACCEPT
sudo iptables -A INPUT -s $MyHomeNetwork2 -d $MyWebServerIP -p tcp -m tcp --dport 1030:1040 -j ACCEPT
sudo iptables -A INPUT -s $MyWorkNetwork -d $MyWebServerIP -p tcp -m tcp --dport 1030:1040 -j ACCEPT

Разрешение доступа к NTP

Разрешите доступ к NTP из IP-адресов вашей локальной сети (LAN):

bash

sudo iptables -A INPUT -s $MyLANips -d $MyWebServerIP -p udp --dport 123 -j ACCEPT

Блокировка всех остальных соединений

Заблокируйте все остальные входящие соединения:

bash

sudo iptables -A INPUT -j DROP

Итог

Эта конфигурация iptables разрешает доступ к вашему веб-серверу только определенным сетям, блокируя все остальные входящие соединения. Убедитесь, что замените заполнительные IP-адреса и диапазоны сетей реальными значениями перед применением этой конфигурации.


Пожалуйста, убедитесь, что вы замените заполнительные IP-адреса и диапазоны сетей н
