
# PND Lab Lesson note
----
## Important tips for kathará 
1. When you create a folder with  pc name, e.g. **pc1**, every time you start kathará, the content inside pc1 folder will be copied inside the root directory of pc1. This is good when for example you don't want to write inside `/etc/network/interfaces` every time at startup;
2. If you don't want to write every time the commands in the terminal, you just need to put them inside the startup file (e.g. pc1.startup);
3. Run `kathará --noterminals` to start the device without opening the 'ugly' terminals. Then use `kathará connect pcN` to open a terminal.
---

## Main Kathará commands (aliases)
- `lstart/lrestart`: start (restart) a lab;
- `lclean`: stop a running lab;
- `kwipe`: wipe the kathará environment;
- `docker network list`: list virtual networks and virtual interfaces of VMs;
- `ip addr flush ethN`: clean all the IP addresses of an ethernet interface;
- `ifconfig/ip addr`: show all the IP addresses that we have;
- `ip route`: show the routes; 
----
## Lab Lessons

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
----
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

##### Background
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

----

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

##### 5.1
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

##### 5.2
- On R2:
    - `ip addr add 2001:db8:cafe:2::2/64 dev eth0`;
    - `ip addr add 2001:db8:cafe:3::1/64 dev eth1`;

- We have to announce the network on the right and on the left:
    - `ip route add 2001:db8:cafe:1::/64 via 2001:db8:cafe:2::1`;
    - `ip route add 2001:db8:cafe:4::/64 via 2001:db8:cafe:3::2`;

Now we should have the full connectivity.

##### 5.3
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
----

### LAB 4 - Network traffic monitoring and Wireshark 

During the exam probably there will be a couple of question like *write a tcp dump filter or evaluating the outcome of tcp dump filter*.

#### Network Architecture Recap

| Layer      | Description | Layer ideal representation      | Description |
| -----------| ------------| --------------------------------|-------------|
| **Transport** | Stream of data/ datagrams (udp). | Has the illusion of an end-to-end connection between the processes. | Port number (source and destination port). |
| **Network** |Is considered at every step, it is responsible for routing details. | Knows that there are network, have to traverse several links. | IP address. |
| **Datalink** | Is responsible for the single channel that has to be traverse, data can be represented in different ways (optical, electrical and so on). | Has a limited view of a single link. | MAC address. |

#### Port numbers
Well known ports [0...1023] used by servers for standard internet applications:
- 25 SMTP
- 80 HTTP
- 143 IMAP

#### TCP vs UDP connection vs connection-less  
| Protocol | Description | Services relying on TCP/UDP |
|----------|-------------|-----------------------------|
| **TCP**  | Reliable data exchange and control flow (tcp will slow down if the link is not reliable). TCP has SYN and ACK flag. | **FTP** - 20,21; SSH - 22; **Telnet** - 23; **SMTP** - 25; **HTTP** - 80; **IMAP** - 143; **SSL** - 443. |
| **UDP**  | No control on data exchange. | **DNS** - 53; **DHCP** - 67,68; **TFTP** - 69; **SNMP** - 161; **RIP** - 520 |

#### DNS
Is a mechanism responsible for making possible a way to resolve domain names with IP addresses. It is considered a big huge distributed tables that make association between a domain name and an IP address. This table is not handled by a single host but distributed over many different services. We arrange this huge table of association distributing the table, e.g., all the IP addresses corresponding to a domain name ends with *.net* will be handled by someone, the ones that will end with *.com* will be handled by another one...it is a recursive process. The resolver is a process which supposes to take a name and return an IP address.

#### Capture packets
If you want to see the real packet you can use some tools called **network traffic dumpers** like:
- Dumpcap;
- Wireshark/tshark;
- Tcpdump (only captures packets, without GUI). 
They are all based on pcap library.

