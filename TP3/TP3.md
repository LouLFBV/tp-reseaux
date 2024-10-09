TP3 : 32°13'34"N 95°03'27"W
Un TP3 plutôt court dédié au protocole ARP.


Sommaire


TP3 : 32°13'34"N 95°03'27"W

Sommaire


0. Prérequis
I. ARP basics

II. ARP dans un réseau local

1. Basics
2. ARP
3. Bonus : ARP poisoning




0. Prérequis


tout se fait en ligne de commande, sauf si c'est explicitement précisément autrement
je tente un truc risqué, ceux du matin, vous servez de crash test

vous allez utiliser vos partages de co dans la partie II

partie II qui se fait à 3 ou 4 (pas + SVP, c'est déjà le cirque à 4)
si ça fonctionne pas ce sera backup plan avec les câbles :c




I. ARP basics

⚠️ Pas besoin du partage de co pour le moment, restez sur le réseau de l'école en WiFi.

☀️ Avant de continuer...

affichez l'adresse MAC de votre carte WiFi !

```
PS C:\Users\loulo> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Intel(R) Wi-Fi 7 BE200 320MHz
   Adresse physique . . . . . . . . . . . : A0-B3-39-64-96-FF
```

☀️ Affichez votre table ARP

allez vous commencez à devenir grands, je vous donne pas la commande, demande à m'sieur internet !

```
PS C:\Users\loulo> arp -a

Interface : 10.33.77.86 --- 0x7
  Adresse Internet      Adresse physique      Type
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 192.168.56.1 --- 0x13
  Adresse Internet      Adresse physique      Type
  192.168.56.255        ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  230.0.0.1             01-00-5e-00-00-01     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  ```

☀️ Déterminez l'adresse MAC de la passerelle du réseau de l'école

la passerelle, vous connaissez son adresse IP normalement (cf TP1 ;) 
si vous avez un accès internet, votre PC a forcément l'adresse MAC de la passerelle dans sa table ARP
```
PS C:\Users\loulo> arp -a

Interface : 10.33.77.86 --- 0x7
  Adresse Internet      Adresse physique      Type
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
  ```

☀️ Supprimez la ligne qui concerne la passerelle

une commande pour supprimer l'adresse MAC de votre table ARP
si vous ré-affichez votre table ARP, y'a des chances que ça revienne presque tout de suite !

```
PS C:\Windows\system32> arp -d 10.33.79.254
```
☀️ Prouvez que vous avez supprimé la ligne dans la table ARP

en affichant la table ARP
si la ligne est déjà revenue, déconnecte-toi temporairement du réseau de l'école, et supprime-la de nouveau

```
PS C:\Windows\system32> arp -d 10.33.79.254
PS C:\Windows\system32> arp -a

Interface : 10.33.77.86 --- 0x7
  Adresse Internet      Adresse physique      Type
  10.33.78.234          34-6f-24-e2-da-27     dynamique
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```

☀️ Wireshark

capture arp1.pcap

lancez une capture Wireshark, puis supprimez la ligne de la passerelle dans la table ARP pendant que la capture est en cours
la capture doit contenir uniquement 2 trames :

un ARP request que votre PC envoie pour apprendre l'adresse MAC de la passerelle
et la réponse

[arp1.pcap](arp1.pcapng)



Si vous les voyez pas, ça veut dire que votre PC n'utilise pas du tout de connexion vers l'extérieur, genre vers internet. Ce qui paraît invraisemblable on est d'accord non ? Vous avez vu ce que ça fait avec netstat aux TPs précédents quand on affiche toutes les connexions réseau en cours ! Très peu de chance que votre PC ne contacte pas internet toutes les quelques secondes.


II. ARP dans un réseau local

⚠️ Avant de continuer, regroupez-vous par 3 ou 4, et connectez-vous au même partage de co avec le téléphone de l'un d'entre vous. Si ça fonctionne pas, vous pouvez faire cette partie sur le réseau de l'école mais moins bieng.

Dans cette situation, le téléphone agit comme une "box" :


c'est un switch

il permet à tout le monde d'être connecté à un même réseau local



c'est aussi un routeur

il fait passer les paquets du réseau local, à internet
et vice-versa
on dit que ce routeur est la passerelle du réseau local




1. Basics

⚠️ Avant de continuer, regroupez-vous par 3 ou 4, et connectez-vous au même partage de co avec le téléphone de l'un d'entre vous. Si ça fonctionne pas, vous pouvez faire cette partie sur le réseau de l'école mais moins bieng.

☀️ Déterminer

pour la carte réseau impliquée dans le partage de connexion (carte WiFi ?)
son adresse IP au sein du réseau local formé par le partage de co
son adresse MAC

```
PS C:\Users\loulo> ipconfig /all
...
Carte réseau sans fil Wi-Fi :

...

   Adresse IPv4. . . . . . . . . . . . . .: 172.20.10.2
   ...
```

☀️ DIY

changer d'adresse IP

vous pouvez le faire en interface graphique


faut procéder comme au TP1 :

vous respectez les informations que vous connaissez du réseau
remettez la même passerelle, le même masque, et le même serveur DNS
changez seulement votre adresse IP, ne changez que le dernier nombre


prouvez que vous avez bien changé d'IP

avec une commande !

```
PS C:\Users\loulo> ipconfig /all

Configuration IP de Windows

...

Carte réseau sans fil Wi-Fi :

...

   Adresse IPv4. . . . . . . . . . . . . .: 172.20.10.10(préféré)
```

☀️ Pingz !

vérifiez que vous pouvez tous vous ping avec ces adresses IP
vérifiez avec une commande ping que vous avez bien un accès internet


Pour vérifier l'accès internet avec un ping il suffit d'envoyer un ping vers une adresse IP publique ou un nom de domaine public que vous connaissez. Genre ping xkcd.com, ça fonctionne, peu importe, du moment que c'est sur internet.

```
PS C:\Users\loulo> ping 172.20.10.9

Envoi d’une requête 'Ping'  172.20.10.9 avec 32 octets de données :
Réponse de 172.20.10.9 : octets=32 temps=2012 ms TTL=128
Réponse de 172.20.10.9 : octets=32 temps=24 ms TTL=128
Réponse de 172.20.10.9 : octets=32 temps=71 ms TTL=128
Réponse de 172.20.10.9 : octets=32 temps=13 ms TTL=128

Statistiques Ping pour 172.20.10.9:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 13ms, Maximum = 2012ms, Moyenne = 530ms
```

2. ARP
☀️ Affichez votre table ARP !

normalement, après les ping, vous avez appris l'adresse MAC de tous les autres

➜ Wireshark that

lancez tous Wireshark
videz tous vos tables ARP
normalement, y'a presque pas de raisons que vos PCs se contactent entre eux spontanément donc les tables ARP devraient rester vides tant que vous faites rien
à tour de rôle, envoyez quelques ping entre vous
constatez les messages ARP spontanés qui précèdent vos ping

ARP request

envoyé en broadcast ff:ff:ff:ff:ff:ff

tout le monde les reçoit donc !


ARP reply

celui qui a été ping répond à celui qui a initié le `pingv
il l'informe que l'adresse IP qui a été ping correspond à son adresse MAC

[ping](TP3/pingarp.pcapng)



☀️ Capture arp2.pcap

ne contient que des ARP request et ARP reply
contient tout le nécessaire pour que votre table ARP soit populée avec l'adresse MAC de tout le monde

[arp2](TP3/arp2.pcapng)

3. Bonus : ARP poisoning


⚠️⚠️⚠️ Si le partage de co n'a pas fonctionné, il est hors de question que vous fassiez ça sur le réseau de l'école. Donc partage de co ou avec des VMs.

⭐ Empoisonner la table ARP de l'un des membres de votre réseau

il faut donc forcer l'injection de fausses informations dans la table ARP de quelqu'un d'autre
on peut le faire en envoyant des messages ARP que l'on a forgé nous-mêmes
avec quelques lignes de code, ou y'a déjà ce qu'il faut sur internet
faites vos recherches, demandez-moi si vous voulez de l'aide
affichez la table ARP de la victime une fois modifiée dans le compte-rendu

⭐ Mettre en place un MITM

MITM pour Man-in-the-middle
placez vous entre l'un des membres du réseau, et la passerelle
ainsi, dès que ce PC va sur internet, c'est à vous qu'il envoie tous ses messages
pour ça, il faut continuellement empoisonner la table ARP de la victime, et celle de la passerelle


Au moins tous ceux veulent faire de la sécu, il faudrait faire cette partie bonus ! Prenez-vous un peu la tête pour le réaliser, avec des VMs y'aura ptet moins de trucs dans tous les sens pour une première fois. Hésitez pas à m'appeler.

