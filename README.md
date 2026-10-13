# TP Final CCNA — CDC 1 : routage dynamique EIGRP

Infrastructure réseau multisite de Satom IT & Learning Solutions, réalisée sur GNS3 avec cinq routeurs Cisco c7200. Ce CDC 1 configure EIGRP (AS 100) sur les cinq routeurs, vérifie les voisinages et les routes, identifie le chemin entre les sites et démontre le basculement automatique en cas de panne d'un lien.

Tout ce qui est affiché ci-dessous est copié depuis les consoles du lab. Les extraits sont signalés comme tels.

## 1. Topologie réelle et plan d'adressage

Capture GNS3 de la topologie (les étiquettes de R3 affichées par GNS3 ne correspondent pas au câblage réel, voir la section 4.3) :

<img width="1197" height="543" alt="image" src="https://github.com/user-attachments/assets/3b7899be-117a-4326-ace2-93281f6ec303" />

Schéma annoté avec le câblage réel, les adresses et les chemins empruntés :

![Schéma annoté du CDC 1](schema-annote.png)

| N° | Liaison | Interfaces réelles | Sous-réseau | Adresses |
|---|---|---|---|---|
| 1 | R1 ↔ R2 | R1 `Fa1/0` ↔ R2 `Fa0/0` | 192.168.1.0/31 | R1 .0 / R2 .1 |
| 2 | R2 ↔ R3 | R2 `Fa1/0` ↔ R3 `Fa1/0` | 192.168.2.0/31 | R2 .0 / R3 .1 |
| 3 | R3 ↔ R4 | R3 `Fa0/0` ↔ R4 `Fa1/0` | 192.168.3.0/31 | R3 .0 / R4 .1 |
| 4 | R1 ↔ R4 (série) | R1 `Serial2/0` ↔ R4 `Serial3/0` | 192.168.4.0/31 | R1 .0 / R4 .1 |
| 5 | R1 ↔ R5 | R1 `Fa1/1` ↔ R5 `Fa0/0` | 192.168.5.0/31 | R1 .0 / R5 .1 |
| 6 | R5 ↔ R4 | R5 `Fa1/0` ↔ R4 `Fa0/0` | 192.168.6.0/31 | R5 .0 / R4 .1 |

| Élément | Réseau | Équipement | Adresse |
|---|---|---|---|
| LAN siège | 192.168.0.0/24 | R1 `Fa0/0` | 192.168.0.1 (passerelle), PC1 : 192.168.0.10 |
| LAN entrepôt | 192.168.7.0/24 | R4 `Fa2/0` | 192.168.7.1 (passerelle), PC2 : 192.168.7.10 |
| Nuage simulé | 10.40.10.0/24 | R5 `Loopback0` | 10.40.10.1 |

Les adresses non fixées par le sujet (passerelles des LAN en `.1`, PC en `.10`) suivent une convention simple : la passerelle est la première adresse du réseau, les PC sont en dehors de cette adresse.

## 2. Inventaire du lab (lecture seule)

- GNS3 2.2.61, cinq routeurs Cisco 7206VXR avec NPE-400.
- IOS 15.2(4)S7, confirmé par `show version` sur R2 à R5 et par l'en-tête de configuration de R1.
- Cartes de R5 (`show inventory`) : un module d'entrée-sortie FastEthernet, deux `PA-FE-TX` et une `PA-4T+`. Aucune carte GigabitEthernet.
- Configurations de départ vierges : nom du routeur uniquement, aucune adresse IP, aucun routage.
- Écart avec le schéma fourni : les liens 1 à 3 sont en FastEthernet et non en GigabitEthernet.

## 3. Configuration des adresses IP

Les masques `/31` (`255.255.255.254`) sont acceptés par l'IOS 15.2(4)S7 : aucune erreur à la saisie. Chaque configuration a été sauvegardée (`write memory`).

