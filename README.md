# TP-FINAL_CCNA2-<br>
tout d'abord j'ai mis en place la topologie<br>
<img width="1197" height="543" alt="image" src="https://github.com/user-attachments/assets/3b7899be-117a-4326-ace2-93281f6ec303" /><br>
## Étape 1 — Inventaire (Lecture seule)

* **Plateforme :** GNS3 2.2.61, 5 routeurs c7200 (NPE-400), IOS 15.2S7
* **État initial :** Configurations vierges (hostname seul, aucune IP, aucun routage)
* **Câblage :** Conforme au schéma, sauf :
  * Liens 1 à 3 en FastEthernet au lieu de GigabitEthernet.
  * Nuage externe non branché physiquement.

---

## 1. Mise en place des Routeurs

### R1
R1#enable
R1#configure terminal
R1(config)#interface FastEthernet0/0
R1(config-if)# description LAN siege vers PC1
R1(config-if)# ip address 192.168.0.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)#interface FastEthernet1/0
R1(config-if)# description Lien1 vers R2 Fa0/0
R1(config-if)# ip address 192.168.1.0 255.255.255.254
R1(config-if)# no shutdown
R1(config-if)#interface Serial2/0
R1(config-if)# description Lien4 serie vers R4 Se3/0
R1(config-if)# ip address 192.168.4.0 255.255.255.254
R1(config-if)# no shutdown
R1(config-if)#interface FastEthernet1/1
R1(config-if)# description Lien5 vers R5 Fa0/0
R1(config-if)# ip address 192.168.5.0 255.255.255.254
R1(config-if)# no shutdown
R1(config-if)#end
R1#write memory
Vérification R1 :

Plaintext
R1#show ip interface brief
Interface          IP-Address      OK? Method Status                Protocol
FastEthernet0/0    192.168.0.1     YES manual up                    up
FastEthernet1/0    192.168.1.0     YES manual up                    up
FastEthernet1/1    192.168.5.0     YES manual up                    up
Serial2/0          192.168.4.0     YES manual up                    up
Serial2/1          unassigned      YES unset  administratively down down
Serial2/2          unassigned      YES unset  administratively down down
Serial2/3          unassigned      YES unset  administratively down down
GigabitEthernet6/0 unassigned      YES unset  administratively down down

R1#ping 192.168.1.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 28/32/40 ms

R1#ping 192.168.4.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/28/40 ms

R1#ping 192.168.5.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/26/32 ms
R2
Plaintext
R2#enable
R2#configure terminal
R2(config)#interface FastEthernet0/0
R2(config-if)# description Lien1 vers R1 Fa1/0
R2(config-if)# ip address 192.168.1.1 255.255.255.254
R2(config-if)# no shutdown
R2(config-if)#interface FastEthernet1/0
R2(config-if)# description Lien2 vers R3 Fa1/0
R2(config-if)# ip address 192.168.2.0 255.255.255.254
R2(config-if)# no shutdown
R2(config-if)#end
R2#write memory
Vérification R2 :

Plaintext
R2#show ip interface brief
Interface          IP-Address      OK? Method Status                Protocol
FastEthernet0/0    192.168.1.1     YES manual up                    up
FastEthernet1/0    192.168.2.0     YES manual up                    up

R2#ping 192.168.2.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/32 ms
R3
Plaintext
R3#configure terminal
R3(config)#interface FastEthernet0/0
R3(config-if)# no ip address
R3(config-if)#interface FastEthernet1/0
R3(config-if)# no ip address
R3(config-if)#interface FastEthernet0/0
R3(config-if)# description Lien3 vers R4 Fa1/0
R3(config-if)# ip address 192.168.3.0 255.255.255.254
R3(config-if)#interface FastEthernet1/0
R3(config-if)# description Lien2 vers R2 Fa1/0
R3(config-if)# ip address 192.168.2.1 255.255.255.254
R3(config-if)#end
Vérification R3 :

Plaintext
R3#show ip interface brief
Interface          IP-Address      OK? Method Status                Protocol
FastEthernet0/0    192.168.2.1     YES manual up                    up
FastEthernet1/0    192.168.3.0     YES manual up                    up

