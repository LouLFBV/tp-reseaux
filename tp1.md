## TP1 réseaux


- 🌞 Adresses IP de ta machine :

````
PS C:\Users\loulo> ipconfig /all

Carte Ethernet Ethernet :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   Suffixe DNS propre à la connexion. . . : home
   Description. . . . . . . . . . . . . . : Realtek PCIe GbE Family Controller
   Adresse physique . . . . . . . . . . . : 74-56-3C-46-3F-C0
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui

Carte réseau sans fil Wi-Fi :

   Adresse IPv4. . . . . . . . . . . . . .: 10.33.76.111
   
````
\
-🌞 Si t'as un accès internet normal, d'autres infos sont forcément dispos... :

```
PS C:\Users\loulo> ipconfig /all

Carte réseau sans fil Wi-Fi :
    Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254

   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                        
```
- 🌟 BONUS : Détermine s'il y a un pare-feu actif sur ta machine: 

```
PS C:\Users\loulo> $FWService = (Get-Service | ?{$_.Name -eq "mpssvc"});
PS C:\Users\loulo> $FWService | %{
>>     If($_.Status -eq "Running"){
>>         Write-Host "The $($_.DisplayName) service is running." -Foregroundcolor Green
>>         }Else{
>>         Write-Host "The $($_.DisplayName) service is stopped." -Foregroundcolor Red
>>         }
>>     };
The Pare-feu Windows Defender service is running.

```
\
-🌞 Envoie un ping vers...
```
PS C:\Users\loulo> ping 10.33.76.111

Envoi d’une requête 'Ping'  10.33.76.111 avec 32 octets de données :
Réponse de 10.33.76.111 : octets=32 temps<1ms TTL=128
Réponse de 10.33.76.111 : octets=32 temps<1ms TTL=128
Réponse de 10.33.76.111 : octets=32 temps<1ms TTL=128
Réponse de 10.33.76.111 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 10.33.76.111:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),


PS C:\Users\loulo> ping  127.0.0.1

Envoi d’une requête 'Ping'  127.0.0.1 avec 32 octets de données :
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 127.0.0.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```
\
-🌞 On continue avec ping. Envoie un ping vers...

```
PS C:\Users\loulo> ping 10.33.79.254

Envoi d’une requête 'Ping'  10.33.79.254 avec 32 octets de données :
Délai d’attente de la demande dépassé.

Statistiques Ping pour 10.33.79.254:
    Paquets : envoyés = 1, reçus = 0, perdus = 1 (perte 100%),
Ctrl+C



PS C:\Users\loulo> ping 10.33.78.236

Envoi d’une requête 'Ping'  10.33.78.236 avec 32 octets de données :
Réponse de 10.33.78.236 : octets=32 temps=10 ms TTL=128
Réponse de 10.33.78.236 : octets=32 temps=12 ms TTL=128
Réponse de 10.33.78.236 : octets=32 temps=10 ms TTL=128
Réponse de 10.33.78.236 : octets=32 temps=14 ms TTL=128

Statistiques Ping pour 10.33.78.236:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 10ms, Maximum = 14ms, Moyenne = 11ms


PS C:\Users\loulo> ping www.google.com

Envoi d’une requête 'ping' sur www.google.com [142.250.75.228] avec 32 octets de données :
Réponse de 142.250.75.228 : octets=32 temps=16 ms TTL=116
Réponse de 142.250.75.228 : octets=32 temps=20 ms TTL=116
Réponse de 142.250.75.228 : octets=32 temps=19 ms TTL=116
Réponse de 142.250.75.228 : octets=32 temps=16 ms TTL=116

Statistiques Ping pour 142.250.75.228:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 16ms, Maximum = 20ms, Moyenne = 17ms
```
\
- 🌞 Faire une requête DNS à la main
```
PS C:\Users\loulo> nslookup www.google.com
Serveur :   dns.google
Address:  8.8.8.8


PS C:\Users\loulo> nslookup www.thinkerview.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    www.thinkerview.com
Addresses:  2a06:98c1:3121::7
          2a06:98c1:3120::7
          188.114.96.7
          188.114.97.7

PS C:\Users\loulo> nslookup www.wikileaks.org
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    wikileaks.org
Addresses:  51.159.197.136
          80.81.248.21
Aliases:  www.wikileaks.org

PS C:\Users\loulo> nslookup www.torproject.org
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    www.torproject.org
Addresses:  2a01:4f8:fff0:4f:266:37ff:feae:3bbc
          2a01:4f8:fff0:4f:266:37ff:fe2c:5d19
          2620:7:6002:0:466:39ff:fe7f:1826
          2620:7:6002:0:466:39ff:fe32:e3dd
          2a01:4f9:c010:19eb::1
          116.202.120.166
          95.216.163.36
          204.8.99.144
          204.8.99.146
          116.202.120.165
```

- 🌞 J'attends dans le dépôt git de rendu un fichier ping.pcap :
[dns](dns.pcap)

- 🌞 Livrez un deuxième fichier : dns.pcap :
[ping](ping.pcap)



- 🌞 Effectue un scan du réseau auquel tu es connecté :

```
PS C:\Users\loulo> nmap -sn -PR 10.33.64.0/20
...
MAC Address: 7C:5A:1C:D3:D8:76 (Sophos)
Nmap scan report for 10.33.76.111
Host is up.
Nmap done: 4096 IP addresses (498 hosts up) scanned in 170.60 seconds
```

-  🌞 Changer d'adresse IP
```
PS C:\Users\loulo> ipconfig

...

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::1884:7156:6457:13d4%7
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.77.86
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
```