TP6 : Des bo services dans des bo LANs

Dans ce TP, on construit encore et toujours sur ce qui a déjà été fait.

➜ Ce qu'on garde des TPs précédents :

des VMs partout, et des réseaux privé-hôte (host-only) pour créer nos LANs
le routeur qui permet à tout le monde de joindre internet
un serveur DHCP qui propose tout ce qu'il faut aux clients (une IP libre, un DNS joignable, l'adresse de la passerelle)

➜ Au menu de ce nouveau TP :


deux LANs !

un pour les clients
un pour nos serveurs
un peu comme le réseau de l'école



des services proposés au sein du LAN des serveurs

un serveur DNS
un serveur Web





Sommaire


TP6 : Des bo services dans des bo LANs

Sommaire


0. Prérequis

I. Le setup

0. Schéma
1. Tableau d'adressage
2. Marche à suivre
II. LAN clients
1. Serveur DHCP
2. Client



III. LAN serveurzzzz

1. Serveur Web
2. Serveur DNS
3. Serveur DHCP




0. Prérequis
➜ une machine Rocky Linux prête à être clonée

on s'en servira pour tout ce qui est routeur/serveur etc.
descendez bien à 1CPU et 1Go de RAM pour économiser des ressources

➜ votre machine Ubuntu prête à être clonée

