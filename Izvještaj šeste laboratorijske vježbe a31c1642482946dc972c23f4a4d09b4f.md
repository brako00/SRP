# Izvještaj šeste laboratorijske vježbe

### Zadatak

U okviru vježe student će se upoznati s osnovnim postupkom upravljanja korisničkim računima na Linux OS-u. Pri tome će se poseban naglasak staviti na **kontrolu pristupa (eng. *access control*)** datotekama, programima i drugim resursima Linux sustava.

### Alati

**A. Kreiranje novog korisničkog računa**

**Provjera identifikatora i pripadajućih grupa (a507)**

```bash
id
```

```bash
uid=1000(a507) gid=1000(a507) groups=1000(a507), 4(adm), 20(dialout), 24(cdrom), 25(floppy), 27(sudo), 29(audio), 30(dip), 44(video), 46(plugdev), 114(netdev), 1001(docker)
```

**Dodavanje alice kao korisnika**

```bash
sudo adduser alice
```

**Promjena korisnika iz a507 u alice**

```bash
a507@DESKTOP-LH5GUL2:/mnt/c/Users/A507$ su - alice
Password:
alice@DESKTOP-LH5GUL2:~$ 
```

**Provjera identifikatora i pripadajućih grupa (alice)**

```bash
id
```

```bash
uid=1001(alice) gid=1002(alice) groups=1002(alice)
```

**Dodavanje korisnika boba i promjena korisnika**

```bash
sudo adduser bob
su - bob
```

**Provjera identifikatora i pripadajućih grupa (bob)**

```bash
id
```

```bash
uid=1002(bob) gid=1003(bob) groups=1003(bob)
```

**B. Standardna prava pristupa datotekama**

Korisnik alice

**Ispis puta do direktorija u kojem se nalazimo trenutno**

```bash
pwd
```

```bash
/home/alice
```

**Kreiranje direktorija srp i promjena direktorija u srp**

```bash
mkdir srp
cd srp
```

**Kreiranje datoteke security.txt i pisanje u nju**

```bash
echo "Hello world" > security.txt
```

**Izlist dopuštenja za security.txt**

```bash
ls -l
```

```bash
total 4
-rw-rw-r-- 1 alice alice 12 Jan 10 13:17 security.txt
```

User ima read i write dopuštenja, grupe read i write, a ostali samo read.

**Izlist dopuštenja za srp**

```bash
getfacl srp
```

```bash
# file: srp
# owner: alice
# group: alice
user::rwx
group::rwx
other::r-x
```

**Ispis datoteke security.txt**

```bash
cat security.txt
```

```bash
Hello world
```

**Oduzimanje prava alice na čitanje security.txt**

```bash
chmod u-r security.txt
```

```bash
alice@DESKTOP-LH5GUL2:~$ getfacl security.txt
# file: security.txt
# owner: alice
# group: alice
user::-w-
group::rw-
other::r--
```

```bash
alice@DESKTOP-LH5GUL2:~$ cat security.txt
cat: security.txt: Permission denied
```

**Oduzimanje execute prava nad direktorijem srp**

```bash
chmod u-x srp
```

```bash
alice@DESKTOP-LH5GUL2:~$ ls -l
total 4
drw-rwxr-x 2 alice alice 4096 Jan 10 13:17 srp
```

```bash
alice@DESKTOP-LH5GUL2:~$ cat srp/security.txt
cat: srp/security.txt: Permission denied
```

**Oduzimanje read prava drugima nad security.txt**

```bash
alice@DESKTOP-LH5GUL2:~$ chmod o-r security.txt
```

```bash
alice@DESKTOP-LH5GUL2:~$ getfacl security.txt
# file: security.txt
# owner: alice
# group: alice
user::rw-
group::rw-
other::---
```

Sada bob nema dopuštenje vidjeti sadržaj security.txt

```bash
bob@DESKTOP-LH5GUL2:~$ cat home/alice/srp/security.txt
cat: home/alice/srp/security.txt: Permission denied
```

**Boba dodamo u grupu alice kako bi imao prava čitanja sadržaja security.txt**

```bash
a507@DESKTOP-LH5GUL2:/mnt/c/Users/A507$ sudo usermod -aG alice bob
[sudo] password for a507:
```

```bash
bob@DESKTOP-LH5GUL2:~$ cat home/alice/srp/security.txt
Hello world
```

**C. Kontrola pristupa korištenjem *Access Control Lists (ACL)***

**Dodavanje boba u zasebnu grupu s dopuštenjem čitanja** 

Slično dodavanju uloge zasebnoj grupi (RBAC)

```bash
setfacl -m u:bob:r security.txt
```

**D. Linux procesi i kontrola pristupa**

**Kreiranje datoteke (lab_6.py)**

```python
import os

print 'Real (R), effective (E) and saved (S) UIDs:'
print(os.getresuid())

with open('/home/alice/srp/security.txt', 'r') as f:
    print(f.read())
```

**Pokretanje lab_6.py**

```bash
a507@DESKTOP-LH5GUL2:/mnt/c/Users/A507$ python lab_6.py
Real (R) effective (E) and saved (S) UIDs:
(1000, 1000, 1000)
Traceback (most recent call last):
	File "lab_6.py", line 6, in <module>
		with open('/home/alice/srp/security.txt', 'r') as f:
IOError: [Errno 13] Permission denied: '/home/alice/srp/security.txt'

```

**Provjera je li bob ima pristup security.txt**

```bash
a507@DESKTOP-LH5GUL2:/$ getfacl home/alice/srp/security.txt
# file: home/alice/srp/security.txt
# owner: alice
# group: alice
user::rw-
user:bob:r--
group::rw-
mask::rw-
other::---
```

```bash
bob@DESKTOP-LH5GUL2:~$ python home/alice/lab_6.py
Real (R) effective (E) and saved (S) UIDs:
(1002, 1002, 1002)
Hello world
```

**Ukoliko bob želi promijeniti svoju lozinku dok je u procesu passwd trenutno ima dopuštenje pisati u shadow file**

```bash
bob@DESKTOP-LH5GUL2:~$ ls -l $(which passwd)
-rwsr-xr-x 1 root root 59640 Mar 22 2019 /usr/bin/passwd
```

```bash
bob@DESKTOP-LH5GUL2:~$ passwd
Changing password for bob.
(current) UNIX password:
```

```bash
a507@DESKTOP-LH5GUL2:/$ ps -eo pid,ruid,euid,suid,cmd | grep passwd
664  1002     0     0 passwd
667  1000  1000  1000 grep --color=auto passwd
```