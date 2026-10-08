# Documento de Respuesta 
---
## Salida dig @localhost xunta.gal no equipo darthvader
```
root@darthvader:/var/cache/bind# dig @localhost xunta.gal

; <<>> DiG 9.20.29-1~deb13u1-Debian <<>> @localhost xunta.gal
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 21417
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 95d7c87e912d537e010000006ac61ac2e0a7716144749be2 (good)
;; QUESTION SECTION:
;xunta.gal.                     IN      A

;; ANSWER SECTION:
xunta.gal.              28800   IN      A       85.91.64.109

;; Query time: 188 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Wed Oct 07 10:11:14 UTC 2026
;; MSG SIZE  rcvd: 82

```

---

## Contido ficheiro named.conf.options de mandalorian
```
options {
	directory "/var/cache/bind";
		forwarders{192.168.20.10;};
};
```
## Salida dig @localhost santiagodecompostela.gal

```
root@mandalorian:~# dig @localhost santiagodecompostela.gal

; <<>> DiG 9.20.29-1~deb13u1-Debian <<>> @localhost santiagodecompostela.gal
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52812
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 44ee356ba3b5f247010000006ac6196684746d81428a3ae2 (good)
;; QUESTION SECTION:
;santiagodecompostela.gal.      IN      A

;; ANSWER SECTION:
santiagodecompostela.gal. 150   IN      A       195.57.25.148

;; Query time: 3 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Wed Oct 07 10:05:26 UTC 2026
;; MSG SIZE  rcvd: 97

root@mandalorian:~#
```

---

## Contido arquivo de configuración da zona starwars.lan

```
$TTL 86400
@       IN      SOA     darthvader.starwars.lan. admin.exemplo.com. (
                        20261006          ; Número de serie
                        3600       ; Actualización (Refresh)
                        1800       ; Reintento (Retry)
                        1209600    ; Caducidade (Expire)
                        86400 )    ; TTL mínimo

; Servidores de nomes (NS)
@       IN      NS      darthvader.starwars.lan.
@       IN      NS      darthsidious.starwars.lan.

; Servidores de correo (MX)
@       IN      MX      10 c3p0.starwars.lan.

; Registros TXT 
lenda   IN  TXT "Que a forza te acompanhe"

; Rexistros A
darthvader     IN  A       192.168.1.10
skywalker      IN  A       192.168.20.101
skywalker      IN  A       192.168.20.111
luke           IN  A       192.168.20.22
darthsidious   IN  A       192.168.20.11
yoda           IN  A       192.168.20.24
yoda           IN  A       192.168.20.25
c3p0           IN  A       192.168.20.26
palpatine      IN  CNAME   darthsidious.starwars.lan.

```

## Contido arquivo /etc/bind/named.conf.local *(Zona de resolución Directa)*

```
//
// Do any local configuration here
//

zone "starwars.lan"{
    type primary;
    file "/etc/bind/db.starwars.lan";
};
```
---

## Contido arquivo de configuración da zona inversa

```
$TTL 86400
@       IN      SOA     darthvader.starwars.lan. admin.exemplo.com. (
                        20261006   ; Número de serie
                        3600       ; Actualización (Refresh)
                        1800       ; Reintento (Retry)
                        1209600    ; Caducidade (Expire)
                        86400 )    ; TTL mínimo

; Servidores de nomes (NS)
@       IN      NS      darthvader.starwars.lan.
@       IN      NS      darthsidious.starwars.lan.

; Rexistros PTR
10           IN  PTR       darthvader 
101          IN  PTR       skywalker
111          IN  PTR       skywalker
22           IN  PTR       luke
11           IN  PTR       darthsidious
24           IN  PTR       yoda
25           IN  PTR       yoda
26           IN  PTR       c3p0
```

## Contido arquivo /etc/bind/named.conf.local *(Zona de resolución inversa)*

```
//
// Do any local configuration here
//

zone "starwars.lan"{
    type primary;
    file "/etc/bind/db.starwars.lan";
};

zone "20.168.192.in-addr.arpa"{
    type primary;
    file "/etc/bind/db.20.168.192";
};
```
---

## Salida dos comandos nslookup

### nslookup darthvader.starwars.lan localhost

```
Server:         localhost
Address:        127.0.0.1#53

Name:   darthvader.starwars.lan
Address: 192.168.1.10
```

### nslookup skywalker.starwars.lan localhost
```
Server:         localhost
Address:        127.0.0.1#53

Name:   skywalker.starwars.lan
Address: 192.168.20.101
Name:   skywalker.starwars.lan
Address: 192.168.20.111
```

### nslookup starwars.lan localhost
```
Server:         localhost
Address:        127.0.0.1#53

*** Can't find starwars.lan: No answer
```

### nslookup -q=mx starwars.lan localhost
```
Server:         localhost
Address:        127.0.0.1#53

starwars.lan    mail exchanger = 10 c3p0.starwars.lan.
```

### nslookup -q=ns starwars.lan localhost
```
Server:         localhost
Address:        127.0.0.1#53

starwars.lan    nameserver = darthvader.starwars.lan.
starwars.lan    nameserver = darthsidious.starwars.lan.
```

### nslookup -q=soa starwars.lan localhost
```
Server:         localhost
Address:        127.0.0.1#53

starwars.lan
        origin = darthvader.starwars.lan
        mail addr = admin.exemplo.com
        serial = 20261006
        refresh = 3600
        retry = 1800
        expire = 1209600
        minimum = 86400
```

### nslookup -q=txt lenda.starwars.lan localhost
```
Server:         localhost
Address:        127.0.0.1#53

lenda.starwars.lan      text = "Que a forza te acompanhe"
```

### nslookup 192.168.20.11 localhost
```
11.20.168.192.in-addr.arpa      name = darthsidious.starwars.lan.
```