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


III. LAN serveurzzzz
Troisième partie, dédiée à la configuration de nos deux serveurs. Pour l'instant c'est juste des Rocky sans âme :d

1. Serveur Web

A faire sur web.tp6.b1.

Document dédié au serveur Web

2. Serveur DNS

A faire sur dns.tp6.b1.

Document dédié au setup serveur DNS

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