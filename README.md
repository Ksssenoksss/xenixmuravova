Решение:
Настраиваем NAT - через nftables:
HQ-R | BR-R
Устанавливаем пакет nftables:
apt-get update && apt-get install -y nftables
Создаём таблицу nat для семейства inet (ipv4 и ipv6):
nft add table inet nat
Создаём цепочку postrouting (после маршрутизации) в таблице nat семейства inet:
nft add chain inet nat postrouting '{ type nat hook postrouting priority 0; }'
Добавляем правило в цепочку postrouting для HQ-R:
nft add rule inet nat postrouting ip saddr 192.168.100.0/26 oifname 'ens33' counter masquerade
Добавляем правило в цепочку postrouting для BR-R:
nft add rule inet nat postrouting ip saddr 192.168.200.0/28 oifname 'ens33' counter masquerade
Информация о том как сохранять правила присутствует в более ранних материалах (внесённые изменения через утилиту nft должы быть и в файле /etc/nftables/nftables.nft)

Настраиваем NAT - через iptables:
HQ-R | BR-R
В Альт iptables установлен по умолчанию:
добавляем правило в таблицу nat  в цепочку postrouting (после маршрутизации):
iptables -t nat -A POSTROUTING -s 192.168.100.0/26 -j MASQUERADE
Информация о том как сохранять правила присутствует в более ранних материалах (внесённые изменения через утилиту iptables должы быть и в файле /etc/sysconfig/iptables)

Настраиваем NAT - через firewalld:
HQ-R | BR-R
Устанавливаем пакет firewalld:
apt-get install -y firewalld
Включаем и добавляем в автозагрузку firewalld:
systemctl enable --now firewalld
Добавляем внешний интерфейс "ens33" в зону "public":
firewall-cmd --permanent --zone=public --add-interface=ens33
Добавляем внутренний интерфейс "ens34" в зону "trusted"
firewall-cmd --permanent -zone=trusted --add-interface=ens34
Добавляем masquerade на зону "public":
firewall-cmd --permanent --zone=public --add-masquerade
Перезапускаем firewalld для применения изменений:
firewall-cmd --reload

![2024-05-30_13-26-39](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/52745e3b-b167-401f-9223-ef17affd0a5c)