pour le(s) client(s) toujours !
idem, limitez les ressources (1CPU, 1Go de RAM, 128Mo mémoire vidéo vu que c'est avec interface graphique)

➜ Ayez les mémos sous le coude !

I. Le setup

0. Schéma


J'ai inclus votre PC en transparence juste pour bien vous rappeler qu'il est là dans les réseaux, qu'il a aussi une interface branchée aux switches host-only, et que c'est pour cette raison qu'il peut ping tout le monde et vous permettre le SSH.


1. Tableau d'adressage



Machine
LAN1 10.6.1.0/24

LAN2 10.6.2.0/24





dhcp.tp6.b1
10.6.1.253
x


client1.tp6.b1
DHCP
x


routeur.tp6.b1
10.6.1.254
10.6.2.254


web.tp6.b1
x
10.6.2.11


dns.tp6.b1
x
10.6.2.12


Votre PC
10.6.1.1
10.6.2.1




2. Marche à suivre
➜ Créez deux nouveaux host-only

attribuez à votre PC les adresses IP indiquées dans le tableau d'adressage

➜ Créez toutes les machines virtuelles

clones dans tous les sens
Ubuntu (ou OS de votre choix) pour client1.tp6.b1

Rocky Linux pour tous les autres
branchez-les aux bons host-only :D

➜ Allumez tout et attribuez les adresses IP indiquées

sauf client1.tp6.b1, no need pour le moment
configuration réseau sur toutes les machines :

adresse IP statique sur les cartes host-only
indiquez l'adresse IP de votre routeur comme passerelle

laquelle ? Celle qui est dans ton LAN !
par exemple, une machine du LAN1, bah sa passerelle c'est 10.6.1.254 (et PAS 10.6.2.254)


indiquez 1.1.1.1 comme DNS


n'oubliez pas d'activer le routage sur routeur.tp6.b1


NOMMEZ VOS MACHINES (configuration du hostname, voir mémo)

☀️ Prouvez que...

une machine du LAN1 peut joindre internet (ping un nom de domaine)
une machine du LAN2 peut joindre internet (ping nom de domaine)
une machine du LAN1 peut joindre une machine du LAN2 (ping une adresse IP)


Prouvez que tout fonctionne quoi ! :d Je veux voir le nom des machines dans le prompt, vous devez donc avoir nommé vos machines.

```
[lou@web ~]$ ping ynov.com
PING ynov.com (104.26.11.233) 56(84) bytes of data.
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=1 ttl=53 time=22.7 ms
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=2 ttl=53 time=23.6 ms
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=3 ttl=53 time=23.1 ms
^C
--- ynov.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 22.721/23.141/23.575/0.348 ms

[lou@dhcp ~]$ ping ynov.com
PING ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=1 ttl=53 time=19.5 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=2 ttl=53 time=25.1 ms
^C
--- ynov.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1004ms
rtt min/avg/max/mdev = 19.458/22.257/25.056/2.799 ms

[lou@dhcp ~]$ ping 10.6.2.11
PING 10.6.2.11 (10.6.2.11) 56(84) bytes of data.
64 bytes from 10.6.2.11: icmp_seq=1 ttl=63 time=2.51 ms
64 bytes from 10.6.2.11: icmp_seq=2 ttl=63 time=4.53 ms
64 bytes from 10.6.2.11: icmp_seq=3 ttl=63 time=5.06 ms
64 bytes from 10.6.2.11: icmp_seq=4 ttl=63 time=3.97 ms
^C
--- 10.6.2.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3009ms
rtt min/avg/max/mdev = 2.507/4.015/5.057/0.951 ms
```

II. LAN clients
Partie dédiée à la configuration du LAN1 : 10.6.1.0/24. Un réseau dans lequel des clients pourraient se connecter, accéder à internet, et plus tard accéder à nos services internes (un ptit site web de fou, ce sera partie III).

1. Serveur DHCP

A faire sur dhcp.tp6.b1.

Dans cette section, install + config du serveur DHCP. Pour que tous les clients du LAN1 puisse avoir un accès au réseau dès leur connexion !
➜ Installez et configurez un serveur DHCP sur dhcp.tp6.b1

pareil qu'au TP5
il doit attribuer des IPs entre .37 et .137

il doit indiquer la bonne passerelle pour les clients
il doit indiquer 1.1.1.1 comme serveur DNS aux clients


2. Client

A faire sur client1.tp6.b1.

➜ Allumez client1.tp6.b1 et configurez sa carte réseau en DHCP

il devrait récupérer automatiquement une adresse IP auprès de votre serveur DHCP
et apprendre l'adresse de la passerelle de ce réseau
et l'adresse d'un DNS utilisable

☀️ Prouvez que...

le client a bien récupéré une adresse IP en DHCP

avec un ip a le mot-clé dynamic doit être écrit sur la ligne qui contient l'adresse IP
(y'a pas ce mot-clé quand tu définis une IP statique)


vous avez bien 1.1.1.1 en DNS

commande dans le mémo pour consulter l'adresse IP du serveur DNS connu


vous avez bien la bonne passerelle indiquée

idem, dans le mémo, pour afficher l'adresse de la passerelle actuellement configurée :)


que ça ping un nom de domaine public sans problème magueule
 
 ```
lou@client6:~$ ip addr show enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:10:04:10 brd ff:ff:ff:ff:ff:ff
    inet 10.6.1.37/24 metric 100 brd 10.6.1.255 scope global dynamic enp0s3
       valid_lft 586sec preferred_lft 586sec
    inet6 fe80::a00:27ff:fe10:410/64 scope link 
       valid_lft forever preferred_lft forever


lou@client6:~$ resolvectl
Global
         Protocols: -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
  resolv.conf mode: stub

Link 2 (enp0s3)
    Current Scopes: DNS
         Protocols: +DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 1.1.1.1
       DNS Servers: 1.1.1.1
        DNS Domain: srv.world


lou@client6:~$ ip r s 
default via 10.6.1.254 dev enp0s3 proto dhcp src 10.6.1.37 metric 100 
1.1.1.1 via 10.6.1.254 dev enp0s3 proto dhcp src 10.6.1.37 metric 100 
10.6.1.0/24 dev enp0s3 proto kernel scope link src 10.6.1.37 metric 100 
10.6.1.254 dev enp0s3 proto dhcp scope link src 10.6.1.37 metric 100 


lou@client6:~$ ping ynov.com
PING ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226: icmp_seq=1 ttl=53 time=15.6 ms
64 bytes from 172.67.74.226: icmp_seq=2 ttl=53 time=17.1 ms
64 bytes from 172.67.74.226: icmp_seq=3 ttl=53 time=17.2 ms
^C
--- ynov.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2088ms
rtt min/avg/max/mdev = 15.623/16.639/17.236/0.722 ms

 ```

III. LAN serveurzzzz
Troisième partie, dédiée à la configuration de nos deux serveurs. Pour l'instant c'est juste des Rocky sans âme :d

1. Serveur Web

A faire sur web.tp6.b1.

III. 1. Serveur Web

1. Intro serveur Web

2. Install this shiet

3. Analyse et test






1. Intro serveur Web
On appelle serveur Web une machine qui exécute un programme qu'on appelle un service Web.
Un service Web est un programme qui écoute sur un port, attend la connexion de clients.

Par convention, le port sur lequel écoute les services Web HTTP c'est 80/tcp.

Un client est supposé se connecter au port envoyer une requête HTTP, et le serveur renverra alors une page HTML qui correspond à la requête du client.

Comme nc aux premiers TPs, sauf que quand tu te connectes, et que t'envoies la bonne requête, bah le service Web répond automatiquement une page HTML.

On va utiliser un service Web qui s'appelle NGINX, réputé pour ses performances et sa stabilité !

2. Install this shiet

On est sur notre (futur) beau serveur Web web.tp6.b1.

On est pas en cours de Linux, ni en cours de dév Web, alors on va s'intéresser à la partie réseau uniquement, et limiter au maximum les interactions spécifiques à Linux.
➜ Installez et démarrez le service web NGINX

# installation du service NGINX
sudo dnf install -y nginx

# démarrage du service NGINX
sudo systemctl enable --now nginx


Devrait po y avoir d'erreurs, et ça tourne !

Le service web NGINX propose une page d'accueil (toute moche) par défaut. On aura même pas besoin d'écrire une vieille page HTML pour tester :D Il permet si on le configure d'héberger les sites Web de notre choix dans une utilisation réelle.


3. Analyse et test
☀️ Déterminer sur quel port écoute le serveur NGINX

commande dans le mémooooooo
isolez la ligne intéressante avec un ... | grep <PORT> une fois que vous avez repéré le port

```
[lou@web ~]$ sudo ss -lnpt | grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=11203,fd=6),("nginx",pid=11202,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=11203,fd=7),("nginx",pid=11202,fd=7))
```

C'est le port conventionnel ! Pour un service Web, qui reçoit donc des requêtes HTTP, c'est ce port qui est la convention :) C'est aussi écrit dans la configuration quelque port qu'il doit écouter sur ce port. Conf dans le dossier /etc/nginx/ pour les curieux.


☀️ Ouvrir ce port dans le firewall

bien que NGINX soit en train d'écouter sur le port repéré, et attende la connexion de clients potentiels...
... ça empêche pas le firewall de bloquer les clients si on ouvre pas le port
commande dans le mémooooo pour faire ça aussi

```
[lou@web ~]$ sudo firewall-cmd --permanent --remove-service dhcpv6-client
success
[lou@web ~]$ sudo firewall-cmd --permanent --remove-service cockpit
success
[lou@web ~]$ sudo firewall-cmd --reload
success
[lou@web ~]$ sudo firewall-cmd --permanent --add-port=80/tcp
success
[lou@web ~]$ sudo firewall-cmd --reload
success
```


☀️ Visitez le site web !

depuis client1.tp6.b1, ouvrez un navigateur, et visitez http://10.6.2.11 (l'adresse IP du serveur web web.tp6.b1)
normalement, page d'accueil !

sinon, tu t'es planté, vérifie tout !


pour le compte-rendu, depuis le terminal de client1.tp6.b1 : curl http://10.6.2.11

vous me mettez que les premières lignes du résultat SVP :D
la commande curl envoie une requête HTTP (comme un navigateur), en réponse on tout l'HTML de la page... bonne bouillie :D

```
lou@client6:~$ curl http://10.6.2.11
<!doctype html>
<html>
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <title>HTTP Server Test Page powered by: Rocky Linux</title>
    <style type="text/css">
      /*<![CDATA[*/
      
      html {
        height: 100%;
        width: 100%;
      }  
```

2. Serveur DNS

A faire sur dns.tp6.b1.

III. 2. Serveur DNS

1. Présentation serveur DNS
2. Dans notre TP
3. Zé bardi
4. Analyse du service
5. Tests manuels




1. Présentation serveur DNS
➜ Un serveur DNS est une machine sur laquelle s'exécute un programme qu'on appelle service DNS.
Un service DNS écoute sur un port, et attend la connexion de clients.

Par convention, les services DNS écoutent sur les ports 53/tcp et 53/udp.

➜ Un service DNS attend qu'un client se connecte et lui envoie une requête DNS.
Une requête DNS est la requête effectuée par une machine lorsqu'elle souhaite connaître l'adresse IP qui correspond à un nom de domaine.

Par exemple, si vous ouvrez un navigateur web et saisissez https://www.ynov.com alors une requête DNS est automatiquement et spontanément effectuée par votre PC pour déterminez à quelle adresse IP correspond le nom www.ynov.com.

➜ Une fois que vous connaissez l'adresse IP, vous pouvez vous connecter à cette adresse IP.

La partie https:// ne fait pas partie du nom de domaine, ça indique simplement au navigateur la méthode de connexion. Ici, c'est HTTPS.


2. Dans notre TP

Dans cette partie, on va installer et configurer notre propre serveur DNS. Le service qu'on va installer s'appelle BIND9. C'est genre le service DNS le plus utilisé au monde. Rien k'sa.

Notre serveur DNS répondra aux autres VMs du LAN (comme le client1.tp6.b1) quand elles auront besoin de connaître des noms. Ainsi, ce serveur pourra :


résoudre des noms locaux

vous pourrez ping web.tp6.b1 et ça fonctionnera
même visiter le site web avec http://web.tp6.b1 dans un navigateur



mais aussi résoudre des noms publics

genre ping www.ynov.com et votre serveur DNS sera capable de le résoudre aussi




En réalité, ce n'est pas votre serveur DNS qui pourra résoudre www.ynov.com, mais il sera capable de forward (faire passer) votre requête à un autre serveur DNS qui lui, connaît la réponse. Comme 1.1.1.1 (serveur DNS de CloudFlare) ou 8.8.8.8 (serveur DNS de Google).

Une fois qu'il sera installé et configuré, on pourra indiquer aux clients de l'utiliser comme serveur DNS.

3. Zé bardi

Pour cette partie, on est sur le beau dns.tp6.b1.

Sur internet ça peut partir dans tous les sens, alors je vous ai écrit un petit tuto maison cette fois hehe. Lisez attentivement toute cette partie en entier avant de commencer les manips. Pour mieux capter ce que vous faites. Ha et dernière chose : good luck, parce que la syntaxe de configuration de BIND, elle fait mal aux dents.

Installation du serveur DNS :

# installation du serveur DNS, son p'tit nom c'est BIND9
$ sudo dnf install -y bind bind-utils


La configuration du serveur DNS va se faire dans 3 fichiers essentiellement :


un fichier de configuration principal

/etc/named.conf
on définit les trucs généraux, comme les adresses IP et le port où on veu écouter
on définit aussi un chemin vers les autres fichiers, les fichiers de zone



un fichier de zone

/var/named/tp6.b1.db
je vous préviens, la syntaxe fait mal
on peut y définir des correspondances nom ---> IP




un fichier de zone inverse

/var/named/tp6.b1.rev
on peut y définir des correspondances IP ---> nom




➜ Allooooons-y, fichier de conf principal

je ne vous mets que les lignes les plus importantes, et celles qu'on modifie
les [...] indiquent qu'il faut laisser la conf par défaut, que j'ai enlevé ici pour que ce soit + lisible

il ne faut donc pas les mettre dans vos fichiers




# éditez le fichier de config principal pour qu'il ressemble à :
$ sudo cat /etc/named.conf
options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
[...]
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };

        recursion yes;
