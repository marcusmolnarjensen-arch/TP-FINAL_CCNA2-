# TP-FINAL_CCNA2-<br>
tout d'abord j'ai mis en place la topologie<br>
<img width="1197" height="543" alt="image" src="https://github.com/user-attachments/assets/3b7899be-117a-4326-ace2-93281f6ec303" /><br>
## Étape 1 — Inventaire (lecture seule)<br>
- GNS3 2.2.61, 5 routeurs c7200 (NPE-400), IOS 15.2S7<br>
- Configs vierges : hostname seul, aucune IP, aucun routage<br>
- Câblage conforme au schéma sauf : liens 1-3 en FastEthernet, Cloud non branché<br>
<br>
mise en place des routeurs<br>
R1:<br>
R1#enable<br>
R1#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R1(config)#interface FastEthernet0/0<br>
R1(config-if)# description LAN siege vers PC1<br>
R1(config-if)# ip address 192.168.0.1 255.255.255.0<br>
R1(config-if)# no shutdown<br>
R1(config-if)#interface FastEthernet1/0<br>
R1(config-if)# description Lien1 vers R2 Fa0/0<br>
R1(config-if)# ip address 192.168.1.0 255.255.255.254<br>
R1(config-if)# no shutdown<br>
R1(config-if)#interface Serial2/0<br>
R1(config-if)# description Lien4 serie vers R4 Se3/0<br>
R1(config-if)# ip address 192.168.4.0 255.255.255.254<br>
R1(config-if)# no shutdown<br>
R1(config-if)#interface FastEthernet1/1<br>
R1(config-if)# description Lien5 vers R5 Fa0/0<br>
R1(config-if)# ip address 192.168.5.0 255.255.255.254<br>
R1(config-if)# no shutdown<br>
R1(config-if)#end<br>
R1#write memory<br>
Warning: Attempting to overwrite an NVRAM configuration previously written<br>
by a different version of the system image.<br>
Overwrite the previous NVRAM configuration?[confirm]<br>
*Oct 11 20:28:48.079: %SYS-5-CONFIG_I: Configured from console by console<br>
[confirm]<br>
Building configuration...<br>
<br>
*Oct 11 20:28:49.419: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up<br>
*Oct 11 20:28:49.615: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up<br>
*Oct 11 20:28:49.787: %LINK-3-UPDOWN: Interface Serial2/0, changed state to up<br>
*Oct 11 20:28:49.995: %LINK-3-UPDOWN: Interface FastEthernet1/1, changed state to up<br>
*Oct 11 20:28:50.419: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up[OK]<br>
R1#<br>
*Oct 11 20:28:50.615: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up<br>
*Oct 11 20:28:50.795: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial2/0, changed state to up<br>
*Oct 11 20:28:50.995: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/1, changed state  upto<br>
R1#<br>
*Oct 11 20:29:11.623: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial2/0, changed state to down<br>
<br>
verification:<br>
R1#show ip interface brief<br>
Interface             IP-Address      OK? Method Status                Protocol<br>
FastEthernet0/0       192.168.0.1     YES manual up                    up<br>
FastEthernet1/0       192.168.1.0     YES manual up                    up<br>
FastEthernet1/1       192.168.5.0     YES manual up                    up<br>
Serial2/0             192.168.4.0     YES manual up                    up<br>
Serial2/1             unassigned      YES unset  administratively down down<br>
Serial2/2             unassigned      YES unset  administratively down down<br>
Serial2/3             unassigned      YES unset  administratively down down<br>
GigabitEthernet6/0    unassigned      YES unset  administratively down down<br>
<br>
R1#ping 192.168.1.1<br>
Type escape sequence to abort.<br>
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:<br>
!!!!!<br>
Success rate is 100 percent (5/5), round-trip min/avg/max = 28/32/40 ms<br>
R1#<br>
R1#ping 192.168.4.1<br>
Type escape sequence to abort.<br>
Sending 5, 100-byte ICMP Echos to 192.168.4.1, timeout is 2 seconds:<br>
!!!!!<br>
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/28/40 ms<br>
R1#<br>
R1#ping 192.168.5.1<br>
Type escape sequence to abort.<br>
Sending 5, 100-byte ICMP Echos to 192.168.5.1, timeout is 2 seconds:<br>
!!!!!<br>
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/26/32 ms<br>
R1#<br>
<br>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
R2:<br>
R2#enable<br>
R2#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R2(config)#interface FastEthernet0/0<br>
R2(config-if)# description Lien1 vers R1 Fa1/0<br>
R2(config-if)# ip address 192.168.1.1 255.255.255.254<br>
R2(config-if)# no shutdown<br>
R2(config-if)#interface FastEthernet1/0<br>
R2(config-if)# description Lien2 vers R3 Fa1/0<br>
R2(config-if)# ip address 192.168.2.0 255.255.255.254<br>
R2(config-if)# no shutdown<br>
R2(config-if)#end<br>
R2#write memory<br>
*Oct 11 20:31:00.631: %SYS-5-CONFIG_I: Configured from console by console<br>
R2#write memory<br>
Warning: Attempting to overwrite an NVRAM configuration previously written<br>
by a different version of the system image.<br>
Overwrite the previous NVRAM configuration?[confirm]<br>
Building configuration...<br>
[OK]<br>
R2#<br>
*Oct 11 20:31:02.399: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up<br>
*Oct 11 20:31:02.555: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up<br>
*Oct 11 20:31:03.435: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up<br>
*Oct 11 20:31:03.627: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up<br>
<br>
verification:<br>
R2#show ip interface brief<br>
Interface             IP-Address      OK? Method Status                Protocol<br>
FastEthernet0/0       192.168.1.1     YES manual up                    up<br>
FastEthernet1/0       192.168.2.0     YES manual up                    up<br>
<br>
R2#ping 192.168.2.1<br>
Type escape sequence to abort.<br>
Sending 5, 100-byte ICMP Echos to 192.168.2.1, timeout is 2 seconds:<br>
!!!!!<br>
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/32 ms<br>
<br>
R2#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R2(config)#interface FastEthernet1/0<br>
R2(config-if)# description Lien2 vers R3 Fa1/0<br>
R2(config-if)#end<br>
<br>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
R3#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R3(config)#interface FastEthernet0/0<br>
R3(config-if)# no ip address<br>
R3(config-if)#interface FastEthernet1/0<br>
R3(config-if)# no ip address<br>
R3(config-if)#interface FastEthernet0/0<br>
R3(config-if)# description Lien3 vers R4 Fa1/0<br>
R3(config-if)# ip address 192.168.3.0 255.255.255.254<br>
R3(config-if)#interface FastEthernet1/0<br>
R3(config-if)# description Lien2 vers R2 Fa1/0<br>
R3(config-if)# ip address 192.168.2.1 255.255.255.254<br>
R3(config-if)#end<br>
R3#<br>
<br>
verification:<br>
R3#show ip interface brief<br>
Interface             IP-Address      OK? Method Status                Protocol<br>
FastEthernet0/0       192.168.2.1     YES manual up                    up<br>
FastEthernet1/0       192.168.3.0     YES manual up                    up<br>
<br>
R3#ping 192.168.3.1<br>
Type escape sequence to abort.<br>
Sending 5, 100-byte ICMP Echos to 192.168.3.1, timeout is 2 seconds:<br>
!!!!!<br>
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/28/44 ms<br>
R3#<br>
R3#show startup-config | include description|ip address<br>
 description Lien3 vers R4 Fa1/0<br>
 ip address 192.168.3.0 255.255.255.254<br>
 description Lien2 vers R2 Fa1/0<br>
 ip address 192.168.2.1 255.255.255.254<br>
