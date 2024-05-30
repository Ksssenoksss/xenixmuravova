http://demo.2024.223.tilda.ws/demo2024123

https://sysahelper.ru/mod/page/view.php?id=162


Выполнение:

HQ-SRV:

apt-get install -y bind bind-utils

vim /etc/bind/options.conf

![image (4)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/ca8e44b2-d2c7-4800-aebd-a9eaeb09ae19)

systemctl enable --now bind

echo name_servers=127.0.0.1 >> /etc/resolvconf.conf

resolvconf -u

![image (5)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/4a803a76-daa8-4427-8cd6-7656262b7e7c)

vim /etc/bind/local.conf

![image (7)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/4f0abe0a-8c84-40e0-bae0-cb97f71940c2)

cp /etc/bind/zone/{localdomain,hq.db}

cp /etc/bind/zone/{localdomain,branch.db}

cp /etc/bind/zone/{127.in-addr.arpa,100.db}

cp /etc/bind/zone/{127.in-addr.arpa,200.db}

chown root:named /etc/bind/zone/{hq,branch,100,200}.db

vim /etc/bind/zone/hq.db

![image (14)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/bb5ba916-5b3f-4aba-863c-04de322e2a1e)

vim /etc/bind/zone/branch.db

![image (9)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/7e4b36c1-bbd6-4bf6-95a0-bf4acff7267c)

vim /etc/bind/zone/100.db

![image (10)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/3c499bc3-71a9-4697-87a0-dda6578936e0)

vim /etc/bind/zone/200.db

![image (11)](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/2ca6ba46-9749-432c-8609-a3854ca31b8d)

systemctl restart bind

НАСТРЙОКА НА HQ-R

apt-get install -y chrony

vim /etc/chrony.conf

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/fbf42ef7-710d-45af-a436-6fe8ee6840a5)

systemctl enable --now chronyd

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/da48c102-f1e5-472a-9af4-7346ac617ba9)

timedatectl set-timezone Europe/Moscow

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/0c488c0f-d531-4873-80d0-c0527cea0698)

HQ-SRV:

timedatectl set-timezone Europe/Moscow

apt-get install -y chrony

vim /etc/chrony.conf

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/e2806271-264e-47e1-bca1-559678fed233)

systemctl enable --now chronyd

проверить chronyc sources на hq-srv

hq-r chronyc clients

НАСТРЙОКА САМБА HQ-SRV

apt-get install -y task-samba-dc

control bind-chroot disabled

grep -q 'bind-dns' /etc/bind/named.conf || echo 'include "/var/lib/samba/bind-dns/named.conf";' >> 

/etc/bind/named.conf

vim /etc/bind/options.conf

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/e2067748-bc47-4210-90c6-43a1cf3829d1)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/fa3fc22f-5ff7-4f98-96ee-251c480b12d7)

systemctl stop bind

vim /etc/sysconfig/network

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/6e3ec30d-5bfd-40d9-8210-77b0a0dd2a4c)

hostnamectl set-hostname hq-srv.demo.first;exec bash

rm -f /etc/samba/smb.conf

rm -rf /var/lib/samba

rm -rf /var/cache/samba

mkdir -p /var/lib/samba/sysvol

samba-tool domain provision

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/1fb89471-8c5e-4037-bc7e-30392a3257d2)

samba-tool domain provision --realm=demo.first --domain=demo --adminpass='P@ssw0rd' --dns-

backend=BIND9_DLZ --server-role=dc --use-rfc2307

systemctl enable --now samba

systemctl enable --now bind

cp /var/lib/samba/private/krb5.conf /etc/krb5.conf

samba-tool domain info 127.0.0.1

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/4753ad6e-b539-49f1-b5a9-9935fc1053a2)

Ввод CLI и BR-SRV в домен:

CLI:

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/863711a5-c58f-4965-96a5-4c6fd1ecc9fb)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/b182968d-ecf1-4793-ae0e-975bdfe05de4)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/e6f6625c-d1c0-4d40-a9d8-d8bb90f3817d)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/b29af8fc-2fe3-4dc9-b86e-ed37beb92136)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/887b259d-28f8-449c-b741-e4ebb1b711df)



![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/a8c68fb5-70cc-4238-971d-eb90725d798b)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/99164fd7-7096-456e-89de-b3804514da67)



![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/4bd53fdc-377f-49e4-8bf9-b2cbf0b099dc)

BR-SRV:

apt-get install -y task-auth-ad-sssd

echo search demo.first > /etc/net/ifaces/ens33/resolv.conf