```
R1:
interface FastEthernet0/0
 description LAN siege vers PC1
 ip address 192.168.0.1 255.255.255.0
 no shutdown
interface FastEthernet1/0
 description Lien1 vers R2 Fa0/0
 ip address 192.168.1.0 255.255.255.254
 no shutdown
interface Serial2/0
 description Lien4 serie vers R4 Se3/0
 ip address 192.168.4.0 255.255.255.254
 no shutdown
interface FastEthernet1/1
 description Lien5 vers R5 Fa0/0
 ip address 192.168.5.0 255.255.255.254
 no shutdown

R2:
interface FastEthernet0/0
 description Lien1 vers R1 Fa1/0
 ip address 192.168.1.1 255.255.255.254
 no shutdown
interface FastEthernet1/0
 description Lien2 vers R3 Fa1/0
 ip address 192.168.2.0 255.255.255.254
 no shutdown

R3 (configuration finale, après correction du câblage, voir 4.3) :
interface FastEthernet0/0
 description Lien3 vers R4 Fa1/0
 ip address 192.168.3.0 255.255.255.254
interface FastEthernet1/0
 description Lien2 vers R2 Fa1/0
 ip address 192.168.2.1 255.255.255.254

R4:
interface FastEthernet1/0
 description Lien3 vers R3 Fa0/0
 ip address 192.168.3.1 255.255.255.254
 no shutdown
interface Serial3/0
 description Lien4 serie vers R1 Se2/0
 ip address 192.168.4.1 255.255.255.254
 no shutdown
interface FastEthernet0/0
 description Lien6 vers R5 Fa1/0
 ip address 192.168.6.1 255.255.255.254
 no shutdown
interface FastEthernet2/0
 description LAN entrepot vers PC2
 ip address 192.168.7.1 255.255.255.0
 no shutdown

R5:
interface FastEthernet0/0
 description Lien5 vers R1 Fa1/1
 ip address 192.168.5.1 255.255.255.254
 no shutdown
interface FastEthernet1/0
 description Lien6 vers R4 Fa0/0
 ip address 192.168.6.0 255.255.255.254
 no shutdown
```

### 3.1 Tests des liaisons directes

Un ping vers l'adresse du voisin n'aboutit que si le câblage correspond au plan, car chaque lien a son propre sous-réseau. Le premier ping perd parfois un paquet (découverte du voisin), seule la dernière tentative est reportée.

| Depuis | Commande | Résultat |
|---|---|---|
| R1 | `ping 192.168.1.1` (lien 1) | 100 % (5/5), 28/32/40 ms |
| R2 | `ping 192.168.2.1` (lien 2) | 100 % (5/5), 20/27/32 ms |
| R3 | `ping 192.168.3.1` (lien 3) | 100 % (5/5), 20/28/44 ms |
| R1 | `ping 192.168.4.1` (lien 4, série) | 100 % (5/5), 20/28/40 ms |
| R1 | `ping 192.168.5.1` (lien 5) | 100 % (5/5), 20/26/32 ms |
| R5 | `ping 192.168.6.1` (lien 6) | 100 % (5/5), 20/27/36 ms |

### 3.2 Incident : câblage de R3 inversé par rapport au dessin GNS3

Avec les adresses placées selon le dessin GNS3, les pings R3 → R4 et R2 → R3 donnaient `Success rate is 0 percent (0/5)`. Les voisins CDP, qui lisent les câbles réels indépendamment des adresses IP, ont montré que R3 est câblé à l'inverse du dessin :

```
R3#show cdp neighbors
Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
R2               Fas 1/0           123               R    7206VXR   Fas 1/0
R4               Fas 0/0           120               R    7206VXR   Fas 1/0
```

Un redémarrage de R3 n'a rien changé (même résultat CDP). Correction : les adresses de R3 ont été échangées pour suivre le câblage réel (`Fa0/0` en `192.168.3.0` vers R4, `Fa1/0` en `192.168.2.1` vers R2), les descriptions de R2 et R4 ont été corrigées, puis les trois routeurs ont été sauvegardés. Les pings R2 → R3 et R3 → R4 sont ensuite passés à 100 %. Le schéma annoté de la section 1 reprend le câblage réel.

