### TP 2


 I. 1. Quelques pings :
 - 🌞 Prouvez que votre configuration est effective

 ```
 PS C:\Users\loulo> Get-NetIPAddress -InterfaceAlias "Ethernet"


IPAddress         : fe80::d259:86eb:c0f4:4851%20
InterfaceIndex    : 20
InterfaceAlias    : Ethernet
AddressFamily     : IPv6
Type              : Unicast
PrefixLength      : 64
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Preferred
ValidLifetime     :
PreferredLifetime :
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 10.111.222.201
InterfaceIndex    : 20
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     :
PreferredLifetime :
SkipAsSource      : False
PolicyStore       : ActiveStore
 ```

 - 🌞 Tester que votre LAN + votre adressage IP est fonctionnel 

```
PS C:\Users\loulo> ping 10.111.222.202

Envoi d’une requête 'Ping'  10.111.222.202 avec 32 octets de données :
Réponse de 10.111.222.202 : octets=32 temps=1 ms TTL=128
Réponse de 10.111.222.202 : octets=32 temps=2 ms TTL=128
Réponse de 10.111.222.202 : octets=32 temps=2 ms TTL=128
Réponse de 10.111.222.202 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 10.111.222.202:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 2ms, Moyenne = 1ms
```

 - 🌞 Capture de ping 


[ping](./ping.pcapng)



   II. 1. Animal numérique 

 - 🌞 Sur le PC serveur
    
```
PS C:\ecole\Fonct. Réseaux\TP2\netcat-1.11> .\nc64.exe -l -p 9999
yhhrt
dfgsdgsd
fgsd
gf
dsfg
ds
fh
```

 - 🌞 Sur le PC serveur toujours

```
PS C:\Windows\system32> netstat -a -b -n
 [svchost.exe]
  TCP    0.0.0.0:9999           0.0.0.0:0              LISTENING
```

 - 🌞 Sur le PC client 

```
PS C:\ecole\Fonct. Réseaux\TP2\netcat-1.11> .\nc64.exe 10.111.222.202
```

 - 🌞 Echangez-vous des messages 
```
PS C:\ecole\Fonct. Réseaux\TP2\netcat-1.11> .\nc64.exe 10.111.222.202 9999
Bonjour
dfgdf
tdc
```

 - 🌞 Utilisez une commande qui permet de voir la connexion en cours

```
Serveur :
PS C:\Windows\system32> netstat -a -b -n
 [svchost.exe]
  TCP    0.0.0.0:9999           0.0.0.0:0              LISTENING
```
 - 🌞 Faites une capture Wireshark complète d'un échange

[netcap1.pcap](netcat1.pcapng)

 - 🌞 Inversez les rôles
```
Client :
PS C:\Windows\system32> netstat -a -b -n
 [svchost.exe]
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING
 Impossible d’obtenir les informations de propriétaire
  TCP    0.0.0.0:5040           0.0.0.0:0              LISTENING
```
[netcat2.pcap](netcap2.pcapng)

 - 🌞 Utilisez Wireshark pour capturer du trafic HTTP

[http.pcap](http.pcapng)

 - 🌞 Pour les 5 applications 
```
PS C:\Windows\system32> netstat -a -b -n | Select-String spotify -Context 0,1

>  [Spotify.exe]
    TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING
>  [Spotify.exe]
    TCP    0.0.0.0:58228          0.0.0.0:0              LISTENING
>  [Spotify.exe]
    TCP    10.33.77.86:21025      35.186.224.24:443      ESTABLISHED
```
[service1.pcap](service1.pcapng)

'''
PS C:\Windows\system32> netstat -a -b -n | Select-String discord -Context 0,1

>  [Discord.exe]
    TCP    10.33.77.86:21270      162.159.130.234:443    ESTABLISHED
>  [Discord.exe]
    TCP    10.33.77.86:49590      20.199.120.182:443     ESTABLISHED
>  [Discord.exe]
    TCP    127.0.0.1:49725        127.0.0.1:49726        ESTABLISHED
>  [Discord.exe]
    UDP    0.0.0.0:58528          162.159.135.232:443
>  [Discord.exe]
    UDP    0.0.0.0:58990          34.120.22.49:443
>  [Discord.exe]
    UDP    0.0.0.0:62251          *:*
'''

[service2.pcap](service2.pcapng)

'''

'''
[service3.pcap](service3.pcapng)

'''

'''
[service4.pcap](service4.pcapng)

'''

'''
[service5.pcap](service5.pcapng)