echo nameserver 192.168.100.1 >> /etc/net/ifaces/ens33/resolv.conf

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/2fe4e789-8abb-4415-aa88-99385c6c4fcb)

system-auth write ad demo.first br-srv demo 'administrator' 'P@ssw0rd'

reboot

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/8f45b65c-4ad1-404f-84e3-87aff15d753d)
![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/67821bbc-8e00-4094-aed1-92ae2625babd)

также на CLI

apt-get install -y admc

kinit administrator


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/8c04a4c5-aef0-4b7a-b933-0de00ffea959)



![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/78bd0579-570c-45cb-83dd-841cf3480410)



![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/ff9d2de5-987a-4ea7-a119-77feeabe571a)




![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/053e105b-289c-41a5-838d-b147b6f2c251)

HQ-SRV:

mkdir /opt/{branch,network,admin}

chmod 777 /opt/{branch,network,admin}

vim /etc/samba/smb.conf

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/5de26729-f139-4bf4-b34c-6fd67dc25407)


systemctl restart samba

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/6af0c15a-8110-4b89-b2c7-43862dfe0b9e)

BR-SRV:

apt-get install -y pam_mount

apt-get install -y cifs-utils

apt-get install -y systemd-settings-enable-kill-user-processes 

vim /etc/pam.d/system-auth

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/f26ee44c-3449-4919-976e-c614bcafaeeb)


vim /etc/security/pam_mount.conf.xml

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/a013ddf8-ef77-445e-8b83-43a1ccb310e7)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/99c7072d-3e8c-42eb-a2a5-b71a5e6e48e0)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/6c91f4db-17d6-4d9c-81c4-6f6083ea2090)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/ec4a5ab9-e6aa-4d64-92fd-0361263b98f6)


BR-SRV:

apt-get install -y apache2 apache2-{base,httpd-prefork,mod_php8.0,mods}

apt-get install -y php8.0 php8.0-{curl,fileinfo,fpm-fcgi,gd,intl,ldap,mbstring,mysqlnd,mysqlnd-mysqli,opcache,soap,sodium,xmlreader,xmlrpc,zip,openssl}

systemctl enable --now httpd2

apt-get install -y MySQL-server

systemctl enable --now mysqld

mysql

> CREATE DATABASE moodle DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
> CREATE USER 'moodle'@'localhost' IDENTIFIED WITH mysql_native_password BY 'moodle';
> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,CREATE TEMPORARY TABLES,DROP,INDEX,ALTER ON moodle.* TO 'moodle'@'localhost';
> EXIT;

apt-get install -y git

git clone git://git.moodle.org/moodle.git

cd moodle

git branch -a

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/a3b326e3-f3a8-41e2-9692-0712a184bc13)


git branch --track MOODLE_403_STABLE origin/MOODLE_403_STABLE

проверка
git checkout 
![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/5db23777-33e9-42a4-8db1-7283f77bc249)

Копируем локальный репозиторий в /var/www/html/:

cd ../

cp -R moodle /var/www/html/

Создаём необходимую структуру каталагов для корректной установки и работы Moodle:

mkdir /var/moodledata

chown -R apache2 /var/moodledata

chmod -R 777 /var/moodledata

chmod -R 0755 /var/www/html/moodle

chown -R apache2:apache2 /var/www/html/moodle

Описываем конфигурационный файл для веб-сервера Apache:

vim /etc/httpd2/conf/sites-available/moodle.conf

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/226d73fb-2178-4503-9be4-32d54b72d36f)

ln -s /etc/httpd2/conf/sites-available/moodle.conf /etc/httpd2/conf/sites-enabled/


apachectl configtest

sed -i "s/; max_input_vars = 1000/max_input_vars = 5000/g" /etc/php/8.0/apache2-mod_php/php.ini


systemctl restart httpd2

CLI:

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/650772c1-44e7-43e9-acbc-13685feea1f8)
![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/270bd83a-fcb8-411a-a0b2-67b8b7a0588d)
HQ-R:
![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/10fe9d1b-7612-40b4-abad-a2337bd9c816)

CLI:

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/97b4e0b8-d3dc-4928-917e-e6ebf628917e)


echo 192.168.200.1 moodle.demo.first moodle >> /etc/hosts

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/bee98b87-02ce-473a-932d-51bd9f9e78b1)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/eeb823d3-6ad3-49dd-abdf-614a7c086cc1)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/b25917f9-385f-4f6c-af99-1cb086153353)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/f307756e-3428-4da4-8e77-0ef36a3d61fc)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/204e9539-f802-4fde-961a-e9eb75f1dae2)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/467ea5c8-7ef1-467e-a03f-53a888e89eec)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/44a74704-3636-4938-8775-0989fa0e8571)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/4272f6aa-59a7-477b-af88-2718103618eb)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/f5416f8d-db24-45e7-918d-72d88ffe301b)

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/6f6840eb-228a-4079-9953-bc48486088e8)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/74ee05ab-22f1-4406-8713-31496c247436)