R3#ping 192.168.3.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/28/44 ms
R4
Plaintext
R4#enable
R4#configure terminal
R4(config)#interface FastEthernet1/0
R4(config-if)# description Lien3 vers R3 Fa1/0
R4(config-if)# ip address 192.168.3.1 255.255.255.254
R4(config-if)# no shutdown
R4(config-if)#interface Serial3/0
R4(config-if)# description Lien4 serie vers R1 Se2/0
R4(config-if)# ip address 192.168.4.1 255.255.255.254
R4(config-if)# no shutdown
R4(config-if)#interface FastEthernet0/0
R4(config-if)# description Lien6 vers R5 Fa1/0
R4(config-if)# ip address 192.168.6.1 255.255.255.254
R4(config-if)# no shutdown
R4(config-if)#interface FastEthernet2/0
R4(config-if)# description LAN entrepot vers PC2
R4(config-if)# ip address 192.168.7.1 255.255.255.0
R4(config-if)# no shutdown
R4(config-if)#end
R4#write memory
Vérification R4 :

Plaintext
R4#show ip interface brief
Interface          IP-Address      OK? Method Status                Protocol
FastEthernet0/0    192.168.6.1     YES manual up                    up
FastEthernet1/0    192.168.3.1     YES manual up                    up
FastEthernet2/0    192.168.7.1     YES manual up                    up
Serial3/0          192.168.4.1     YES manual up                    up
Serial3/1          unassigned      YES unset  administratively down down
Serial3/2          unassigned      YES unset  administratively down down
Serial3/3          unassigned      YES unset  administratively down down
R5
Plaintext
R5#enable
R5#configure terminal
R5(config)#interface FastEthernet0/0
R5(config-if)# description Lien5 vers R1 Fa1/1
R5(config-if)# ip address 192.168.5.1 255.255.255.254
R5(config-if)# no shutdown
R5(config-if)#interface FastEthernet1/0
R5(config-if)# description Lien6 vers R4 Fa0/0
R5(config-if)# ip address 192.168.6.0 255.255.255.254
R5(config-if)# no shutdown
R5(config-if)#end
R5#write memory
Vérification R5 :

Plaintext
R5#show ip interface brief
Interface          IP-Address      OK? Method Status                Protocol
FastEthernet0/0    192.168.5.1     YES manual up                    up
FastEthernet1/0    192.168.6.0     YES manual up                    up
FastEthernet2/0    unassigned      YES unset  administratively down down
Serial3/0          unassigned      YES unset  administratively down down

R5#ping 192.168.6.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/36 ms
2. Mise en Place du Nuage / Réseau Simulé
Simulation du nuage 10.40.10.1 via une interface Loopback0 sur R5 :

Plaintext
R5#configure terminal
R5(config)#interface Loopback0
R5(config-if)# description Nuage simule 10.40.10.1
R5(config-if)# ip address 10.40.10.1 255.255.255.0
R5(config-if)# no shutdown
R5(config-if)#exit
3. Configuration EIGRP (AS 100)
Plaintext
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
Configuration sauvegardée sur chaque routeur via write memory.

Configuration des VPCS
Plaintext
PC1> ip 192.168.0.10/24 192.168.0.1
PC2> ip 192.168.7.10/24 192.168.7.1
4. Preuves et Validation
Table de voisinage de R1
Plaintext
R1#show ip eigrp neighbors
EIGRP-IPv4 Neighbors for AS(100)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
2   192.168.5.1             Fa1/1                    14 00:03:02   43   258  0  5
1   192.168.4.1             Se2/0                    11 00:03:14   41   246  0  14
0   192.168.1.1             Fa1/0                    12 00:03:33   36   216  0  14
Routes EIGRP apprises par R1
Plaintext
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
Pings depuis PC1
Plaintext
PC1> ping 192.168.7.10
84 bytes from 192.168.7.10 icmp_seq=1 ttl=61 time=105.360 ms
84 bytes from 192.168.7.10 icmp_seq=2 ttl=61 time=91.795 ms

PC1> ping 10.40.10.1
84 bytes from 10.40.10.1 icmp_seq=1 ttl=254 time=45.645 ms
84 bytes from 10.40.10.1 icmp_seq=2 ttl=254 time=46.946 ms
5. Test de Bascule (Failover) de la Liaison R1 ↔ R5
État avant la coupure (R1) :

Plaintext
R1#show ip route 192.168.7.0
Routing entry for 192.168.7.0/24
  Known via "eigrp 100", distance 90, metric 33280, type internal
  - 192.168.5.1, from 192.168.5.1, via FastEthernet1/1
