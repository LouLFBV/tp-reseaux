TP7 : On dit chiffrer pas crypter
Dans ce TP on va mettre en place des outils afin de mettre en oeuvre du chiffrement.
Au menuuu :

serveur Web avec HTTPS
serveur VPN



Sommaire


TP7 : On dit chiffrer pas crypter

Sommaire


0. Prérequis
I. Setup
II. Serveur Web
III. Serveur VPN


0. Prérequis
➜ une machine Rocky Linux prête à être clonée

on s'en servira pour tout ce qui est routeur/serveur etc.
descendez bien à 1CPU et 1Go de RAM pour économiser des ressources

➜ votre machine Ubuntu prête à être clonée

pour le(s) client(s) toujours !
idem, limitez les ressources (1CPU, 1Go de RAM, 128Mo mémoire vidéo vu que c'est avec interface graphique)

➜ Ayez les mémos sous le coude !

I. Setup
➜ Pour chaque machine

donnez une IP statique
pour l'accès internet

activer le routage sur le routeur
carte NAT sur le routeur
définir le routeur comme passerelle pour les autres
aussi, n'oubliez pas d'indiquer 1.1.1.1 en serveur DNS partout


nommez vos machines (configuration du hostname)


II. Serveur Web

Serveur Web


Serveur Web


0. Setup

A. Schéma
B. Tableau adressage



1. HTTP

A. Install
B. Configuration
C. Tests client
D. Analyze



2. On rajoute un S

A. Config
B. Test test test analyyyze






0. Setup

A. Schéma


B. Tableau adressage



Machine
LAN1 10.7.1.0/24





client1.tp7.b1
10.7.1.101


web.tp7.b1
10.7.1.11


routeur.tp7.b1
10.7.1.254




1. HTTP

A. Install

Sur web.tp7.b1.

➜ Installer le service NGINX

dnf install -y nginx



B. Configuration

Sur web.tp7.b1.

➜ Créer un fichier de configuration pour un nouveau site web

créer un fichier dans le dossier /etc/nginx/conf.d/ qui a l'extension .conf

on va créer /etc/nginx/conf.d/sitedefou.tp7.b1.conf

avec le contenu suivant (remplacez <ADRESSE_IP> par l'adresse IP du serveur web)


server {
     le nom que devront taper les clients pour visiter le site
    server_name   sitedefou.tp7.b1;
    # on indique sur quelle adresse IP et quel port écouter
    listen        <ADRESSE_IP>:80;
    location      / {
        # "root" pour "racine" : l'emplacement de la racine web
        # c'est à dire le dossier qui contient le site web
        root      /var/www/sitedefou.tp7.b1;
    }
}


➜ Créer un nouveau site web :d

 on crée le dossier qui contient le site web
sudo mkdir -p /var/www/sitedefou.tp7.b1

 on dév un gros site web
echo "meow !" | sudo tee /var/www/sitedefou.tp7.b1

 gestion des permissions sur le dossier
sudo chown nginx:nginx -R /var/www/sitedefou.tp7.b1


➜ On démarre le service web NGINX

sudo systemctl start nginx


🌞 Lister les ports en écoute sur la machine

avec une commande adaptée (voir mémo)
isolez la ligne qui concerne le service NGINX

```
[lou@web ~]$ sudo ss -lnpt | grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=11283,fd=6),("nginx",pid=11282,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=11283,fd=7),("nginx",pid=11282,fd=7))
```

🌞 Ouvrir le port dans le firewall de la machine

```
[lou@web ~]$ sudo firewall-cmd --permanent --add-port=80/tcp
[sudo] password for lou:
success
[lou@web ~]$ sudo firewall-cmd --reload
success
```

C. Tests client

Sur client1.tp7.b1.

➜ Ajoutez la ligne suivante au fichier /etc/hosts

10.7.1.11 sitedefou.tp7.b1



Le fichier /etc/hosts c'est quand on veut un joli nom qui correspond à une IP, mais qu'on a la flemme de monter un serveur DNS juste pour ça :d

🌞 Vérifier que ça a pris effet

faites un ping vers sitedefou.tp7.b1

visitez http://sitedefou.tp7.b1

pour le compte-rendu, vous pouvez faire un curl depuis le terminal


vous devriez avoir la page "meow !" quand vous visitez le site

```
lou@client1:~$ ping sitedefou.tp7.b1
PING sitedefou.tp7.b1 (10.7.1.11) 56(84) bytes of data.
64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=1 ttl=64 time=1.78 ms
64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=2 ttl=64 time=1.90 ms
^C
--- sitedefou.tp7.b1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.781/1.842/1.903/0.061 ms


lou@client1:~$ curl sitedefou.tp7.b1
meow !
```

D. Analyze

Sur client1.tp7.b1.

🌞 Capture tcp_http.pcap


capturer une session TCP complète

le début (SYN, SYN ACK, ACK)
des données échangées (PSH, PSH ACK)

en l'occurence ce sera votre requête HTTP
et la réponse HTTP du serveur (meow !)


une fin de session (ça dépend, le plus souvent : FIN, FIN ACK ou RST)


cette session TCP doit être celle de l'échange HTTP pour récupérer la page du serveur web
il faut donc capturer pendant que tu visites le site web

[tcp.http.pcap](TP7/tcp_http.pcap)

⚠️⚠️ Remarquez que vous pouvez voir en clair le texte "meow !" dans la capture Wireshark. Toute la requête HTTP étouétou.

🌞 Voir la connexion établie

utilisez une commande ss sur le client pour voir le tunnel TCP établi
il faut taper la commande au moment où vous visitez le site web (ou juste après :d)


```
tcp                ESTAB               0                    0                                                                       10.7.1.101:59318                               34.120.208.123:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:59654                                 34.36.165.17:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:44078                              142.250.200.195:http                                          
tcp                ESTAB               0                    0                                                                       10.7.1.101:36260                               142.251.37.228:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:44064                              142.250.200.195:http                                          
tcp                ESTAB               0                    0                                                                       10.7.1.101:50134                                34.107.243.93:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:35026                                34.107.221.82:http                                          
tcp                ESTAB               0                    0                                                                       10.7.1.101:47972                               34.149.100.209:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:50142                                34.107.243.93:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:38102                               34.160.144.191:https                                         
tcp                ESTAB               0                    0                                                                       10.7.1.101:35042                                34.107.221.82:http       
```


2. On rajoute un S

A. Config

Sur web.tp7.b1.

➜ Générer une paire de clé pour le chiffrement

l'une des deux sera stocké dans un certificat
suivez juste le guide :d


 générer une paire de clé
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -keyout server.key -out server.crt

 déplacer la clé privée dans un endroit moins pourri
 il existe sur tous les OS destinés à stocker des clés de chiffrement
sudo mv server.key /etc/ssl/private/sitedefou.tp7.b1.key
 idem pour le certificat (qui contient la clé publique)
sudo mv server.crt /etc/ssl/certs/sitedefou.tp7.b1.crt

 gestion des permissions
sudo chown nginx:nginx /etc/ssl/private/sitedefou.tp7.b1.key
sudo chown nginx:nginx /etc/ssl/certs/sitedefou.tp7.b1.crt



➜ Modifier la configuration NGINX

go modifier votre fichier /etc/nginx/conf.d/sitedefou.tp7.b1.conf



server {
    server_name   sitedefou.tp7.b1;
    # on change le port d'écoute : 443 la convention pour HTTPS
    # il faut aussi ajouter la mention "ssl" pour activer le chiffrement
    listen        <ADRESSE_IP>:443 ssl;
    # et on indique les chemins vers la clé privée et le certificat
    ssl_certificate /etc/ssl/certs/sitedefou.tp7.b1.crt;
    ssl_certificate_key /etc/ssl/private/sitedefou.tp7.b1.key;
    location      / {
        root      /var/www/sitedefou.tp7.b1;
    }
}


➜ Redémarrer le service web NGINX !

sudo systemctl start nginx

🌞 Lister les ports en écoute sur la machine

avec une commande adaptée (voir mémo)
isolez la ligne qui concerne le service NGINX qui écoute en HTTPS

```
[lou@web ~]$ sudo ss -lnpt | grep 443
LISTEN 0      511        10.7.1.11:443       0.0.0.0:*    users:(("nginx",pid=1370,fd=6),("nginx",pid=1369,fd=6))
```

🌞 Gérer le firewall

ouvrir le nouveau port sur lequel NGINX écoute pour HTTPS
fermer l'ancien port qui était utilisé pour HTTP

```
[lou@web ~]$ sudo firewall-cmd --permanent --add-port=443/tcp
success
[lou@web ~]$ sudo firewall-cmd --reload
success
[lou@web ~]$ sudo firewall-cmd --permanent --remove-port=80/tcp
success
[lou@web ~]$ sudo firewall-cmd --reload
success
```

B. Test test test analyyyze

Sur client1.tp7.b1.

➜ Tu devrais pouvoir visiter https://sitedefou.tp7.b1 (bien https avec le 's')
🌞 Capture tcp_https.pcap


capturer une session TCP complète

le début (SYN, SYN ACK, ACK)
des données échangées (PSH, PSH ACK)

en l'occurence ce sera votre échange TLS

Wireshark affichera "TLS" dans la colonne protocole, mais si vous cliquez dessus, vous verrez que c'est bien contenu dans du TCP PSH


puis les données HTTP... chiffrées


une fin de session (ça dépend, le plus souvent : FIN, FIN ACK ou RST)


cette session TCP doit être celle de l'échange HTTPS pour récupérer la page du serveur web
il faut donc capturer pendant que tu visites le site web en HTTPS


⚠️⚠️ Remarquez que vous pouvez voir en clair le texte "meow !" vous voyez plus rien dukoo dans la capture Wireshark. Juste y'a du trafic chiffré qui circule, c'est tout ce qu'on peut dire.


[tcp_https.pcap](tcp_https.pcap) 


III. Serveur VPN
Dans cette partie on va mettre en place un serveur VPN. On va utiliser Wireguard : opensource, moderne, robuste, rapide, le feu.
Le but : on se connecte à un serveur VPN en tant que client (là ce sera une VM le serveur VPN, mais à l'autre bout du monde ça a le même effet).
Une fois connecté au VPN, on accès à :


on peut désigner le serveur VPN comme passerelle

ainsi, notre traffic ira jusqu'au serveur VPN avant de sortir sur internet



un réseau local, à distance

là il contiendra un serveur Web





Sommaire


III. Serveur VPN

Sommaire

0. Setup

A. Schéma
B. Tableau adressage


1. Install et conf Wireguard
2. Ajout d'un client VPN
3. Proofs
4. Private service




0. Setup

A. Schéma


B. Tableau adressage



Machine
LAN1 10.7.1.0/24

LAN2 10.7.2.0/24





client1.tp7.b1
10.7.1.101
x


vpn.tp7.b1
10.7.1.111
10.7.2.111


routeur.tp7.b1
10.7.1.254
x


web.tp7.b1
x
10.7.2.11




1. Install et conf Wireguard

Sur vpn.tp7.b1.

➜ Installer Wireguard

sudo dnf install -y epel-release
sudo dnf install -y wireguard-tools


➜ Générer la paire de clés du serveur

après cette commande, la clé publique sera stockée dans le fichier /etc/wireguard/wg0.pub

et la clé privée dans /etc/wireguard/wg0.priv



wg genkey | sudo tee /etc/wireguard/wg0.priv | wg pubkey | sudo tee /etc/wireguard/wg0.pub


➜ Ecrire le fichier de configuration du serveur

créer le fichier /etc/wireguard/wg0.conf

il faut remplacer <CLE_PRIVEE_DU_SERVEUR> par... la clé privée du serveur

sudo cat /etc/wireguard/wg0.priv pour l'obtenir




[Interface]
PrivateKey = <CLE_PRIVEE_DU_SERVEUR>
Address = 10.7.200.1/24
ListenPort = 51820


➜ Démarrez l'interface réseau wg0

sudo systemctl start wg-quick@wg0


🌞 Prouvez que vous avez bien une nouvelle carte réseau wg0

elle doit porter l'adresse IP indiquée dans le fichier de conf

```
[lou@vpn ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:1e:74:96 brd ff:ff:ff:ff:ff:ff
    inet 10.7.1.111/24 brd 10.7.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe1e:7496/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:c1:e4:79 brd ff:ff:ff:ff:ff:ff
    inet 10.7.2.111/24 brd 10.7.2.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fec1:e479/64 scope link
       valid_lft forever preferred_lft forever
4: wg0: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1420 qdisc noqueue state UNKNOWN group default qlen 1000
    link/none
    inet 10.7.200.1/24 scope global wg0
       valid_lft forever preferred_lft forever
```


🌞 Déterminer sur quel port écoute Wireguard

avec une commande adaptée
isolez la ligne intéressante

```
[lou@vpn ~]$ sudo ss -lnpu | grep 51820
UNCONN 0      0            0.0.0.0:51820      0.0.0.0:*

UNCONN 0      0               [::]:51820         [::]:*
```


🌞 Ouvrez ce port dans le firewall
➜ Enfin, activez le routage avec :

oublie pas ça, important pour la suite :d


 on active le forwarding : on accepte de faire passer des paquets
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -w net.ipv6.conf.all.forwarding=1

 on active le masquerading : permettre d'être la passerelle internet des clients
sudo firewall-cmd --add-interface=wg0 --zone=public --permanent
sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload


```
[lou@vpn ~]$ sudo firewall-cmd --permanent --add-port=51820/udp
success
[lou@vpn ~]$ sudo firewall-cmd --reload
success
```


2. Ajout d'un client VPN
Allez on ajoute un client : il génère ses clés, échange de clé publique, donc fichier de conf qui inclut la clé publique du correspondant.
Z'est bardi.

Sur client1.tp7.b1.

➜ Installer Wireguard

allez, je donne pas la commande, demande à google pour savoir comment le faire sur le client !s

➜ Générer la paire de clés du client

même commande que sur le serveur

➜ Ecrire le fichier de configuration du client

créer le fichier /etc/wireguard/wg0.conf

remplacez les clés par les bonnes valeurs
il faut sudo cat sur les clés pour les afficher et les insérer


avec le contenu :


[Interface]
PrivateKey = <CLE_PRIVEE_DU_CLIENT>
Address = 10.7.200.11/24

[Peer]
PublicKey = <CLE_PUBLIQUE_DU_SERVEUR>
AllowedIPs = 0.0.0.0/0
Endpoint = 10.7.1.111:51820
PersistentKeepalive = 25


➜ Allumer l'interface wg0 du client

sudo wg-quick up wg0




De retour sur le serveur vpn.tp7.b1.

➜ Modifier le fichier de configuration du serveur

on ajoute une section [Peer] pour indiquer

l'IP du client
sa clé publique




[Interface]
PrivateKey = <CLE_PRIVEE_DU_SERVEUR>
Address = 10.7.200.1/24
ListenPort = 51820


[Peer]
PublicKey = <CLE_PUBLIQUE_DU_CLIENT>
AllowedIps = 10.7.200.11/32


➜ Redémarrer l'interface du serveur après l'ajout du client

sudo systemctl restart wg-quick@wg0


➜ Vérifier qu'on voit bien un nouveau client

sudo wg show



3. Proofs
🌞 Ping ping ping !

depuis le client, faites un ping vers l'IP du serveur VPN au sein du réseau virtuel
donc ping 10.7.200.1

```
lou@client1:~$ ping 10.7.200.1
PING 10.7.200.1 (10.7.200.1) 56(84) bytes of data.
64 bytes from 10.7.200.1: icmp_seq=1 ttl=64 time=2.06 ms
64 bytes from 10.7.200.1: icmp_seq=2 ttl=64 time=3.52 ms
64 bytes from 10.7.200.1: icmp_seq=3 ttl=64 time=2.08 ms
64 bytes from 10.7.200.1: icmp_seq=4 ttl=64 time=3.78 ms
64 bytes from 10.7.200.1: icmp_seq=5 ttl=64 time=3.07 ms
64 bytes from 10.7.200.1: icmp_seq=6 ttl=64 time=3.88 ms
^C
--- 10.7.200.1 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 6079ms
rtt min/avg/max/mdev = 2.062/3.064/3.877/0.747 ms

```

🌞 Capture ping1_vpn.pcap

capturez ces pings, en capturant sur l'interface host-ononly
ne vous attendez pas à vraiment voir directement des pings... vous regardez du trafic VPN

[ping1_vpn.pcap](ping1_vpn.pcap)

🌞 Capture ping2_vpn.pcap

capturez ces pings, en capturant sur l'interface wg0

vous allez voir vos vrais ping

[ping2_vpn.pcap](ping2_vpn.pcap)

➜ Sur le client1.tp7.b1

ajoutez 10.7.200.1 comme votre passerelle, temporairement :


 on supprime la passerelle actuelle manuellement
sudo ip route delete default

 on ajoute une passerelle qui passe par le serveur VPN
sudo ip route add default via 10.7.200.1


🌞 Prouvez que vous avez toujours un accès internet

mais on va pas utiliser ping

vous allez utiliser traceroute

l'avantage, c'est que lui, il affiche tous les intermédiaires entre vous et la destination

traceroute 1.1.1.1 dans le compte-rendu


On devrait voir que pour aller sur internet, vous passez par le serveur VPN.

```
lou@client1:~$ traceroute 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 60 byte packets
 1  _gateway (10.7.200.1)  1.940 ms  1.921 ms  2.065 ms
 2  10.7.1.254 (10.7.1.254)  4.600 ms  4.269 ms  4.261 ms
 3  10.0.3.2 (10.0.3.2)  6.236 ms  6.054 ms  11.033 ms
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
```

4. Private service

Sur web.tp7.b1.

Last but not least, le serveur Web. Il est isolé dans un LAN (potentiellement à l'autre bout du monde), impossible d'y accéder... sauf si on est connectés au VPN.
Pour ce faire :

recycler la machine web de la partie d'avant
branchez-le au nouveau host-only
attribuez-lui la nouvelle IP 10.7.2.11

installer wireguard, et ajouter la machine comme nouveau client (même manip que sur client1.tp7.b1)

l'IP de web.tp7.b1 doit être 10.7.200.37



modifier la configuration NGINX pour qu'il écoute uniquement sur l'IP 10.7.200.37


🌞 Visitez le service Web à travers le VPN

site web privé accessible uniquement à ceux qui sont connectés au VPN

curl https://https://sitedefou.tp7.b1 en ayant modifié votre fichier hosts pour que pointe vers 10.7.200.37

```
lou@client1:~$ sudo curl -k https://sitedefou.tp7.b1
meow !

```


Et on a donc virtuellement obtenu ce réseau LAN :