[...]
# référence vers notre fichier de zone
zone "tp6.b1" IN {
     type master;
     file "tp6.b1.db";
     allow-update { none; };
     allow-query {any; };
};
# référence vers notre fichier de zone inverse
zone "2.6.10.in-addr.arpa" IN {
     type master;
     file "tp6.b1.rev";
     allow-update { none; };
     allow-query { any; };
};


➜ Et pour les fichiers de zone

dans ces fichiers c'est le caractère ; pour les commentaires
hésitez pas à virer mes commentaires de façon générale
c'juste pour que vous captiez mais ça fait dégueu dans un fichier de conf


# Fichier de zone pour nom -> IP

$ sudo cat /var/named/tp6.b1.db

$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Enregistrements DNS pour faire correspondre des noms à des IPs
web       IN A 10.6.2.11
dns       IN A 10.6.2.12



# Fichier de zone inverse pour IP -> nom

$ sudo cat /var/named/tp6.b1.rev

$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Reverse lookup
11 IN PTR web.tp6.b1.
12 IN PTR dns.tp6.b1.


➜ Une fois ces 3 fichiers en place, démarrez le service DNS

# Démarrer le service
$ sudo systemctl enable --now named
# OUI JE SAIS le truc s'appelle bind9 et faut démarrer "named"
# et moi, je vous en pose des questions ?