## 4. Nuage simulé

Le TP est réalisé à distance du réseau de la classe : la passerelle `10.40.10.1` est simulée par une interface `Loopback0` sur R5, annoncée par EIGRP aux autres routeurs.

```
R5:
interface Loopback0
 description Nuage simule 10.40.10.1
 ip address 10.40.10.1 255.255.255.0
 no shutdown
```

## 5. Configuration EIGRP (AS 100)

La commande `network` active EIGRP sur les interfaces dont l'adresse est dans la plage (wildcard `0.0.0.1` pour un lien /31, `0.0.0.255` pour un LAN /24). `passive-interface` protège les interfaces qui ne doivent former aucun voisinage (LAN et loopback) : leur réseau reste annoncé.

```
R1:
router eigrp 100
 network 192.168.0.0 0.0.0.255
 network 192.168.1.0 0.0.0.1
 network 192.168.4.0 0.0.0.1
 network 192.168.5.0 0.0.0.1
 no auto-summary
 passive-interface FastEthernet0/0

R2:
router eigrp 100
 network 192.168.1.0 0.0.0.1
 network 192.168.2.0 0.0.0.1
 no auto-summary

R3:
router eigrp 100
 network 192.168.2.0 0.0.0.1
 network 192.168.3.0 0.0.0.1
 no auto-summary

R4:
router eigrp 100
 network 192.168.3.0 0.0.0.1
 network 192.168.4.0 0.0.0.1
 network 192.168.6.0 0.0.0.1
 network 192.168.7.0 0.0.0.255
 no auto-summary
 passive-interface FastEthernet2/0

R5:
router eigrp 100
 network 192.168.5.0 0.0.0.1
 network 192.168.6.0 0.0.0.1
 network 10.40.10.0 0.0.0.255
 no auto-summary
 passive-interface Loopback0
```

Dans le `show running-config`, IOS affiche parfois `network 192.168.0.0` sans wildcard (commande saisie avec `0.0.0.255`), et n'affiche pas `no auto-summary`. Les routes apprises sont bien des sous-réseaux (`/31`, `/24`), donc sans résumé automatique.

Configuration des PC (VPCS), enregistrée avec `save` :

```
PC1> ip 192.168.0.10/24 192.168.0.1
PC2> ip 192.168.7.10/24 192.168.7.1
```

## 6. Preuves de fonctionnement

### 6.1 Voisinages EIGRP

Messages d'adjacence sur chaque routeur lors de la mise en route :

```
R1: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.1.1 (FastEthernet1/0) is up: new adjacency
R1: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.4.1 (Serial2/0) is up: new adjacency
R1: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.5.1 (FastEthernet1/1) is up: new adjacency
R2: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.1.0 (FastEthernet0/0) is up: new adjacency
R2: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.2.1 (FastEthernet1/0) is up: new adjacency
R3: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.2.0 (FastEthernet1/0) is up: new adjacency
R3: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.3.1 (FastEthernet0/0) is up: new adjacency
R4: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.4.0 (Serial3/0) is up: new adjacency
R4: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.3.0 (FastEthernet1/0) is up: new adjacency
R4: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.6.0 (FastEthernet0/0) is up: new adjacency
R5: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.5.0 (FastEthernet0/0) is up: new adjacency
R5: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.6.1 (FastEthernet1/0) is up: new adjacency
```

Table de voisinage de R1 : R1 est voisin des trois routeurs auxquels il est relié.

```
R1#show ip eigrp neighbors
EIGRP-IPv4 Neighbors for AS(100)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
2   192.168.5.1             Fa1/1                    14 00:03:02   43   258  0  5
1   192.168.4.1             Se2/0                    11 00:03:14   41   246  0  14
0   192.168.1.1             Fa1/0                    12 00:03:33   36   216  0  14
```

### 6.2 Table de routage de R1

Routes apprises par EIGRP :