![2024-05-30_14-13-23](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/6468c8e5-7628-4001-a646-34911bea3788)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/e2403ff6-3a0b-43a4-a8a3-e08196b9cb09)



![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/0a0eb067-fb0c-482c-a582-abe1e6cfaa4a)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/3e8a7203-0bbd-4af0-a5df-2e4555ea9df9)



![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/15160ba0-9440-485f-8e78-d90a22adb7c4)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/933b7550-55a8-479b-8ec7-35117b712888)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/4b2e101b-b738-46e4-80d0-25d3fd7a527a)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/bb8a3ef6-76e4-4385-8382-2db5e070b1e4)

hq-srv

apt-get install -y docker-{ce,compose}

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/cdc430d1-7307-46c2-8eb1-3f8256d7c04d)


systemctl enable --now docker.service

vim ~/wiki.yml

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/d1e8c7aa-ba58-410e-84d2-2d290489ed4d)
После первоначальной настройки через Web-интерфейс с CLI загрузите LocalSettings.php в тот же каталог, что и эта wiki.yml и раскомментируйте следующую строку "# - ./LocalSettings.php:/var/www/html/LocalSettings.php" и используйте docker-compose для перезапуска службы mediawiki

Чтобы отдельный volume для хранения базы данных имел правильное имя - создаём его средствами docker:
docker volume create dbvolume
Выполняем сборку и запуск стека контейнеров с приложением MediaWiki и базой данных описанных в файле wiki.yml:

docker-compose -f wiki.yml up -d


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/472b97c1-2809-4414-8c6c-2b9f6f3921ae)

MediaWiki должна быть доступна извне через порт 8080
HQ-R:

nft add rule inet nat prerouting ip daddr 11.11.11.11 tcp dport 8080 dnat ip to 192.168.100.1:8080

также не забываем сохранить внесённые изменения в файл "/etc/nftables/nftables.nft"

iptables -t nat -A PREROUTING -i ens33 -p tcp --dport 8080 -j DNAT --to-destination 192.168.100.1:8080

также не забываем сохранить внесённые изменения в файл "/etc/sysconfig/iptables"

firewall-cmd --permanent --zone=public --add-forward-port=port=8080:proto=tcp:toport=8080:toaddr=192.168.100.1

firewall-cmd --reload

CLI:

echo 11.11.11.11 mediawiki.demo.first mediawiki >> /etc/hosts


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/47b5c825-4556-45f8-9593-4768c9d26255)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/a947d508-a088-43d6-96a1-df34a725b054)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/33eedde2-e01c-4e92-a45e-0f3f5d198622)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/8cfe56c0-6b31-4d77-a5c6-3788d152bd44)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/1edca8a2-7147-471e-8fef-e549869606cb)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/2135f3b0-4634-49a9-a32a-05638040fcb2)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/f95bef7e-0cab-40e2-a372-4b69c40fe25a)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/a9a5efea-7a5e-4f65-b9d2-7e8f666672c7)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/541e28af-c493-4044-9237-3bc0782934f7)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/acb981ce-0745-4a52-911d-5f801f78145d)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/c72d3375-a9f6-41fb-ad3f-0d2c976d2a25)

systemctl enable --now sshd

BR-R:

scp user@33.33.33.33:~/Загрузки/LocalSettings.php ./

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/1f499b2e-0f6f-4901-af06-8f4e38795a4b)

scp LocalSettings.php admin@11.11.11.11:/tmp

HQ-SRV:


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/45b4520a-c4da-4870-a1b6-7889994fd479)

mv /tmp/LocalSettings.php ~/

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/fff8569f-1853-45c8-80ae-c17be365caa3)

docker-compose -f wiki.yml stop
docker-compose -f wiki.yml up -d


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/9b1311f6-24a1-42c3-a0a6-75635c6d57b9)


3 модуль

BR-SRV:

apt-get install -y rsyslog-classic

vim /etc/rsyslog.d/00_common.conf


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/9f3004b0-fa7a-4049-a5a9-ea1475a58e2b)


![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/3badb258-b64d-4b0b-8ee5-8eb9a2b708dd)

systemctl enable --now rsyslog


BR-R:

![image](https://github.com/Ksssenoksss/xenixmuravova/assets/171246409/b58fb9af-0b4f-4855-85b6-ec021179728c)
