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
grep -q 'bind-dns' /etc/bind/named.conf || echo 'include "/var/lib/samba/bind-dns/named.conf";' >> /etc/bind/named.conf
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



samba-tool domain provision --realm=demo.first --domain=demo --adminpass='P@ssw0rd' --dns-backend=BIND9_DLZ --server-role=dc --use-rfc2307


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