# Obtenir des infos sur le service
$ sudo systemctl status named

# Obtenir des logs en cas de probème
$ sudo journalctl -xe -u named



4. Analyse du service
☀️ Déterminer sur quel(s) port(s) écoute le service BIND9

isolez la ligne intéressante avec un ... | grep <PORT> une fois que vous avez repéré le port
bon normalement vous l'avez vu dans la conf, non ? :d

```
[lou@dns ~]$ sudo ss -lnpt | grep 53
LISTEN 0      10         10.6.2.12:53        0.0.0.0:*    users:(("named",pid=1860,fd=25))
LISTEN 0      10         127.0.0.1:53        0.0.0.0:*    users:(("named",pid=1860,fd=22))
LISTEN 0      4096       127.0.0.1:953       0.0.0.0:*    users:(("named",pid=1860,fd=28))
LISTEN 0      10             [::1]:53           [::]:*    users:(("named",pid=1860,fd=27))
LISTEN 0      4096           [::1]:953          [::]:*    users:(("named",pid=1860,fd=29))
```

☀️ Ouvrir ce(s) port(s) dans le firewall

```
[lou@dns ~]$ sudo firewall-cmd --permanent --add-port=53/tcp
success
[lou@dns ~]$ sudo firewall-cmd --reload
success
```


