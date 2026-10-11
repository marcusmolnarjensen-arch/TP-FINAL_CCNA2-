# TP-FINAL_CCNA2-
tout d'abord j'ai mis en place la topologie
<img width="1197" height="543" alt="image" src="https://github.com/user-attachments/assets/3b7899be-117a-4326-ace2-93281f6ec303" />
## Étape 1 — Inventaire (lecture seule)
- GNS3 2.2.61, 5 routeurs c7200 (NPE-400), IOS 15.2S7
- Configs vierges : hostname seul, aucune IP, aucun routage
- Câblage conforme au schéma sauf : liens 1-3 en FastEthernet, Cloud non branché


mise en place des routeurs
R1:
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
Interface              IP-Address      OK? Method Status                Protocol<br>
FastEthernet0/0        192.168.0.1     YES manual up                    up<br>
FastEthernet1/0        192.168.1.0     YES manual up                    up<br>
FastEthernet1/1        192.168.5.0     YES manual up                    up<br>
Serial2/0              192.168.4.0     YES manual up                    up<br>
Serial2/1              unassigned      YES unset  administratively down down<br>
Serial2/2              unassigned      YES unset  administratively down down<br>
Serial2/3              unassigned      YES unset  administratively down down<br>
GigabitEthernet6/0     unassigned      YES unset  administratively down down<br>
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

------------------------------------------------------------------------------------------------------------------------------------------------------------------------
R2:
R2#enable
R2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interface FastEthernet0/0
R2(config-if)# description Lien1 vers R1 Fa1/0
R2(config-if)# ip address 192.168.1.1 255.255.255.254
R2(config-if)# no shutdown
R2(config-if)#interface FastEthernet1/0
R2(config-if)# description Lien2 vers R3 Fa0/0
R2(config-if)# ip address 192.168.2.0 255.255.255.254
R2(config-if)# no shutdown
R2(config-if)#end
R2#write memory
*Oct 11 20:31:00.631: %SYS-5-CONFIG_I: Configured from console by console
R2#write memory
Warning: Attempting to overwrite an NVRAM configuration previously written
by a different version of the system image.
Overwrite the previous NVRAM configuration?[confirm]
Building configuration...
[OK]
R2#
*Oct 11 20:31:02.399: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up
*Oct 11 20:31:02.555: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up
*Oct 11 20:31:03.435: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up
*Oct 11 20:31:03.627: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up

verification:
R2#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/0        192.168.1.1     YES manual up                    up
FastEthernet1/0        192.168.2.0     YES manual up                    up

R2#ping 192.168.2.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.2.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/32 ms

------------------------------------------------------------------------------------------------------------------------------------------------------------------------
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
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
R3#


verification:
R3#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/0        192.168.2.1     YES manual up                    up
FastEthernet1/0        192.168.3.0     YES manual up                    up

R3#ping 192.168.3.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/28/44 ms
R3#

------------------------------------------------------------------------------------------------------------------------------------------------------------------------
R4:
R4#enable
R4#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
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
Warning: Attempting to overwrite an NVRAM configuration previously written
by a different version of the system image.
Overwrite the previous NVRAM configuration?[confirm]
*Oct 11 20:32:14.667: %SYS-5-CONFIG_I: Configured from console by console
[confirm]
Building configuration...
[OK]
R4#
*Oct 11 20:32:16.083: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up
*Oct 11 20:32:16.267: %LINK-3-UPDOWN: Interface Serial3/0, changed state to up
*Oct 11 20:32:16.407: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up
*Oct 11 20:32:16.603: %LINK-3-UPDOWN: Interface FastEthernet2/0, changed state to up
*Oct 11 20:32:17.659: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up
*Oct 11 20:32:17.663: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial3/0, changed state to up
*Oct 11 20:32:17.667: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up
*Oct 11 20:32:17.671: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet2/0, changed state to up

verification:
R4#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/0        192.168.6.1     YES manual up                    up
FastEthernet1/0        192.168.3.1     YES manual up                    up
FastEthernet2/0        192.168.7.1     YES manual up                    up
Serial3/0              192.168.4.1     YES manual up                    up
Serial3/1              unassigned      YES unset  administratively down down
Serial3/2              unassigned      YES unset  administratively down down
Serial3/3              unassigned      YES unset  administratively down down

------------------------------------------------------------------------------------------------------------------------------------------------------------------------
R5:
R5#enable
R5#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
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
Warning: Attempting to overwrite an NVRAM configuration previously written
by a different version of the system image.
Overwrite the previous NVRAM configuration?[confirm]
*Oct 11 20:32:53.411: %SYS-5-CONFIG_I: Configured from console by console
[confirm]
Building configuration...
[OK]
R5#
*Oct 11 20:32:55.131: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up
*Oct 11 20:32:55.311: %LINK-3-UPDOWN: Interface FastEthernet1/0, changed state to up
*Oct 11 20:32:56.187: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up
R5#
*Oct 11 20:32:56.311: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up
R5#
!!!NUAGE pas encore configuré a faire!!!

verification:
R5#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/0        192.168.5.1     YES manual up                    up
FastEthernet1/0        192.168.6.0     YES manual up                    up
FastEthernet2/0        unassigned      YES unset  administratively down down
Serial3/0              unassigned      YES unset  administratively down down
Serial3/1              unassigned      YES unset  administratively down down
Serial3/2              unassigned      YES unset  administratively down down
Serial3/3              unassigned      YES unset  administratively down down

R5#ping 192.168.6.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.6.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/36 ms
!!!NUAGE pas encore configuré a faire!!!

------------------------------------------------------------------------------------------------------------------------------------------------------------------------