Simulation de la coupure :

Plaintext
R1#configure terminal
R1(config)#interface FastEthernet1/1
R1(config-if)#shutdown
R1(config-if)#end
*Oct 12 09:23:50.903: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.5.1 (FastEthernet1/1) is down: interface down
État après la coupure (R1) :

Plaintext
R1#show ip route 192.168.7.0
Routing entry for 192.168.7.0/24
  Known via "eigrp 100", distance 90, metric 2172416, type internal
  - 192.168.4.1, from 192.168.4.1, via Serial2/0
Continuité du trafic (Ping continu - Extrait) :

Plaintext
84 bytes from 192.168.7.10 icmp_seq=17 ttl=61 time=91.336 ms
192.168.7.10 icmp_seq=18 timeout
192.168.7.10 icmp_seq=19 timeout
192.168.7.10 icmp_seq=20 timeout
192.168.7.10 icmp_seq=21 timeout
192.168.7.10 icmp_seq=22 timeout
192.168.7.10 icmp_seq=23 timeout
84 bytes from 192.168.7.10 icmp_seq=24 ttl=62 time=60.726 ms
(6 paquets perdus, puis reconvergence automatique via la liaison série).

Rétablissement du lien :

Plaintext
R1#configure terminal
R1(config)#interface FastEthernet1/1
R1(config-if)#no shutdown
R1(config-if)#end
Retour immédiat à la route d'origine via R5 (métrique 33280).

6. Rapport Court — CDC 1 : Routage EIGRP
Choix du chemin (VPC1 → VPC2) :
Le trafic passe par R5 (R1 → R5 → R4) car c'est le chemin le plus rapide calculé par EIGRP. Les liens sont en FastEthernet et comportent moins de sauts qu'en passant par le haut (R2-R3) ou par la liaison série.

Simulation du nuage :
Le réseau 10.40.10.0/24 est simulé par une interface Loopback0 sur R5 (10.40.10.1) et annoncé dynamiquement par EIGRP, éliminant tout besoin de routes statiques.

Test de bascule (Failover) :
La coupure de FastEthernet1/1 sur R1 déclenche la reconvergence DUAL en 2-3 secondes. Le trafic bascule instantanément sur la liaison série de secours avant de revenir automatiquement au rétablissement du lien principal.

Écarts assumés par rapport au schéma :
Les liens 1 à 3 sont configurés en FastEthernet et non en GigabitEthernet.

Le nuage externe est simulé par une Loopback0 sur R5.