R3#<br>
<br>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
R4:<br>
R4#enable<br>
R4#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R4(config)#interface FastEthernet1/0<br>
R4(config-if)# description Lien3 vers R3 Fa1/0<br>
R4(config-if)# ip address 192.168.3.1 255.255.255.254<br>
R4(config-if)# no shutdown<br>
R4(config-if)#interface Serial3/0<br>
R4(config-if)# description Lien4 serie vers R1 Se2/0<br>
R4(config-if)# ip address 192.168.4.1 255.255.255.254<br>
R4(config-if)# no shutdown<br>
R4(config-if)#interface FastEthernet0/0<br>
R4(config-if)# description Lien6 vers R5 Fa1/0<br>
R4(config-if)# ip address 192.168.6.1 255.255.255.254<br>
R4(config-if)# no shutdown<br>
R4(config-if)#interface FastEthernet2/0<br>
R4(config-if)# description LAN entrepot vers PC2<br>
R4(config-if)# ip address 192.168.7.1 255.255.255.0<br>
R4(config-if)# no shutdown<br>
R4(config-if)#end<br>
R4#write memory<br>
Warning: Attempting to overwrite an NVRAM configuration previously written<br>
by a different version of the system image.<br>
Overwrite the previous NVRAM configuration?[confirm]<br>
*Oct 11 20:32:14.667: %SYS-5-CONFIG_I: Configured from console by console<br>
[confirm]<br>
Building configuration...<br>
[OK]<br>
R4#<br>
*Oct 11 20:32:16.083: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up<br>
*Oct 11 20:32:16.267: %LINK-3-UPDOWN: Interface Serial3/0, changed state to up<br>
*Oct 11 20:32:16.407: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up<br>
*Oct 11 20:32:16.603: %LINK-3-UPDOWN: Interface FastEthernet2/0, changed state to up<br>
*Oct 11 20:32:17.659: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up<br>
*Oct 11 20:32:17.663: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial3/0, changed state to up<br>
*Oct 11 20:32:17.667: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up<br>
*Oct 11 20:32:17.671: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet2/0, changed state to up<br>
<br>
verification:<br>
R4#show ip interface brief<br>
Interface             IP-Address      OK? Method Status                Protocol<br>
FastEthernet0/0       192.168.6.1     YES manual up                    up<br>
FastEthernet1/0       192.168.3.1     YES manual up                    up<br>
FastEthernet2/0       192.168.7.1     YES manual up                    up<br>
Serial3/0             192.168.4.1     YES manual up                    up<br>
Serial3/1             unassigned      YES unset  administratively down down<br>
Serial3/2             unassigned      YES unset  administratively down down<br>
Serial3/3             unassigned      YES unset  administratively down down<br>
<br>
R4#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R4(config)#interface FastEthernet1/0<br>
R4(config-if)# description Lien3 vers R3 Fa0/0<br>
R4(config-if)#end<br>
<br>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
R5:<br>
R5#enable<br>
R5#configure terminal<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R5(config)#interface FastEthernet0/0<br>
R5(config-if)# description Lien5 vers R1 Fa1/1<br>
R5(config-if)# ip address 192.168.5.1 255.255.255.254<br>
R5(config-if)# no shutdown<br>
R5(config-if)#interface FastEthernet1/0<br>
R5(config-if)# description Lien6 vers R4 Fa0/0<br>
R5(config-if)# ip address 192.168.6.0 255.255.255.254<br>
R5(config-if)# no shutdown<br>
R5(config-if)#end<br>
R5#write memory<br>
Warning: Attempting to overwrite an NVRAM configuration previously written<br>
by a different version of the system image.<br>
Overwrite the previous NVRAM configuration?[confirm]<br>
*Oct 11 20:32:53.411: %SYS-5-CONFIG_I: Configured from console by console<br>
[confirm]<br>
Building configuration...<br>
[OK]<br>
R5#<br>
*Oct 11 20:32:55.131: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up<br>
*Oct 11 20:32:55.311: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up<br>
*Oct 11 20:32:56.187: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up<br>
R5#<br>
*Oct 11 20:32:56.311: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up<br>
R5#<br>
<br>
verification:<br>
R5#show ip interface brief<br>
Interface             IP-Address      OK? Method Status                Protocol<br>
FastEthernet0/0       192.168.5.1     YES manual up                    up<br>
FastEthernet1/0       192.168.6.0     YES manual up                    up<br>
FastEthernet2/0       unassigned      YES unset  administratively down down<br>
Serial3/0             unassigned      YES unset  administratively down down<br>
Serial3/1             unassigned      YES unset  administratively down down<br>
Serial3/2             unassigned      YES unset  administratively down down<br>
Serial3/3             unassigned      YES unset  administratively down down<br>
<br>
R5#ping 192.168.6.1<br>
Type escape sequence to abort.<br>
Sending 5, 100-byte ICMP Echos to 192.168.6.1, timeout is 2 seconds:<br>
!!!!!<br>
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/36 ms<br>
<br>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
<br>
MISE EN PLACE DU NUAGE / RÉSEAU SIMULÉ<br>
<br>
Nuage simulé (Loopback sur R5)<br>
Le TP est réalisé à distance du réseau de la classe : le nuage 10.40.10.1 est simulé par une interface Loopback0 sur R5, qui joue le rôle de passerelle.<br>
<br>
R5#configure terminal<br>
R5(config)#interface Loopback0<br>
R5(config-if)# description Nuage simule 10.40.10.1<br>
R5(config-if)# ip address 10.40.10.1 255.255.255.0<br>
R5(config-if)# no shutdown<br>
R5(config-if)#exit<br>
<br>
Configuration EIGRP (AS 100)<br>
network active EIGRP sur les interfaces dont l'adresse est dans la plage (wildcard 0.0.0.1 pour un /31, 0.0.0.255 pour un /24). passive-interface protège les interfaces qui ne doivent former aucun voisinage (LAN et loopback) : leur réseau est quand même annoncé.<br>
<br>
R1:<br>
router eigrp 100<br>
 network 192.168.0.0 0.0.0.255<br>
 network 192.168.1.0 0.0.0.1<br>
 network 192.168.4.0 0.0.0.1<br>
 network 192.168.5.0 0.0.0.1<br>
 no auto-summary<br>
 passive-interface FastEthernet0/0<br>