```
R1#show ip route eigrp
Gateway of last resort is not set

      10.0.0.0/24 is subnetted, 1 subnets
D        10.40.10.0 [90/156160] via 192.168.5.1, 00:03:14, FastEthernet1/1
      192.168.2.0/31 is subnetted, 1 subnets
D        192.168.2.0 [90/30720] via 192.168.1.1, 00:03:31, FastEthernet1/0
      192.168.3.0/31 is subnetted, 1 subnets
D        192.168.3.0 [90/33280] via 192.168.5.1, 00:03:14, FastEthernet1/1
                     [90/33280] via 192.168.1.1, 00:03:14, FastEthernet1/0
      192.168.6.0/31 is subnetted, 1 subnets
D        192.168.6.0 [90/30720] via 192.168.5.1, 00:03:19, FastEthernet1/1
D     192.168.7.0/24 [90/33280] via 192.168.5.1, 00:03:14, FastEthernet1/1
```

Les routes `D` couvrent tous les réseaux du plan d'adressage qui ne sont pas directement connectés à R1. Les réseaux connectés à R1 sont `192.168.0.0/24`, `192.168.1.0/31`, `192.168.4.0/31` et `192.168.5.0/31`.

> **À compléter avant le rendu :** coller ici la sortie complète de `show ip route` sur R1 (avec les routes connectées `C` et `L`).

Route vers le LAN de l'entrepôt, en détail :

```
R1#show ip route 192.168.7.0
Routing entry for 192.168.7.0/24
  Known via "eigrp 100", distance 90, metric 33280, type internal
  Redistributing via eigrp 100
  Last update from 192.168.5.1 on FastEthernet1/1, 00:05:44 ago
  Routing Descriptor Blocks:
  * 192.168.5.1, from 192.168.5.1, 00:05:44 ago, via FastEthernet1/1
      Route metric is 33280, traffic share count is 1
      Total delay is 300 microseconds, minimum bandwidth is 100000 Kbit
      Reliability 255/255, minimum MTU 1500 bytes
      Loading 1/255, Hops 2
```

### 6.3 Table de topologie EIGRP de R1

```
R1#show ip eigrp topology all-links
EIGRP-IPv4 Topology Table for AS(100)/ID(192.168.5.0)
Codes: P - Passive, A - Active, U - Update, Q - Query, R - Reply,
       r - reply Status, s - sia Status

P 192.168.3.0/31, 2 successors, FD is 33280, serno 44
        via 192.168.1.1 (33280/30720), FastEthernet1/0
        via 192.168.5.1 (33280/30720), FastEthernet1/1
        via 192.168.4.1 (2172416/28160), Serial2/0
P 192.168.2.0/31, 1 successors, FD is 30720, serno 5
        via 192.168.1.1 (30720/28160), FastEthernet1/0
        via 192.168.4.1 (2180096/35840), Serial2/0
P 192.168.0.0/24, 1 successors, FD is 28160, serno 1
        via Connected, FastEthernet0/0
P 192.168.7.0/24, 1 successors, FD is 33280, serno 45
        via 192.168.5.1 (33280/30720), FastEthernet1/1
        via 192.168.4.1 (2172416/28160), Serial2/0
P 192.168.1.0/31, 1 successors, FD is 28160, serno 10
        via Connected, FastEthernet1/0
P 10.40.10.0/24, 1 successors, FD is 156160, serno 42
        via 192.168.5.1 (156160/128256), FastEthernet1/1
        via 192.168.4.1 (2300416/156160), Serial2/0
P 192.168.6.0/31, 1 successors, FD is 30720, serno 43
        via 192.168.5.1 (30720/28160), FastEthernet1/1
        via 192.168.4.1 (2172416/28160), Serial2/0
P 192.168.4.0/31, 1 successors, FD is 2169856, serno 12
        via Connected, Serial2/0
P 192.168.5.0/31, 1 successors, FD is 28160, serno 41
        via Connected, FastEthernet1/1
        via 192.168.4.1 (2174976/30720), Serial2/0
```

