# H4
Kotitehtävä 4 haaga-Helia AMK Palvelinten hallinta ict4tn022 kurssille

# h4.

a) Kokeile Ansible

b) Kokeile Salt:tia

Seuraavalle tunnille 2 tyhjää USB-tikkua mukaan.

Mieti, minkä oman modulin / projektin haluat tehdä tällä kurssilla.

Aloita kertaamaan kurssikirjallisuutta kokeeseen: tunnit, Puppet 3.8 Reference Manual, Puppet Cookbook.

http://terokarvinen.com/2017/aikataulu-palvelinten-hallinta-ict4tn022-3-5-op-uusi-ops-loppusyksy-2017-p5

Tein kotitehtävät Haaga-Helian labraluokassa live-xubuntu 16.04 LTS versiolla.
Tehtävien tekoon käytetty lähteenä Monialaprojektiin Arctic CCM-ryhmän kokoamaa github repoa
https://github.com/joonaleppalahti/CCM

## b) Kokeile Salt:tia

Asensin master ja orja koneelle SALTin

`sudo apt-get -y install salt-master
sudo apt-get -y install salt-minion`

Vaihdoin molemmille koneille nimet: Master ja Orja
Tämä ei saltin kohdalla käsittääkseni ole millään tavalla vaadittua, koska salt ottaa yhteyden iipparilla.

`sudo hostnamectl set-hostname master-eino`

Sekä myös /etc/hosts kansioon, jotta järjestelmä ei lähde etsimään nimiä verkon ulkopuolelta.

`sudoedit /etc/hosts`
`127.0.1.1 xubuntu master-eino`

`127.0.1.1 xubuntu orja-eino
172.28.171.161 master-eino`

Lisäsin minionille saltin asetuksiin masterin. Tiedosto on pelkkää kommenttia, paikalla ei väliä.

`sudoedit /etc/salt/minion`
`master: 172.28.171.161`

Käynnistin molemmat palvelut uusiksi
`sudo service salt-minion/master restart`

Masterilla listasin avaimet komennolla, ja josta syöte

`sudo salt-key -F master`
`
Local Keys:
master.pem:  e0:3e:28:c4:3a:32:57:ba:9c:14:13:fc:bc:ac:95:c4
master.pub:  ca:68:20:04:a3:ea:f5:1f:f5:89:09:d8:e7:fc:69:f3
Unaccepted Keys:
xubuntu:  16:66:a3:19:35:3c:1b:b0:78:61:05:e3:ea:f5:de:c4
`  

Hyväksyin avaimen komennolla

`
xubuntu@master-eino:~$ sudo salt-key -a xubuntu
The following keys are going to be accepted:
Unaccepted Keys:
xubuntu
Proceed? [n/Y] 
yKey for minion xubuntu accepted.
`

Kokeilin pingata orjaa, josta syöte:

`
xubuntu@master-eino:~$ sudo salt xubuntu test.ping
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
xubuntu:
    True
`

Kokeilin vielä asentaa apachen orjalle masterin kautta.

`
xubuntu@master-eino:~$ sudo salt xubuntu pkg.install apache2
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
[WARNING ] Key 'file_ignore_glob' with value None has an invalid type of NoneType, a list is required for this value
xubuntu:
    ----------
    apache2:
        ----------
        new:
            2.4.18-2ubuntu3.5
        old:
    apache2-api-20120211:
        ----------
        new:
            1
        old:
    apache2-bin:
        ----------
        new:
            2.4.18-2ubuntu3.5
        old:
    apache2-data:
        ----------
        new:
            2.4.18-2ubuntu3.5
        old:
    apache2-utils:
        ----------
        new:
            2.4.18-2ubuntu3.5
        old:
    httpd:
        ----------
        new:
            1
        old:
    httpd-cgi:
        ----------
        new:
            1
        old:
    libapr1:
        ----------
        new:
            1.5.2-3
        old:
    libaprutil1:
        ----------
        new:
            1.5.4-1build1
        old:
    libaprutil1-dbd-sqlite3:
        ----------
        new:
            1.5.4-1build1
        old:
    libaprutil1-ldap:
        ----------
        new:
            1.5.4-1build1
        old:
    liblua5.1-0:
        ----------
        new:
            5.1.5-8ubuntu1
        old:
`

Nyt syöttäessäni urliin "localhost", avautui apachen testisivu.

## a) Kokeile Ansible

Asensin masterille Ansiblen ja ssh, sekä orjalle ssh

`sudo apt-get -y install ansible ssh`

Lisäsin masterilla `/etc/ansible/hosts` tiedostoon ryhmän test, sekä orjan iipparin

`
[test]
172.28.171.18
`

Lisäsin minionille masterin julkisen avaimen

`
xubuntu@orja-eino:~$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/xubuntu/.ssh/id_rsa): ssh-copy-id xubuntu@172.28.171.161
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in ssh-copy-id xubuntu@172.28.171.161.
Your public key has been saved in ssh-copy-id xubuntu@172.28.171.161.pub.
The key fingerprint is:
SHA256:e5EIopECKMlETWC7ku4sWlPm1dVtTTGreJoAkVLybfM xubuntu@orja-eino
The key's randomart image is:
+---[RSA 2048]----+
|**+. ..o.      oo|
|*..o .o.o  . . oo|
|o.o . oo +. . o..|
| o.o . oooo....  |
|o.. o . S.oE o   |
|o  + .   ...+    |
| .o .   . .o     |
|+. .     .       |
|+o               |
+----[SHA256]-----+
`