<br>
R2:<br>
router eigrp 100<br>
 network 192.168.1.0 0.0.0.1<br>
 network 192.168.2.0 0.0.0.1<br>
 no auto-summary<br>
<br>
R3:<br>
router eigrp 100<br>
 network 192.168.2.0 0.0.0.1<br>
 network 192.168.3.0 0.0.0.1<br>
 no auto-summary<br>
<br>
R4:<br>
router eigrp 100<br>
 network 192.168.3.0 0.0.0.1<br>
 network 192.168.4.0 0.0.0.1<br>
 network 192.168.6.0 0.0.0.1<br>
 network 192.168.7.0 0.0.0.255<br>
 no auto-summary<br>
 passive-interface FastEthernet2/0<br>
<br>
R5:<br>
router eigrp 100<br>
 network 192.168.5.0 0.0.0.1<br>
 network 192.168.6.0 0.0.0.1<br>
 network 10.40.10.0 0.0.0.255<br>
 no auto-summary<br>
 passive-interface Loopback0<br>
<br>
Configuration sauvegardée sur chaque routeur (write memory).<br>
PC1 et PC2 (consoles VPCS) :<br>
<br>
PC1> ip 192.168.0.10/24 192.168.0.1<br>
PC2> ip 192.168.7.10/24 192.168.7.1<br>
<br>
Preuves<br>
Voisinages EIGRP : messages d'adjacence (R2 à R5)<br>
<br>
R2: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.1.0 (FastEthernet0/0) is up: new adjacency<br>
R2: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.2.1 (FastEthernet1/0) is up: new adjacency<br>
R3: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.2.0 (FastEthernet1/0) is up: new adjacency<br>
R3: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.3.1 (FastEthernet0/0) is up: new adjacency<br>
R4: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.4.0 (Serial3/0) is up: new adjacency<br>
R4: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.3.0 (FastEthernet1/0) is up: new adjacency<br>
R4: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.6.0 (FastEthernet0/0) is up: new adjacency<br>
R5: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.5.0 (FastEthernet0/0) is up: new adjacency<br>
R5: %DUAL-5-NBRCHANGE: EIGRP-IPv4 100: Neighbor 192.168.6.1 (FastEthernet1/0) is up: new adjacency<br>
<br>
Table de voisinage de R1<br>
<br>
R1#show ip eigrp neighbors<br>
EIGRP-IPv4 Neighbors for AS(100)<br>
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq<br>
                                                   (sec)         (ms)       Cnt Num<br>