### 6.4 Pings depuis PC1

```
PC1> ping 192.168.7.10
84 bytes from 192.168.7.10 icmp_seq=1 ttl=61 time=106.190 ms
84 bytes from 192.168.7.10 icmp_seq=2 ttl=61 time=91.045 ms
84 bytes from 192.168.7.10 icmp_seq=3 ttl=61 time=90.726 ms
84 bytes from 192.168.7.10 icmp_seq=4 ttl=61 time=92.281 ms
84 bytes from 192.168.7.10 icmp_seq=5 ttl=61 time=90.425 ms
```

Ping de PC1 vers le nuage simulé (relevé avant un redémarrage du lab) :

```
PC1> ping 10.40.10.1
84 bytes from 10.40.10.1 icmp_seq=1 ttl=254 time=45.645 ms
84 bytes from 10.40.10.1 icmp_seq=2 ttl=254 time=46.946 ms
84 bytes from 10.40.10.1 icmp_seq=3 ttl=254 time=45.738 ms
84 bytes from 10.40.10.1 icmp_seq=4 ttl=254 time=45.763 ms
84 bytes from 10.40.10.1 icmp_seq=5 ttl=254 time=46.588 ms
```

Ping de R1 vers le nuage simulé (après rétablissement du lien R1–R5) :

```
R1#ping 10.40.10.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.40.10.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 24/27/36 ms
```

Le TTL de 61 vers PC2 correspond à trois routeurs traversés (R1, R5, R4), donc au chemin nominal par R5. Le TTL de 254 vers `10.40.10.1` correspond à un seul routeur traversé avant d'atteindre la loopback de R5.

## 7. Test de bascule : coupure de la liaison R1 ↔ R5 (`Fa1/1` de R1)

Le test est fait avec un ping continu de PC1 vers PC2 (`ping 192.168.7.10 -t`) pendant la coupure.

### 7.1 Avant la coupure (relevé lors d'un premier essai, même état du réseau)

```
R1#show ip route 192.168.7.0
Routing entry for 192.168.7.0/24
  Known via "eigrp 100", distance 90, metric 33280, type internal
  Redistributing via eigrp 100
  Last update from 192.168.5.1 on FastEthernet1/1, 00:05:44 ago
  Routing Descriptor Blocks:
  * 192.168.5.1, from 192.168.5.1, 00:05:44 ago, via FastEthernet1/1
      Route metric is 33280, traffic share count is 1
      Total delay is 300 microseconds, minimum bandwidth is 100000 Kbit
      Reliability 255/255, minimum MTU 1500 bytes
      Loading 1/255, Hops 2

R1#show ip eigrp neighbors
EIGRP-IPv4 Neighbors for AS(100)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
2   192.168.5.1             Fa1/1                    13 00:05:34   41   246  0  8
1   192.168.4.1             Se2/0                    14 00:05:34   41   246  0  15
0   192.168.1.1             Fa1/0                    11 00:05:34  660  3960  0  16
```

Pendant la phase avant coupure, les réponses du ping ont un TTL de 61 (trois routeurs traversés).

### 7.2 Coupure du lien

```
R1#configure terminal
R1(config)#interface FastEthernet1/1
R1(config-if)#shutdown
R1(config-if)#end
*Oct 12 09:23:50.903: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.5.1 (FastEthernet1/1) is down: interface down
```

### 7.3 Après la coupure (R1)

```
R1#show ip route 192.168.7.0
Routing entry for 192.168.7.0/24
  Known via "eigrp 100", distance 90, metric 2172416, type internal
  Redistributing via eigrp 100
  Last update from 192.168.4.1 on Serial2/0, 00:00:17 ago
  Routing Descriptor Blocks:
  * 192.168.4.1, from 192.168.4.1, 00:00:17 ago, via Serial2/0
      Route metric is 2172416, traffic share count is 1
      Total delay is 20100 microseconds, minimum bandwidth is 1544 Kbit
      Reliability 255/255, minimum MTU 1500 bytes
      Loading 1/255, Hops 1

R1#show ip eigrp neighbors
EIGRP-IPv4 Neighbors for AS(100)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   192.168.4.1             Se2/0                    12 00:42:19   40   240  0  25
0   192.168.1.1             Fa1/0                    11 00:42:19   92   552  0  36
```

