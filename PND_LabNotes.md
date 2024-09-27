# PND Lab Lesson note


## Important tips for kathará 
1. When you create a folder with  pc name, e.g. **pc1**, every time you start kathará, the content inside pc1 folder will be copied inside the root directory of pc1. This is good when for example you don't want to write inside `/etc/network/interfaces` every time at startup;
2. If you don't want to write every time the commands in the terminal, you just need to put them inside the startup file (e.g. pc1.startup);
3. Run `kathará --noterminals` to start the device without opening the 'ugly' terminals. Then use `kathará connect pcN` to open a terminal.


## Main Kathará commands (aliases)
- `lstart/lrestart`: start (restart) a lab;
- `lclean`: stop a running lab;
- `kwipe`: wipe the kathará environment;
- `docker network list`: list virtual networks and virtual interfaces of VMs;
- `ip addr flush ethN`: clean all the IP addresses of an ethernet interface;
- `ifconfig/ip addr`: show all the IP addresses that we have;
- `ip route`: show the routes; 


## Labs Lessons

### LAB 1 - IPv4 

#### Ex. 1.1 - Static configuration
An host has to receive 4 main pieces of information:
1. IP address;
2. The Netmask;
3. The IP address of its default Gateway;
4. The IP address of a DNS.

Our addresses start from .25 to .30 (only 6 addresses), so:

- Launch kathará: `lstart`
- For pc1 (manual configuration using interfaces file): 
    - `nano /etc/network/interfaces.d/eth0` and write these lines:
        ```
        auto eth0
        iface eth0 inet 
        static                
        address 192.168.100.25/29                                 
        gateway 192.168.100.30
        dns-nameserver 8.8.8.8
        ```

- If the last line of previous code block doesn't work write: 
    - `cat > /etc/resolv.conf`
        	- `nameserver 8.8.8.8`
Write in the terminal: `ifup eth0` (interface up, to make the host read startup file).

Pc1 and pc2 are done in startup file:
- For pc2 (using ifconfig):
    - `ifconfig eth0 192.168.100.26 netmask 255.255.255.248 broadcast 192.168.100.31`;
    - `ifconfig eth0 up` non necessario;
    - `route add default gw 192.168.100.30`;
    - `echo "nameserver 8.8.8.8" > etc/resolv.conf`;
- For pc3 (using ip):
    - `ip addr add 192.168.100.27/29 broadcast 192.168.100.31 dev eth0` (in pc1 broadcast domain is added automatically);
    - `ip route add 151.101.0.0/24 via 192.168.100.30` to add a particular route (facultative)
    - `ip route add default via 192.168.100.30`
    - `echo "nameserver 8.8.8.8" > etc/resolv.conf`.

#### Ex. 1.2 -  DHCP configuration
We have to configure DHCP server which is a client server mechanism:
1. The server has a pull (set) of addresses;
2. The client/host makes a request for an IP (*dhcp discover*);
3. The server offers an IP to the client (*dhcp offer*);
4. Once the client accepted it, the IP is granted and reserved for a limited amount of time (*leasing time*).

The procedure is the following:
- Configure **r1** in order to operate as a DHCP server. To configure udhcpd server (u stands for micro, it is very small), in r1 we have to manipulate /`etc/udhcpd.conf` file:
    - Modify the following lines in `/etc/udhcpd.conf`:
        ```
        start           192.168.100.25    #default: 192.168.0.20
        end             192.168.100.29    #default: 192.168.0.254
        ```
    - Setup the DNS, subnet and router:
        ``` 
        opt     dns     1.1.1.1 8.8.8.8
        option  subnet  255.255.255.248
        opt     router  192.168.100.30
        ```
    - Select the *lease time* :
        `option  lease   3600         # 1 hour`
- Now that we have prepared the file, we have to run the server (with `ps aux` you see there is no running server):
    - `touch /var/lib/misc/udhcpd.leases`;
    - `udhcpd -f` (launch in foreground);
- Configure **pc1** using interfaces file (so you can create pc1 folder):
    - `nano /etc/network/interfaces.d/eth0`:
        ```
            auto eth0
            allow-hotplug eth0
            iface eth0 inet dhcp
        ```
    - `ifup eth0`.
