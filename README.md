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