Le voisin R5 (`192.168.5.1`) a disparu et R1 utilise désormais la liaison série vers R4.

### 7.4 Continuité du trafic pendant la coupure (PC1, extrait du ping en continu)

```
84 bytes from 192.168.7.10 icmp_seq=16 ttl=61 time=91.859 ms
84 bytes from 192.168.7.10 icmp_seq=17 ttl=61 time=91.336 ms
192.168.7.10 icmp_seq=18 timeout
192.168.7.10 icmp_seq=19 timeout
192.168.7.10 icmp_seq=20 timeout
192.168.7.10 icmp_seq=21 timeout
192.168.7.10 icmp_seq=22 timeout
192.168.7.10 icmp_seq=23 timeout
84 bytes from 192.168.7.10 icmp_seq=24 ttl=62 time=60.726 ms
84 bytes from 192.168.7.10 icmp_seq=25 ttl=62 time=60.260 ms
```

Extrait : le ping complet compte 43 paquets : 17 réponses en TTL 61, 6 timeouts, puis 20 réponses en TTL 62 jusqu'à l'arrêt avec Ctrl+C.

### 7.5 Rétablissement du lien et retour au chemin d'origine

```
R1#configure terminal
R1(config)#interface FastEthernet1/1
R1(config-if)#no shutdown
R1(config-if)#end
*Oct 12 09:24:55.339: %LINK-3-UPDOWN: Interface FastEthernet1/1, changed state to up
*Oct 12 09:25:00.567: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.5.1 (FastEthernet1/1) is up: new adjacency
```

```
R1#show ip route 192.168.7.0
Routing entry for 192.168.7.0/24
  Known via "eigrp 100", distance 90, metric 33280, type internal
  Redistributing via eigrp 100
  Last update from 192.168.5.1 on FastEthernet1/1, 00:01:42 ago
  Routing Descriptor Blocks:
  * 192.168.5.1, from 192.168.5.1, 00:01:42 ago, via FastEthernet1/1
      Route metric is 33280, traffic share count is 1
      Total delay is 300 microseconds, minimum bandwidth is 100000 Kbit
      Reliability 255/255, minimum MTU 1500 bytes
      Loading 1/255, Hops 2
```

R1 a retrouvé la route d'origine, via R5 (métrique 33280).

### 7.6 Analyse

- 6 paquets perdus (`icmp_seq` 18 à 23), puis le trafic reprend seul, sans intervention.
- Le chemin de secours utilisé est la liaison série (métrique 2172416).
- TTL 62 après la bascule : le trafic traverse deux routeurs (R1, R4) au lieu de trois, ce qui explique aussi un temps de réponse plus bas dans GNS3 (environ 60 ms au lieu de 91 ms). La métrique EIGRP classe quand même la série comme le moins bon chemin, car elle se base sur la bande passante déclarée (1544 kbit/s).
- Explication du choix de la série, confirmée par la table de topologie de R1 : pour `192.168.7.0`, la route d'origine passe par R5 (FD 33280). La série est annoncée par R4 avec une distance de 28160, inférieure à 33280 : c'est un chemin de secours « faisable » (condition de faisabilité), et EIGRP bascule dessus immédiatement, sans recalcul complet. La table de topologie de R1 ne contient aucun chemin passant par R2 pour ce réseau.

## 8. Rapport court

### Choix de la métrique et chemin entre VPC1 et VPC2

EIGRP calcule : métrique = 256 × (10⁷ / bande passante minimale en kbit/s + somme des délais en dizaines de µs).