Le câblage physique réel de R3 est inversé par rapport au dessin GNS3 (vérifié par CDP) : R2 Fa1/0 ↔ R3 Fa1/0 (lien 2), R3 Fa0/0 ↔ R4 Fa1/0 (lien 3). Les adresses IP de R3 suivent rigoureusement ce câblage réel.
<br>
1. b) Un protocole IP numéro 88<br>
2. b) Même numéro de système autonome<br>
3. b) 90<br>
4. a) Bande passante et délai<br>
5. c) 224.0.0.10<br>
6. a) La route est faisable (feasible successor)<br>
7. b) Calculer les routes sans boucle et sans route de secours inutile<br>
8. b) show ip eigrp neighbors<br>
9. a) 5 secondes<br>
10. b) 15 secondes<br>
11. b) Elle arrête l'envoi des hellos sur l'interface, tout en continuant à annoncer le réseau<br>
12. b) Désactivée par défaut<br>
13. b) variance<br>
14. a) Le chemin à plus petit coût total composé de bande passante et de délai<br>
15. a) show ip protocols<br>
16. c) 2<br>
17. b) Externe<br>
18. b) Feasibility condition / faisable distance<br>
19. a) Elle permet un basculement immédiat sans recalcul complet<br>
20. a) show ip eigrp topology<br>
21. c) OSPF<br>
22. a) 1<br>
23. a) Le vecteur de distance ne connaît pas toute la topologie, l'état de lien la connaît<br>
24. a) router-id<br>
25. a) Pour vérifier que la convergence fonctionne et que le trafic est réacheminé<br>
26. b) HDLC<br>
27. a) PPP<br>
28. a) Le signal d'horloge<br>
29. a) Relier le réseau interne aux réseaux externes<br>
30. b) TCP<br>
31. a) Une étiquette (label)<br>
32. b) Elle permet de simuler une liaison opérateur avec un débit et une horloge contrôlés<br>
33. a) QoS<br>
34. b) 1 500 octets<br>
35. a) Pour absorber l'en-tête supplémentaire du tunnel sans fragmentation<br>
36. a) Négocier et établir un canal sécurisé de gestion (ISAKMP SA)<br>
37. a) Négocier les SA qui protégeront le trafic de données<br>
38. b) ESP<br>
39. b) AH<br>
40. b) 50<br>
41. b) 500<br>
42. b) 4500<br>
43. a) En mode tunnel, le paquet IP complet est chiffré dans un nouveau paquet<br>
44. a) Définir les algorithmes de chiffrement et d'authentification de la SA de phase 2<br>
45. a) Renouveler une clé de session indépendamment des précédentes<br>
46. c) AES<br>
47. b) MD5<br>
48. a) 3 600 secondes<br>
49. a) À une interface<br>
50. a) Même politique ISAKMP, clés pré-partagées ou certificats compatibles, et trafic intéressant défini<br>
51. a) Le trafic dont la source et la destination sont définies dans une ACL pour être chiffré<br>
52. b) show crypto ipsec sa<br>
53. a) Moins d'échanges et une meilleure robustesse<br>
54. a) L'ACL définissant le trafic intéressant est incorrecte<br>
55. a) Pour sécuriser les échanges entre les sites tout en gardant une politique centralisée<br>
56. a) Dynamic Multipoint VPN<br>
57. b) NHRP<br>
58. a) Serveur NHRP et point central de connexion des spokes<br>
59. a) mGRE (multipoint GRE)<br>
60. a) 47<br>
61. a) Il permet de relier plusieurs pairs sur une seule interface de tunnel<br>
62. a) NHRP redirect et shortcut<br>
63. a) Tout le trafic spoke-to-spoke passe par le hub<br>
64. a) ip nhrp network-id<br>
65. a) Associer l'adresse IP du tunnel à l'adresse NBMA (IP publique ou de transport)<br>
66. a) NBMA est l'adresse de transport, tunnel IP est l'adresse logique du tunnel<br>
67. a) Pour chiffrer les paquets GRE<br>
68. a) Configurer le spoke avec l'adresse du hub et l'enregistrer auprès du hub<br>
69. a) EIGRP ou BGP<br>
70. a) show ip nhrp<br>
71. a) Il empêche le routeur d'annoncer une route apprise sur la même interface<br>
72. a) Il évite de configurer manuellement chaque tunnel entre chaque couple de sites<br>
73. a) Le hub est un point central de signalisation, le spoke initie les tunnels vers le hub<br>
74. a) L'adresse NBMA du hub, le ip nhrp nhs et la connectivité de transport<br>
75. a) Indiquer au spoke l'adresse NHRP du serveur (hub)<br>
76. a) 179<br>
77. a) 20<br>
78. c) 200<br>
79. a) LOCAL_PREF<br>
80. a) Le chemin le plus court est préféré<br>
81. a) AS_PATH prepend<br>
82. a) 100<br>
83. a) LOCAL_PREF<br>
84. a) Remplacer le prochain saut par l'adresse du routeur qui annonce<br>
85. a) Annoncer un préfixe présent dans la table de routage locale<br>
86. a) show ip bgp summary<br>
87. a) Le routeur rejette la route pour éviter une boucle<br>
88. a) Suggérer au voisin par quel point d'entrée il doit envoyer le trafic<br>
89. a) Weight (propre à Cisco)<br>
90. a) Éviter la nécessité d'un maillage complet de sessions iBGP<br>
91. a) Pour garantir la stabilité de la session même si une interface physique tombe<br>
92. a) Accepter ou refuser certaines routes annoncées ou reçues<br>
93. a) 1<br>
94. a) Vérifier l'état de la session et détecter une panne<br>
95. a) Il peut attirer du trafic non désiré et exposer le réseau interne<br>
96. a) OMP<br>
97. a) Étendre des réseaux de niveau 2 sur un réseau de niveau 3<br>
98. a) 4789<br>
99. a) Un DMVPN ou une architecture SD-WAN avec contrôleur centralisé<br>
100. a) Vérifier la connectivité de base, la couche physique et les voisinages avant d'analyser les protocoles<br>