- Configure **pc2**, it is just more simple:
    - `dhclient eth0` (you can put the command in startup file).

#### Ex. 1.3 -  Static Route


### LAB 2 - IPv6 I
The exercises are the following:
- 2.1 - Static manual configuration
- 2.2 - Stateless autoconfiguration

#### Ex. 2.1 -  Manually configure IPv6 (Static and Static EUI64)

PC1 and PC4 belong to different broadcast domain (you can't ping using link local) but you have to use GUA (Global Unicast Address).
We have two network, every network host 2 pc. We have to configure PC1 and PC4 with interfaces file, PC2 with IP and PC3 with ifconfig. We are not expected to ping PC1 and PC4 using link local, because by definition links are only in our broadcast domain, so pc1 and pc4 belong to different broadcast domain (we must use GUA).
Procedure:
- Configure the router **R1** with interfaces file:
    - in `/etc/network/interfaces.d/eth0`:
        ```
        auto eth0
        iface eth0 inet66 static
            address 2001:DB8:CAFE:1::1/64 # i'm adding an address
        iface eth0 inet6 static 
            address FE80::1/64 # i'm specifying another IPv6 address which belongs to the link local space
        ``` 
    - In `/etc/network/interfaces.d/eth1`:
        ```
            auto eth1
            iface eth1 inet66 static
                address 2001:DB8:CAFE:2::1/64
            iface eth1 inet6 static 
                address FE80::1/64 
        ``` 
As you can notice, we are assign the same address to two different interfaces (FE80::1/64). In IPv4 you can't have anything like that. In IPv6 this is one of the characteristic of link local addresses: they are the same for all the network.
- On network 1:	
    - Configure PC1 with interfaces file (`nano /etc/network/interfaces.d/eth0`):
        ```
        auto eth0
        iface eth0 inet6 static
            address 2001:DB8:CAFE:1::101/64
            gateway FE80::1 #I can use link local
        ```
    - Configure PC2 with IP:
        - `ip addr add 2001:db8:cafe:1::102/64 dev eth0`;
        - `ip -6 route add default via FE80::1 eth0` 

To ping the default gateway using link local we must specify the interface name: e.g. `ping fe80::1%eth0`.
Since the link local address space is the same for all interfaces, we must specify it.

- On network 2, PC3:
    - `if config eth0 add 2001:db8:cafe:2::103/64`;
    - `route -6 add default gw fe80::1 dev eth0` # we also have to specify which device (dev) we have to use.
    ```
        auto eth0
        iface eth0 inet6 static
            address 2001:DB8:CAFE:2::104/64
            gateway FE80::1 
    ```
From a network we can't ping a link local (only for the links) address of another network. Every time we create a link local address all of them are EUI-64 address (ff:fe in the middle of the address).

#### Ex. 2.2 -  Dynamic configure (SLAAC), without GUI

**Background**
We have to configure our gateway to start sending **router advertisement**: `pkt = (prefix, prefix-length, default gateway)`. 
As soon as the host receive this packet, it automatically generates its own IP address.
In Linux `/proc/` folder is a virtual file system (an interface to access some of the details of the running system in our computer):
- `ll /proc/` (show folder for every process in our system, and also different folder which make possible to access at the very low level of the elements of our system);
- In `ll /proc/sys/net/ipv6/conf` we can play with the options of our configuration (e.g. enp0s30);
- In particular we are going to use the address generation mode.

To change the options we could use `sudo sysctl -w net.ipv6.conf.eth0.accept_ra=1` (configure our gateway to start sending router advertisement). It is a simple change of a variable (writing on a file). To modify these options in docker we need privilege docker (`sudo kathara lstart --privileged`).
There is another option using a script lstart.sh:
- `./start.sh`; 
- if you run it, it will execute all kathará machine and also open the terminal;  
- In this way we can play with all the details/system control directly as if we were in standard mechanism. 

PC1:
- already configured
    
PC2:
- random interface ID


### LAB 3 - IPv6 II
DHCPv6 with prefix delegation and ICMPv6 MTU discovery (We will play with the framgent header). 

#### Ex. 4 -  DHCPv6 with prefix delegation 
(Do at home)

#### Ex. 5 -  ICMPv6 MTU discovery 
We have to configure the addresses of the whole network and to play with the MTU. 

The main idea is the following:
1. We configure R1 and R3 to became the default routers of LAN1 and LAN2;
2. We statically configure R2 to be the connection router between R1 and R3;
3. We will play with the MTU size of the two links in order to see how this can affect the packets moving from one network to the other.

**5.1**
- R1 has two network, eth0 and eth1, on R1: 
    - `ip addr add 2001:db8:cafe:1::1/64 dev eth0`;
    - `ip addr add 2001:db8:cafe:2::1/64 dev eth1`.
- Configure the router advertisement on R1:
    - `find . -iname "*radv*`;
    - `cp ./usr/share/doc/radvd/examples/radvd.conf.example /etc/radvd.conf`
    - `nano /etc/radvd.conf`;
    - I want to configure the router advertisement on the interface eth0:
        - `interface eth0`;
            ```
            # example of a standard prefix
            #	
            prefix 2001:db8;cafe:1::/64
            {
                AdvOnLink on;
                AdvAutonomous on;
                AdvRouterAddr on;
            };
            ```
        - Remove everything else for our purpose;
        - Remove all the comments:
            - `cat etc/radvd.conf | grep -v	-e "^#"`;
    - `radvd -C /etc/radvd.conf`;	
    - To check if there is a router advertisement running:
        - `ps axuw`;
        - `Look for radvd`.

Now, if everything is done correctly, in pc1 we should have an IP for the SLAAC (stateless autoconfigure address). Also a default route for IPv6 (check with ip -6 route). So r1 became the default router for this network, we self-generate IP addresses and we are ready to communicate. 

- On R3 do the same: 
    - `nano /etc/radvd.conf`;
    - copy and paste the content of R1; 
    
- Static part, let R3 knows the network:
    - `ip addr add 2001:db8:cafe:4::1/64 dev eth0`;
    - `ip addr add 2001:db8:cafe:3::2/64 dev eth1`.

- We have to announce the other hops. Behind r2 we can reach the other network:
    - `ip route add 2001:db8:cafe:2::/64 via 2001:db8:cafe:3::1`;
    - `ip route add 2001:db8:cafe:1::/64 via 2001:db8:cafe:3::1`.

- The same should be done for R1:
    - `ip route add 2001:db8:cafe:3::/64 via 2001:db8:cafe:2::2`;			
    - `ip route add 2001:db8:cafe:4::/64 via 2001:db8:cafe:2::2`.

Then we have to configure the intermediate router (R2). 

**5.2**
- On R2:
    - `ip addr add 2001:db8:cafe:2::2/64 dev eth0`;
    - `ip addr add 2001:db8:cafe:3::1/64 dev eth1`;

- We have to announce the network on the right and on the left:
    - `ip route add 2001:db8:cafe:1::/64 via 2001:db8:cafe:2::1`;
    - `ip route add 2001:db8:cafe:4::/64 via 2001:db8:cafe:3::2`;

Now we should have the full connectivity.

**5.3**
The generic size of an ICMP packet (ping request) is 64 bytes. Let's try to capture these packets:
- `tcpdump -w shared/cap.pcap &` (we are capturing the packets)
- let's do a ping again
- stop the capture.
    
If we now go in Wireshark (a sniffer, network analyzer), opening the file we just created, we can explore the packets captured in the network. The ICMP packet has a size of 56 bytes (payload) + the header = 64 bytes. We can change this!
We can say that the ping we want to send has a size of 1000 bytes:

- `ping 2001:db8:cafe:4:f0ca:6cff:fe2d:5fde -s 1000`;
- capture again with Wireshark to see it.

If we made it even bigger (`-s 2000`), the packet has been fragmented (usually the MTU is 1500). So 2000 bytes of packet is fragmented (in Wireshark look for Next Header: Fragment header). Remember that the fragmentation is realized at the source. We want to change the MTUs.

- On R1:
    - `ip link set eth1 mtu 1480`;

- On R2:
    - `ip link set eth0 mtu 1480`;
    - `ip link set eth1 mtu 1450`.
    
- On R3:
    - `ip link set eth0 mtu 1450`

    
### LAB 4 - Network traffic monitoring and Wireshark 

During the exam probably there will be a couple of question like *write a tcp dump filter or evaluating the outcome of tcp dump filter*.

**Network Architecture Recap**