Toutes les liaisons étant en FastEthernet (100 Mbit/s, 100 µs par interface), le chemin R1 → R5 → R4 affiche un délai de 300 µs et une bande passante minimale de 100000 kbit/s, soit 256 × (100 + 30) = 33280, valeur affichée par R1 pour `192.168.7.0`. Un chemin par R2 et R3 compterait une interface de plus et coûterait donc plus cher (calcul théorique : 256 × (100 + 40) = 35840) ; il n'apparaît d'ailleurs pas dans la table de topologie de R1 pour ce réseau. La liaison série (1544 kbit/s, délai de 20100 µs) est la moins bonne : 256 × (6476 + 2010) = 2172416, valeur affichée pendant la coupure. EIGRP retient donc le chemin de plus faible métrique : par R5.

### Nuage simulé

Le TP est fait à distance du réseau de la classe : le nuage `10.40.10.1` est simulé par une `Loopback0` sur R5, annoncée par EIGRP aux autres routeurs, sans route statique.

### Test de bascule

On a coupé l'interface `Fa1/1` de R1 pendant un ping continu de PC1 vers PC2. R1 a perdu le voisin R5 et a installé la route de secours par la liaison série (`192.168.4.1`, métrique 2172416). Six paquets ont été perdus, puis le trafic a repris automatiquement (TTL 62 au lieu de 61). Le lien a ensuite été rétabli : le voisin R5 est revenu en moins de 6 secondes (à 09:25:00) et R1 a retrouvé la route d'origine via R5 (métrique 33280).

### Procédure de test

1. Vérifier les voisinages et les routes (`show ip eigrp neighbors`, `show ip route`).
2. Ping PC1 → PC2 et PC1 → `10.40.10.1`.
3. Lancer `ping -t`, couper `Fa1/1` de R1 (`shutdown`), relever la route et les voisins, arrêter le ping, rétablir le lien (`no shutdown`) et vérifier le retour à la route d'origine.

### Écarts assumés par rapport au schéma

- Les liens 1 à 3 sont en FastEthernet et non en GigabitEthernet : le lab n'a pas de carte Giga. Cela change le chemin préféré : avec du FastEthernet partout, EIGRP choisit le chemin par R5.
- Le nuage est simulé par une `Loopback0` sur R5, parce que le TP est fait à distance du réseau de la classe.
- Le câblage réel de R3 est inversé par rapport au dessin GNS3 (vérifié avec CDP) : R2 `Fa1/0` ↔ R3 `Fa1/0` (lien 2), R3 `Fa0/0` ↔ R4 `Fa1/0` (lien 3). Les adresses de R3 suivent le câblage réel.

## 9. Fichiers de configuration

Configurations finales commentées des cinq routeurs, reprises des `show running-config` : [R1.conf](R1.conf), [R2.conf](R2.conf), [R3.conf](R3.conf), [R4.conf](R4.conf), [R5.conf](R5.conf).

## 10. QCM

Réponses de l'étudiant (l'énoncé des questions n'est pas reproduit ici) :