2   192.168.5.1             Fa1/1                    14 00:03:02   43   258  0  5<br>
1   192.168.4.1             Se2/0                    11 00:03:14   41   246  0  14<br>
0   192.168.1.1             Fa1/0                    12 00:03:33   36   216  0  14<br>
<br>
Routes EIGRP apprises par R1<br>
<br>
R1#show ip route eigrp<br>
Gateway of last resort is not set<br>
<br>
      10.0.0.0/24 is subnetted, 1 subnets<br>
D        10.40.10.0 [90/156160] via 192.168.5.1, 00:03:14, FastEthernet1/1<br>
      192.168.2.0/31 is subnetted, 1 subnets<br>
D        192.168.2.0 [90/30720] via 192.168.1.1, 00:03:31, FastEthernet1/0<br>
      192.168.3.0/31 is subnetted, 1 subnets<br>
D        192.168.3.0 [90/33280] via 192.168.5.1, 00:03:14, FastEthernet1/1<br>
                     [90/33280] via 192.168.1.1, 00:03:14, FastEthernet1/0<br>
      192.168.6.0/31 is subnetted, 1 subnets<br>
D        192.168.6.0 [90/30720] via 192.168.5.1, 00:03:19, FastEthernet1/1<br>
D     192.168.7.0/24 [90/33280] via 192.168.5.1, 00:03:14, FastEthernet1/1<br>
<br>
Pings depuis PC1<br>
<br>
PC1> ping 192.168.7.10<br>
84 bytes from 192.168.7.10 icmp_seq=1 ttl=61 time=105.360 ms<br>
84 bytes from 192.168.7.10 icmp_seq=2 ttl=61 time=91.795 ms<br>
84 bytes from 192.168.7.10 icmp_seq=3 ttl=61 time=108.698 ms<br>
84 bytes from 192.168.7.10 icmp_seq=4 ttl=61 time=93.575 ms<br>
84 bytes from 192.168.7.10 icmp_seq=5 ttl=61 time=90.687 ms<br>
<br>
PC1> ping 10.40.10.1<br>
84 bytes from 10.40.10.1 icmp_seq=1 ttl=254 time=45.645 ms<br>
84 bytes from 10.40.10.1 icmp_seq=2 ttl=254 time=46.946 ms<br>
84 bytes from 10.40.10.1 icmp_seq=3 ttl=254 time=45.738 ms<br>
84 bytes from 10.40.10.1 icmp_seq=4 ttl=254 time=45.763 ms<br>
84 bytes from 10.40.10.1 icmp_seq=5 ttl=254 time=46.588 ms<br>
(Le VPCS affiche toujours PC1 : dans ses messages, y compris pour PC2.)<br>
<br>
Analyse<br>
- R1 est voisin de R2 (Fa1/0), R4 (Se2/0) et R5 (Fa1/1), les trois routeurs auxquels il est relié.<br>
- Tous les réseaux du plan d'adressage sont présents sur R1 : les réseaux connectés (192.168.0.0/24, 192.168.1.0/31, 192.168.4.0/31, 192.168.5.0/31) et les routes EIGRP 192.168.2.0, 192.168.3.0, 192.168.6.0, 192.168.7.0 et 10.40.10.0.<br>
- Chemin PC1 → PC2 : il passe par R5 (R1 Fa1/1 → R5 → R4), avec la métrique 33280. Le TTL de 61 correspond à 3 routeurs traversés (R1, R5, R4). Tous les liens sont en FastEthernet : le chemin par R2 et R3 coûte plus cher (4 interfaces de délai 100 µs contre 3), et la liaison série (1544 kbit/s) reste la dernière option.<br>
- Métrique EIGRP par défaut : 256 x (10^7 / bande passante minimale en kbit/s + somme des délais en dizaines de µs). Pour 192.168.7.0 via R5 : 256 x (100 + 30) = 33280.<br>
- 10.40.10.0 est appris avec la métrique 156160 : le délai de la loopback est plus élevé que celui d'une FastEthernet.<br>
<br>
Écarts assumés par rapport au schéma<br>
- Les liens 1 à 3 sont en FastEthernet et non en GigabitEthernet : le lab n'a pas de carte Giga.<br>
- Le nuage est simulé par une Loopback0 sur R5, parce que le TP est fait à distance du réseau de la classe.<br>
- Le câblage réel de R3 est inversé par rapport au dessin GNS3 (vérifié avec CDP) : R2 Fa1/0 ↔ R3 Fa1/0 (lien 2), R3 Fa0/0 ↔ R4 Fa1/0 (lien 3). Les adresses de R3 suivent le câblage réel.<br>
<br>
Reste à faire<br>
- Table de routage complète de R1 (show ip route, avec les routes connectées).<br>
- Test de bascule : coupure d'un lien, table avant et après, ping pendant la coupure.<br>
- Export des configurations finales des 5 routeurs (show running-config).<br>
- Rapport d'une page.<br>

