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
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
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
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
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
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
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
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
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
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
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
84 bytes from 10.40.10.1 icmp_seq=1 ttl=251 time=137.660 ms<br>
84 bytes from 10.40.10.1 icmp_seq=2 ttl=251 time=139.687 ms<br>
84 bytes from 10.40.10.1 icmp_seq=3 ttl=251 time=137.761 ms<br>
84 bytes from 10.40.10.1 icmp_seq=4 ttl=251 time=135.553 ms<br>
84 bytes from 10.40.10.1 icmp_seq=5 ttl=251 time=137.235 ms<br>
(Le VPCS affiche toujours PC1 : dans ses messages, y compris pour PC2.)<br>
<br>
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
<br>
TEST DE BASCULE (FAILOVER) DE LA LIAISON R1 ↔ R5<br>
<br>
1. État avant la coupure (vérification de la route active sur R1) :<br>
R1# show ip route 192.168.7.0<br>
Routing entry for 192.168.7.0/24<br>
  Known via "eigrp 100", distance 90, metric 33280, type internal<br>
  * 192.168.5.1, from 192.168.5.1, via FastEthernet1/1<br>
<br>
2. Simulation de la coupure de l'interface principale sur R1 :<br>
R1# configure terminal<br>
R1(config)# interface FastEthernet1/1<br>
R1(config-if)# shutdown<br>
R1(config-if)# end<br>
<br>
3. Vérification de la table de routage après reconvergence DUAL (R1) :<br>
R1# show ip route 192.168.7.0<br>
Routing entry for 192.168.7.0/24<br>
  Known via "eigrp 100", distance 90, metric 35840, type internal<br>
  * 192.168.1.1, from 192.168.1.1, via FastEthernet1/0<br>
<br>
4. Vérification de la table de voisinage EIGRP après coupure :<br>
R1# show ip eigrp neighbors<br>
EIGRP-IPv4 Neighbors for AS(100)<br>
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq<br>
1   192.168.4.1             Se2/0                    10 00:11:50   36   216  0  27<br>
0   192.168.1.1             Fa1/0                    14 00:12:09   39   234  0  22<br>
<br>
5. Test de continuité pendant le failover (depuis PC1) :<br>
PC1> ping 192.168.7.10 -t<br>
192.168.7.10 icmp_seq=1 timeout<br>
192.168.7.10 icmp_seq=2 timeout<br>
84 bytes from 192.168.7.10 icmp_seq=3 ttl=61 time=93.687 ms<br>
84 bytes from 192.168.7.10 icmp_seq=4 ttl=61 time=93.796 ms<br>
... (le trafic est réacheminé automatiquement via R2 après la convergence DUAL)<br>
<br>
6. Rétablissement de la liaison principale :<br>
R1# configure terminal<br>
R1(config)# interface FastEthernet1/1<br>
R1(config-if)# no shutdown<br>
R1(config-if)# end<br>
<br>
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
<br>
RAPPORT COURT — CDC 1 : ROUTAGE EIGRP<br>
<br>
1. Pourquoi ce chemin (VPC1 → VPC2) ?<br>
Le trafic entre le siège (VPC1) et l'entrepôt (VPC2) passe par R5 (R1 → R5 → R4) tout simplement parce que c'est le chemin le plus rapide calculé par EIGRP. Les liens sont en FastEthernet et il y a moins de sauts qu'en passant par le haut (R2-R3) ou par la liaison série.<br>
<br>
2. Simulation du nuage<br>
Comme on est à distance et qu'on n'a pas le vrai réseau de l'école branché physiquement, on a simulé le nuage `10.40.10.0/24` en créant une interface Loopback0 sur R5 (`10.40.10.1`). EIGRP s'occupe de l'annoncer aux autres routeurs, du coup tout le monde y accède sans qu'on ait besoin de mettre de routes statiques.<br>
<br>
3. Test de bascule (Failover)<br>
Pour prouver que le réseau est résilient, on a coupé l'interface principale vers R5 (`shutdown` sur R1). EIGRP a vu que le lien était mort et a basculé automatiquement le trafic sur l'autre route en 2-3 secondes. Dès qu'on a remis le lien (`no shutdown`), c'est revenu tout seul sans casser le réseau.<br>
<br>
Écarts assumés par rapport au schéma<br>
- Les liens 1 à 3 sont en FastEthernet et non en GigabitEthernet<br>
- Le nuage est simulé par une Loopback0 sur R5, parce que le TP est fait à distance du réseau de la classe.<br>
- Le câblage réel de R3 est inversé par rapport au dessin GNS3 (vérifié avec CDP) R2 Fa1/0 ↔ R3 Fa1/0 (lien 2), R3 Fa0/0 ↔ R4 Fa1/0 (lien 3). Les adresses de R3 suivent le câblage réel.<br>
-----------------------------------------------------------------------------------------------------------------------------------------------------------------<br>
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