```
1. b) Un protocole IP numéro 88
2. b) Même numéro de système autonome
3. b) 90
4. a) Bande passante et délai
5. c) 224.0.0.10
6. a) La route est faisable (feasible successor)
7. b) Calculer les routes sans boucle et sans route de secours inutile
8. b) show ip eigrp neighbors
9. a) 5 secondes
10. b) 15 secondes
11. b) Elle arrête l'envoi des hellos sur l'interface, tout en continuant à annoncer le réseau
12. b) Désactivée par défaut
13. b) variance
14. a) Le chemin à plus petit coût total composé de bande passante et de délai
15. a) show ip protocols
16. c) 2
17. b) Externe
18. b) Feasibility condition / faisable distance
19. a) Elle permet un basculement immédiat sans recalcul complet
20. a) show ip eigrp topology
21. c) OSPF
22. a) 1
23. a) Le vecteur de distance ne connaît pas toute la topologie, l'état de lien la connaît
24. a) router-id
25. a) Pour vérifier que la convergence fonctionne et que le trafic est réacheminé
26. b) HDLC
27. a) PPP
28. a) Le signal d'horloge
29. a) Relier le réseau interne aux réseaux externes
30. b) TCP
31. a) Une étiquette (label)
32. b) Elle permet de simuler une liaison opérateur avec un débit et une horloge contrôlés
33. a) QoS
34. b) 1 500 octets
35. a) Pour absorber l'en-tête supplémentaire du tunnel sans fragmentation
36. a) Négocier et établir un canal sécurisé de gestion (ISAKMP SA)
37. a) Négocier les SA qui protégeront le trafic de données
38. b) ESP
39. b) AH
40. b) 50
41. b) 500
42. b) 4500
43. a) En mode tunnel, le paquet IP complet est chiffré dans un nouveau paquet
44. a) Définir les algorithmes de chiffrement et d'authentification de la SA de phase 2
45. a) Renouveler une clé de session indépendamment des précédentes
46. c) AES
47. b) MD5
48. a) 3 600 secondes
49. a) À une interface
50. a) Même politique ISAKMP, clés pré-partagées ou certificats compatibles, et trafic intéressant défini
51. a) Le trafic dont la source et la destination sont définies dans une ACL pour être chiffré
52. b) show crypto ipsec sa
53. a) Moins d'échanges et une meilleure robustesse
54. a) L'ACL définissant le trafic intéressant est incorrecte
55. a) Pour sécuriser les échanges entre les sites tout en gardant une politique centralisée
56. a) Dynamic Multipoint VPN
57. b) NHRP
58. a) Serveur NHRP et point central de connexion des spokes
59. a) mGRE (multipoint GRE)
60. a) 47
61. a) Il permet de relier plusieurs pairs sur une seule interface de tunnel
62. a) NHRP redirect et shortcut
63. a) Tout le trafic spoke-to-spoke passe par le hub
64. a) ip nhrp network-id
65. a) Associer l'adresse IP du tunnel à l'adresse NBMA (IP publique ou de transport)
66. a) NBMA est l'adresse de transport, tunnel IP est l'adresse logique du tunnel
67. a) Pour chiffrer les paquets GRE
68. a) Configurer le spoke avec l'adresse du hub et l'enregistrer auprès du hub
69. a) EIGRP ou BGP
70. a) show ip nhrp
71. a) Il empêche le routeur d'annoncer une route apprise sur la même interface
72. a) Il évite de configurer manuellement chaque tunnel entre chaque couple de sites
73. a) Le hub est un point central de signalisation, le spoke initie les tunnels vers le hub
74. a) L'adresse NBMA du hub, le ip nhrp nhs et la connectivité de transport
75. a) Indiquer au spoke l'adresse NHRP du serveur (hub)
76. a) 179
77. a) 20
78. c) 200
79. a) LOCAL_PREF
80. a) Le chemin le plus court est préféré
81. a) AS_PATH prepend
82. a) 100
83. a) LOCAL_PREF
84. a) Remplacer le prochain saut par l'adresse du routeur qui annonce
85. a) Annoncer un préfixe présent dans la table de routage locale
86. a) show ip bgp summary
87. a) Le routeur rejette la route pour éviter une boucle
88. a) Suggérer au voisin par quel point d'entrée il doit envoyer le trafic
89. a) Weight (propre à Cisco)
90. a) Éviter la nécessité d'un maillage complet de sessions iBGP
91. a) Pour garantir la stabilité de la session même si une interface physique tombe
92. a) Accepter ou refuser certaines routes annoncées ou reçues
93. a) 1
94. a) Vérifier l'état de la session et détecter une panne
95. a) Il peut attirer du trafic non désiré et exposer le réseau interne
96. a) OMP
97. a) Étendre des réseaux de niveau 2 sur un réseau de niveau 3
98. a) 4789
99. a) Un DMVPN ou une architecture SD-WAN avec contrôleur centralisé
100. a) Vérifier la connectivité de base, la couche physique et les voisinages avant d'analyser les protocoles
```