5. Tests manuels
☀️ Effectuez des requêtes DNS manuellement depuis le serveur DNS lui-même dans un premier temps

# dig permet de faire des requêtes DNS manuellement sous Linux
# cette commande demande à 10.6.2.12 à quelle IP correspond web.tp6.b1
dig web.tp6.b1 @10.6.2.12

# lui doit fonctionner aussi
dig dns.tp6.b1 @10.6.2.12

# et ça aussi dukou !
dig ynov.com @10.6.2.12

# et on devrait aussi pouvoir faire l'inverse en ajoutant -x :
# demander quel est le nom qui correspond à une IP donnée
dig -x 10.6.2.11 @10.6.2.12
dig -x 10.6.2.12 @10.6.2.12

```
[lou@dns ~]$ dig web.tp6.b1 @10.6.2.12

; <<>> DiG 9.16.23-RH <<>> web.tp6.b1 @10.6.2.12
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 3766
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: bdfd6ba11af3a9370100000067167aaf246235cf0f37067e (good)
;; QUESTION SECTION:
;web.tp6.b1.                    IN      A

;; ANSWER SECTION:
web.tp6.b1.             86400   IN      A       10.6.2.11

;; Query time: 0 msec
;; SERVER: 10.6.2.12#53(10.6.2.12)
;; WHEN: Mon Oct 21 18:00:47 CEST 2024
;; MSG SIZE  rcvd: 83

[lou@dns ~]$ dig dns.tp6.b1 @10.6.2.12

; <<>> DiG 9.16.23-RH <<>> dns.tp6.b1 @10.6.2.12
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52614
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 324ba51729a454720100000067167ab2dd568bac0dc971b8 (good)
;; QUESTION SECTION:
;dns.tp6.b1.                    IN      A

;; ANSWER SECTION:
dns.tp6.b1.             86400   IN      A       10.6.2.12

;; Query time: 0 msec
;; SERVER: 10.6.2.12#53(10.6.2.12)
;; WHEN: Mon Oct 21 18:00:50 CEST 2024
;; MSG SIZE  rcvd: 83

[lou@dns ~]$ dig ynov.com @10.6.2.12

; <<>> DiG 9.16.23-RH <<>> ynov.com @10.6.2.12
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15019
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 9b3478f03872f5980100000067167aba9fa3b2a4a4227437 (good)
;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               300     IN      A       104.26.11.233
ynov.com.               300     IN      A       104.26.10.233
ynov.com.               300     IN      A       172.67.74.226

;; Query time: 261 msec
;; SERVER: 10.6.2.12#53(10.6.2.12)
;; WHEN: Mon Oct 21 18:00:58 CEST 2024
;; MSG SIZE  rcvd: 113

[lou@dns ~]$ dig -x 10.6.2.11 @10.6.2.12

; <<>> DiG 9.16.23-RH <<>> -x 10.6.2.11 @10.6.2.12
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16184
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 911d1d56ccd66dc40100000067167ac5a71ef2e312281b94 (good)
;; QUESTION SECTION:
;11.2.6.10.in-addr.arpa.                IN      PTR

;; ANSWER SECTION:
11.2.6.10.in-addr.arpa. 86400   IN      PTR     web.tp6.b1.

;; Query time: 0 msec
;; SERVER: 10.6.2.12#53(10.6.2.12)
;; WHEN: Mon Oct 21 18:01:09 CEST 2024
;; MSG SIZE  rcvd: 103

[lou@dns ~]$ dig -x 10.6.2.12 @10.6.2.12

; <<>> DiG 9.16.23-RH <<>> -x 10.6.2.12 @10.6.2.12
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18321
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 242a0035177ea17d0100000067167acc8b081a43f07e8c3e (good)
;; QUESTION SECTION:
;12.2.6.10.in-addr.arpa.                IN      PTR

;; ANSWER SECTION:
12.2.6.10.in-addr.arpa. 86400   IN      PTR     dns.tp6.b1.

;; Query time: 0 msec
;; SERVER: 10.6.2.12#53(10.6.2.12)
;; WHEN: Mon Oct 21 18:01:16 CEST 2024
;; MSG SIZE  rcvd: 103
```