##### Wireshark: *dissecting packets*
Is a software that allows you to inspect packets and tries to highlight the encapsulation mechanism. It can dissects a *frame/segment/packet* (you can refer to the single elements in order to recognize the single fields of every specific protocol). It requires promiscuous mode (don't ignore any packet, just send them to me and i will process in my way).

###### The logic
Frames collected from the interface are passed between several dissectors, that are responsible for understanding the different layers. Frames pass from bottom layer to upper layer. Protocols can be detected in two ways:
- **Directly**: if a frame has the field that states which protocol it is encapsulating;
- **Indirectly**: with tables of protocol/port combinations;
    
An alternative way to capture the traffic info is when traffic is represented as connections/flow, a collection of packets that belongs to the same transmission (Netflow9, Zeek...).

###### How to use Wireshark
Pre-requisite:
- `sudo adduser user wireshark`;
- reboot pc;

If on the terminal you type `host www.debian.org`, you are creating a dns request, that can be seen in Wireshark if you put dns in the display filter. You are making two different request for the same domain, A and AAAA. The former is ipv4 the latter is ipv6. If you click on a packet, on the top part of the window, you can see the packet dissected in the bottom part:
- **frame**: the whole frame, which include the payload;
- **ethernet**: only the first packet of the packet.
You can also explore the single header and see every single element of the original one. In the case of the DNS, if you open the query you have the structure of a DNS request.

##### Activity 1
On the terminal:
- `cd pnd-labs/`; 
- `git pull`;
- `cd lab1/ex4/`;
- `lstart`.

This lab run a web server on the pc1, on port 80. We have two options to capture the packets (?):

1. Go in Wireshark and do a new capture on a new port we have created;
    - Using docker network ls command you can see the virtual network started by kathará. These network are connected to a virtual card, which can be found in Wireshark capture interfaces.
2. *ip link*: you can see virtual interfaces.

We can connect to the host with the internal lan:
- In `pnd-labs/`  run `./connect-lab.sh 10.0.0.2/24 lan A`;
- We created a new interface connected inside the lab in the same network of pc1;
- Since the host is hosting a web server, if you open the browser and go to 10.0.0.1 you connect to the internal host which have a web server;
- Now you can capture the traffic also with Wireshark (veth0 and veth1) you are able to capture the exchange of packets;
- Right click on a `request made to the web server -> right click -> follow http stream`, to see the requests and responses exchanged with the web server.

##### TCPDUMP

Instead of doing the capture in Realtime with Wireshark, you can do the capture with tcp dump:
- Inside an host: `tcpdump` without any option start capturing and every packet will be just display in the terminal:
    - option `-v` to increase the verbosity;
    - option `-t` you remove the timestamps;
    - option `tcp` only capture tcp (tcp filter);
    - option `-n` do not substitute names;
    - option `-w shared/captur.cap` capture the packet and don't write on the display but in a file named `captur.cap`;

Tcpdump is like an offline analysis of the packets. The filter used in Wireshark in the capture filter are the same used in tcpdump as options.

### LAB 5 - Network traffic regulation with iptables

We are in `lab4/ex1`. We have to connect with our host in the internal network passing 
through the firewall:
- `./connect-lab.sh 192.168.10.2/24 external`;
- Now we can ping the firewall, but we are not aware of the internal network, so we need also to add a route:
    - `ip route add 192.168.100.0/24 via 192.168.10.1`;
- Now we can ping 192.168.100.80, but since it is a server we can connect to its port:
    - 192.168.100.80 (internal host in the internal network, passing through the firewall).

#### Demo 1

Now we try to configure the network so that we can block any ping to our pc1. We want to ping the internal host (`ping 192.168.100.80`) so let's start capturing the packet in r1:
- `tcpdump -t` (timestamp are removed);
- We'll see a request and a reply; 
- We want pc1 to stop answering these packets, we have 2 options:
    1. Configure pc1 to just ignore these requests (**host firewall**);
    2. Configure r1 to block these request (**network firewall**).

- We use the option 1, host firewall. In pc1:
    - `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` (packet that are incoming, input, for my host that have an icmp protocol should be dropped);
    - Now the capture in r1 will see only requests, not responses (pc1 ignore request);
    - With `iptables -L` we list all the routes that we are using so far;
    - `iptables -F`, where `-F` stand for *FLUSH*, will drop all the rules;

#### Demo 2
We want to exclude any service but allow HTTP. In s1:
- `iptables -A INPUT -p tcp --destination-port 80 -j ACCEPT` (only packets allowed to pass);
- `iptables -A INPUT -j REJECT` (very strong reject, everything it will be blocked);
- With `iptables -D INPUT -j REJECT` we can delete the rule. 
- Try also `iptables -A INPUT -j DROP`. The difference between reject and drop is that with drop we do not receive any answer. 

#### IPtables fundamentals
It is a packet filtering firewall for Linux. For more details: www.frozentux.net/iptables-tutorial/iptables-tutorial.html. 
- The rules are grouped in tables. For today we focus on FILTER table;
- Each table has different CHAINS (a sequence) of rules;
- Each packet will go through all the rules of a given table;
- The fate of a packet depends of the first matching rule.
- To see chains of rules we use `iptables -L`.

##### FILTER table
Every packet that reaches the network interface will go under a routing decision. It is taken according to the destination address. If it is one of our addresses, the packet will be process by the rules in the **INPUT** chain. If the IP address is for a different host we go through the **FORWARD** chain. Any packet originated by our host will go through **OUTPUT** chain. In any of these chains, if a packet will reach the end of the chain, we take as the decision the policy associated to the chain. 
If we want to chain the policy we have to use `iptables -P INPUT DROP`.
You can save the rules in a script, to run them when you want.

##### Command switches
- `-t table`: specify the table (filter is default);
- `-j target`: jump to the chain;
- `-A chain`: append a rule at the end of rule chain;
- `-F`: flush a chain;
- `-P policy`: change the default policy;
- `-p protocol`: match the protocol type;
- `-i interface-name`: match input interface (useful if you have a firewall connected to two network).

###### -j target

We can specify different targets:
- `ACCEPT`, the packet is accepted;
- `DROP` the packet is blocked;
- `REJECT` the packet is blocked, but it also sends an error message to the source host of the blocked packet.
    
This type of packet filtering (iptables) is STATELESS, unless you want to became a STATEFUL firewall, using connection tracking mechanism:
- `-m state --state s`: enable *connection tracking* (a specific state):
- NEW
- ESTABLISHED
- RELATED
- INVALID

#### Lab activity
We are in `pnd-labs/lab4/ex1`. Now the filtering should be happen on r1, so iptables should be configured in r1 (network firewall):
- `cd lab4/ex1`;
- `lstart`;
- on r1:
    - `iptables -A FORWARD -d 192.168.100.80 -p tcp --dport 80 -j ACCEPT`;
    - `iptables -A FORWARD -j REJECT`;
    - `iptables -I FORWARD -s 192.168.100.80 -p tcp --dport 80 -j ACCEPT` (now you get also the response).

But in this way you are not exploiting the statefulness of our server, because the server can connect to anyone outside using port 80, so:
- `iptables -D FORWARD 1` (delete the rule);
- And use the statefulnsess of our iptables:
    - `iptables -I 2 FORWARD 2 -p tcp -s 192.168.100.80 --sport 80 -m state --state ESTABILISHED - j ACCEPT`.

Let's trying the same with ipv6:
- `./connect-lab.sh 192.168.10.2/24 external`;
- `sudo ip route add 192.168.100.0/24 via 192.168.10.1`;
- `sudo ip addr add 2001:db8:cafe:2::/64 dev veth0`;
- `sudo ip route add 2001:db8:cafe:1::/64 via fe80::1 dev veth0`.

We use ip6tables: 
- `ip6tables -A FORWARD -d 2001:db8:cafe:1::80 -p tcp --dport 80 -j ACCEPT`;
- `ip6tables -A FORWARD -d 2001:db8:cafe:1::80 -p tcp --sport 80 -m state --state ESTABILISHED -j ACCEPT`;
- `ip6tables -A FORWARD -j REJECT`.

Let's try to replicate the protection from the inside (from a local network). In s1:
- `ip6tables -A INPUT -s 2001:db8:cafe:1::1 -p tcp -dport 22 -j ACCEPT`;
- `ip6tables -A INPUT -J REJECT`;
- `ip6tables -I INPUT 1 -p tcp --dport 80 -j ACCEPT`;
- `ip6tables -INPUT 2 -p tcp --sport 80 -j ACCEPT`.

Why cannot I do a `wget`? We are in the same network...
With reject rule we are rejecting anything, but ip6 in order to work need neighbour solicitation. So you need to allow icmp6:
- `ip6tables -I INPUT 1 -p ipv6-icmp -j ACCEPT`.

### LAB 7 - Attacks related to link-local

#### Quick introduction about network networking

##### Network sniffing
It is a way to intercept the traffic that is flowing in a network. We have to use protection against that (encryption).
To do network sniffing you need a network sniffer like Ettercap, bettercap...This is a passive attack (intercept the packet but not changing anything). It requires the packet to be in the same broadcast domain: 
- The ideal is a Non-swtiched Lan, a pure broadcast domain; 
- When we are using a lan with physical switch thing became more complicated. The main idea is that you prevent to be the next hop in every destination of your target (controlla appunti prof)
- In new wireless lan is impossible because every single host will use a different symmetric key with the access point. When using previous protection mechanism like wep, it use a single key, so is possible to attack it.

#### Switch segmentation mechanism
- flashback
- bridges
- switches: multiport bridges - you make a packet to crsoo just one pot if the destination is on that port

Let's look for the setup
- `cd lab3/ex2`;
- `lstart`
- Go to the host and connect to the lab: `./connect-lab.sh 192.168.100.200/64 lanA`;
- Now we should be able to ping all the machine inside the network:
    - `sudo install net-tools`;
    - `arp or ip neigh to see same values`;
    - `sudo iptable flush dev` to empty...?;
- Now let's play with bettercap:
    - unzip bettercap: `unzip bettercap...`;
    - launch bettercap: `./bettercap`.

Bettercap leverage arp spoofing mechanism pretending to be host in a network

net.probe on is a echanism used by bettercap to know the topology of the network
net.probe off
now we have the list of the host in our network. 
set gateway address