☀️ Effectuez une requête DNS manuellement depuis client1.tp6.b1

pour obtenir l'adresse IP qui correspond au nom web.tp6.b1

```
lou@client6:~$ dig web.tp6

; <<>> DiG 9.18.28-0ubuntu0.24.04.1-Ubuntu <<>> web.tp6
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: SERVFAIL, id: 65482
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;web.tp6.			IN	A

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Mon Oct 21 18:06:11 CEST 2024
;; MSG SIZE  rcvd: 36

```


☀️ Capturez une requête DNS et la réponse de votre serveur

vous pouvez télécharger Wireshark sur client1.tp6.b1 mais bon... bourrin
sinon vous pouvez lancer une capture réseau avec tcpdump, on pourra ensuite ouvrir le fichier avec Wireshark (sur votre PC, pas dans une VM) pour visualiser


# principe de la commande
tcpdump -w <NOM_DU_FICHIER> -i <INTERFACE_A_CAPTURER>

# par exemple, si vous voulez enregistrer tout ce qui passe par enp0s3
# et enregistrer ça dans un fichier toto.pcap
tcpdump -w toto.pcap -i enp0s3

[toto](TP6/toto.pcapng)

3. Serveur DHCP
What ?! Again ?!
Nan nan juste, on va aller changer une ligne dans la configuration de dhcp.tp6.b1.
Bah oui ! On a notre propre serveur DNS maintenant ! Faut le dire à nos clients qu'ils peuvent l'utiliser :d
➜ Editez la configuration du serveur DHCP sur dhcp.tp6.b1

faut qu'il file l'adresse IP 10.6.2.12 comme DNS à tous les nouveaux clients !

☀️ Créez un nouveau client client2.tp6.b1 vitefé

supprimez le client1 si vous voulez, on a pu besoin
récupérez une IP en DHCP sur ce nouveau client2.tp6.b1

vérifiez que vous avez bien 10.6.2.12 comme serveur DNS à contacter

commande dans le mémo pour voir le serveur DNS connu actuellement



➜ Vous devriez pouvoir visiter http://web.tp6.b1 avec le navigateur, ça devrait fonctionner sans aucune autre action.