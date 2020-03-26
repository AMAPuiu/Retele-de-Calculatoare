# Tema 2

## Informații temă
Punctaj: 20% din total pentru laborator.
Deadline: **27 martie 2020**, se va lucra individual.

**UPDATE**: Predarea soluției se va face într-un repository de github.
Pentru a va inscrie folositi acest link: [https://classroom.github.com/a/9xUyiMpD](https://classroom.github.com/a/9xUyiMpD)

În repository scrieti sub forma de text sau markdown rezultatele voastre, puteți adăuga printscreen-uri din terminal, bucăți de cod și observații sau explicații pentru soluționarea exercițiilor. 

Pentru printscreen, asigurați-vă că este vizibil usernameul cu care faceți apelulrile din terminal.

## Cerințe

1. Citiți informațiile despre [HTTP/S din capitolul2](https://github.com/senisioi/computer-networks/tree/2020/capitolul2#https). 
2. Citiți informațiile despre [UDP din capitolul2](https://github.com/senisioi/computer-networks/tree/2020/capitolul2#socket)
3. Citiți informațiile despre [TCP din capitolul2](https://github.com/senisioi/computer-networks/tree/2020/capitolul2#tcp)


Rezolvați:
- exercițiile de la sectiune [HTTP](https://github.com/senisioi/computer-networks/tree/2020/capitolul2#exercitii_http) (3%)
- exercitiile de la secțiunea [UDP](https://github.com/senisioi/computer-networks/tree/2020/capitolul2#exercitii_udp) (10%)
- exercițiile de la secțiunea [TCP](https://github.com/senisioi/computer-networks/tree/2020/capitolul2#exercitii_tcp). (7%)



# Exemplu solutie cu markdown


## Exerciții HTTP/S
1. Cloudflare are un serviciu DoH care ruleaza pe IP-ul [1.1.1.1](https://blog.cloudflare.com/announcing-1111/). Urmăriți [aici documentația](https://developers.cloudflare.com/1.1.1.1/dns-over-https/json-format/) pentru request-uri de tip GET către cloudflare-dns și scrieți o funcție care returnează adresa IP pentru un nume dat ca parametru. Indicații: setați header-ul cu {'accept': 'application/dns-json'}.
```python
import requests
import json

def print_ip(name):
    # dictionar cu headerul HTTP sub forma de chei-valori
    headers = {
        "Accept": 'application/dns-json',
        "Accept-Language": "en-US,en",
        "Cookie": "__utmc=177244722",
        "User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.79 Safari/537.36"
    }
    param = {
        "name": name
    }
    response = requests.get('https://cloudflare-dns.com/dns-query', params = param, headers=headers)  
    return response.json()['Answer'][0]['data']
    

if __name__ == "__main__":
    print(print_ip("fmi.unibuc.ro"))
```

---

2. Executati pe containerul `rt1` scriptul 'simple_flask.py' care deserveste API HTTP pentru GET si POST. Daca accesati in browser [http://localhost:8001](http://localhost:8001) ce observati?
```
Apare scris "Hello World!".
```
---

3. Conectați-vă la containerul `docker-compose exec rt2 bash`. Testati conexiunea catre API-ul care ruleaza pe rt1 folosind curl: `curl -X POST http://rt1:8001/post  -d '{"value": 10}' -H 'Content-Type: application/json'`. Scrieti o metoda POST care ridică la pătrat un numărul definit în `value`. Apelați-o din cod folosind python requests.
```python
from flask import Flask, jsonify
from flask import request

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello World!"

@app.route('/post', methods=['POST'])
def post_method():
    res = request.get_json()
    print("Got from user: ", res)
    print(f"Squared value: {res['value'] * res['value']}")
    return jsonify({'got_it': 'yes'})

@app.route('/<name>')
def hello_name(name):
    return "Hello {}!".format(name)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8001)
```

---

4. Urmăriți alte exemple de request-uri pe [HTTPbin](http://httpbin.org/)
```
Am aflat de metodele HTTP, GET, POST, PUT, DELETE si de [PATCH](http://httpbin.org/#/HTTP_Methods/patch_patch)
```

---


## Exerciții UDP
1. Executați serverul apoi clientul fie într-un container de docker fie pe calculatorul vostru personal: `python3 udp_server.py` și `python3 udp_client.py "mesaj de trimis"`.

Printscreen cu rezultatul:
![alt text](https://github.com/nlp-unibuc/tema-2-AMAPuiu/blob/master/Printscreens/udp_server.jpg)
![alt text](https://github.com/nlp-unibuc/tema-2-AMAPuiu/blob/master/Printscreens/udp_client.jpg)

---

2. Modificați adresa de pornire a serverului din 'localhost' în IP-ul rezervat descris mai sus cu scopul de a permite serverului să comunice pe rețea cu containere din exterior. 
```python
port = 10000
adresa = '0.0.0.0'
server_address = (adresa, port)
```

---

3. Porniți un terminal în directorul capitolul2 și atașați-vă la containerul rt1: `docker-compose exec rt1 bash`. Pe rt1 folositi calea relativă montată în directorul elocal pentru a porni serverul: `python3 /elocal/src/udp_server.py`. 
![alt text](https://github.com/nlp-unibuc/tema-2-AMAPuiu/blob/master/Printscreens/udp_eth0.jpg)

---

4. Modificați udp_client.py ca el să se conecteze la adresa serverului, nu la 'localhost'. Sfaturi: puteți înlocui localhost cu adresa IP a containerului rt1 sau chiar cu numele 'rt1'.
```python
port = 10000
adresa = 'rt1'
server_address = (adresa, port)
```
---

5. Porniți un al doilea terminal în directorul capitolul2 și rulați clientul în containerul rt2 pentru a trimite un mesaj serverului: 
![alt text](https://github.com/nlp-unibuc/tema-2-AMAPuiu/blob/master/Printscreens/udp_client_eth0.jpg)
---

6. Deschideți un al treilea terminal și atașați-vă containerului rt1: `docker-compose exec rt1 bash`. Utilizați `tcpdump -nvvX -i any udp port 10000` pentru a scana mesajele UDP care circulă pe portul 10000. Apoi apelați clientul pentru a genera trafic.
```
ana@ana-VirtualBox:~/Desktop/Workshop/Retele/computer-networks/capitolul2$ docker-compose exec rt1 bash
root@c8a5ee4617ac:/# tcpdump -nvvX -i any udp port 10000
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked v1), capture size 262144 bytes
18:45:46.080858 IP (tos 0x0, ttl 64, id 49334, offset 0, flags [DF], proto UDP (17), length 36)
    172.9.0.1.58458 > 172.9.0.2.10000: [bad udp cksum 0x5837 -> 0x1bb0!] UDP, length 8
	0x0000:  4500 0024 c0b6 4000 4011 21fd ac09 0001  E..$..@.@.!.....
	0x0010:  ac09 0002 e45a 2710 0010 5837 4865 6565  .....Z'...X7Heee
	0x0020:  6969 6969                                iiii
18:45:46.081179 IP (tos 0x0, ttl 64, id 25483, offset 0, flags [DF], proto UDP (17), length 36)
    172.9.0.2.10000 > 172.9.0.1.58458: [bad udp cksum 0x5837 -> 0x1bb0!] UDP, length 8
	0x0000:  4500 0024 638b 4000 4011 7f28 ac09 0002  E..$c.@.@..(....
	0x0010:  ac09 0001 2710 e45a 0010 5837 4865 6565  ....'..Z..X7Heee
	0x0020:  6969 6969                                iiii
18:45:47.460032 IP (tos 0x0, ttl 64, id 49653, offset 0, flags [DF], proto UDP (17), length 36)
    172.9.0.1.50285 > 172.9.0.2.10000: [bad udp cksum 0x5837 -> 0x3b9d!] UDP, length 8
	0x0000:  4500 0024 c1f5 4000 4011 20be ac09 0001  E..$..@.@.......
	0x0010:  ac09 0002 c46d 2710 0010 5837 4865 6565  .....m'...X7Heee
	0x0020:  6969 6969                                iiii
18:45:47.460500 IP (tos 0x0, ttl 64, id 25682, offset 0, flags [DF], proto UDP (17), length 36)
    172.9.0.2.10000 > 172.9.0.1.50285: [bad udp cksum 0x5837 -> 0x3b9d!] UDP, length 8
	0x0000:  4500 0024 6452 4000 4011 7e61 ac09 0002  E..$dR@.@.~a....
	0x0010:  ac09 0001 2710 c46d 0010 5837 4865 6565  ....'..m..X7Heee
	0x0020:  6969 6969                                iiii
```

---

7. Containerul rt1 este definit în [docker-compose.yml](https://github.com/senisioi/computer-networks/blob/2020/capitolul2/docker-compose.yml) cu redirecționare pentru portul 8001. Modificați serverul și clientul în așa fel încât să îl puteți executa pe containerul rt1 și să puteți să vă conectați la el de pe calculatorul vostru sau de pe rețeaua pe care se află calculatorul vostru.
```
Am modificat in docker-compose.yml:         
	ports:
		- "8001:8001/udp"
In server si in client am modificat portul in 8001.

root@c8a5ee4617ac:/# tcpdump -nvvX -i any udp port 8001 
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked v1), capture size 262144 bytes
19:07:15.310454 IP (tos 0x0, ttl 64, id 55074, offset 0, flags [DF], proto UDP (17), length 36)
    172.9.0.1.35609 > 172.9.0.2.8001: [bad udp cksum 0x5837 -> 0x7cc0!] UDP, length 8
	0x0000:  4500 0024 d722 4000 4011 0b91 ac09 0001  E..$."@.@.......
	0x0010:  ac09 0002 8b19 1f41 0010 5837 4865 6565  .......A..X7Heee
	0x0020:  6969 6969                                iiii
19:07:15.311170 IP (tos 0x0, ttl 64, id 13825, offset 0, flags [DF], proto UDP (17), length 36)
    172.9.0.2.8001 > 172.9.0.1.35609: [bad udp cksum 0x5837 -> 0x7cc0!] UDP, length 8
	0x0000:  4500 0024 3601 4000 4011 acb2 ac09 0002  E..$6.@.@.......
	0x0010:  ac09 0001 1f41 8b19 0010 5837 4865 6565  .....A....X7Heee
	0x0020:  6969 6969                                iiii

```
---


## Exerciții TCP

1. Executați serverul apoi clientul fie într-un container de docker fie pe calculatorul vostru personal: `python3 tcp_server.py` și `python3 tcp_client.py "mesaj de trimis"`.
```
prinscreen sau daca a mers la UDP, aici nu mai e necesar
```
---

2. Modificați adresa de pornire a serverului din 'localhost' în IP-ul rezervat '0.0.0.0' cu scopul de a permite serverului să comunice pe rețea cu containere din exterior. Modificați tcp_client.py ca el să se conecteze la adresa serverului, nu la 'localhost'. Pentru client, puteți înlocui localhost cu adresa IP a containerului rt1 sau chiar cu numele 'rt1'.
```
daca mers la UDP, aici nu mai e necesar
```

---

3. Într-un terminal, în containerul rt1 rulați serverul: `docker-compose exec rt1 bash -c "python3 /elocal/src/tcp_server.py"`. 

```
daca mers la UDP, aici nu mai e necesar
```

---

4. Într-un alt terminal, în containerul rt2 rulați clientul: `docker-compose exec rt1 bash -c "python3 /elocal/src/tcp_client.py TCP_MESAJ"`

Printscreen cu rezultatul:
![alt text](https://github.com/nlp-unibuc/tema-2-AMAPuiu/blob/master/Printscreens/tcp_server.jpg)
![alt text](https://github.com/nlp-unibuc/tema-2-AMAPuiu/blob/master/Printscreens/tcp_client.jpg)

---

5. Mai jos sunt explicați pașii din 3-way handshake captați de tcpdump și trimiterea unui singur byte de la client la server. Salvați un exemplu de tcpdump asemănător care conține și partea de [finalizare a conexiunii TCP](http://www.tcpipguide.com/free/t_TCPConnectionTermination-2.htm). Sfat: Modificați clientul să trimită un singur byte fără să facă recv. Modificați serverul să citească doar un singur byte cu recv(1) și să nu facă send. Reporniți serverul din rt1. Deschideți un al treilea terminal, tot în capitolul2 și rulați tcpdump: `docker-compose exec rt1 bash -c "tcpdump -Snnt tcp"` pentru a porni tcpdump pe rt1. 

#### SYN
```
19:12:29.777993 IP 127.0.0.1.48178 > 127.0.0.1.8001: Flags [S], seq 423619793, win 65495, options [mss 65495,sackOK,TS val 2569496588 ecr 0,nop,wscale 7], length 0
```
 - Flags [S] - cerere de sincronizare de la adresa 127.0.0.1 cu portul 48178 către adresa 127.0.0.1 cu portul 8001
 - seq 423619793 - primul sequence nr pe care îl setează clientul în mod aleatoriu
 - win 65495 - Window Size inițial
 - options [mss 65495,sackOK,TS val 2569496588 ecr 0,nop,wscale 7] - reprezintă Opțiunile de TCP
 - length 0 - mesajul SYN nu are payload, conține doar headerul TCP
 
#### SYN-ACK:
```
19:12:29.778003 IP 127.0.0.1.8001 > 127.0.0.1.48178: Flags [S.], seq 1318475688, ack 423619794, win 65483, options [mss 65495,sackOK,TS val 2569496588 ecr 2569496588,nop,wscale 7], length 0
```
 - Flags [S.] - . (punct) reprezintă flag de Acknowledgement din partea serverului (127.0.0.1.8001) că a primit pachetul și returnează și un Acknowledgement number: ack 423619794 care reprezintă Sequence number trimis de client + 1 (vezi mai sus la SYN).
 - Flags [S.] - în același timp, serverul trimite și el un flag de SYN și propriul Sequence number: seq 1318475688
 - optiunile sunt la fel ca înainte și length 0, mesajul este compus doar din header, fără payload

#### ACK:
```
19:12:29.778011 IP 127.0.0.1.48178 > 127.0.0.1.8001: Flags [.], ack 1318475689, win 512, options [nop,nop,TS val 2569496588 ecr 2569496588], length 0
```
 - Flags [.] - . (punct) este pus ca flack de Ack și se transmite Ack Number ca fiind seq number trimis de server + 1: ack 1318475689
 - length 0, din nou, mesajul este fără payload, doar cu header
 
 #### PSH:
 ```
19:12:32.782507 IP 127.0.0.1.48178 > 127.0.0.1.8001: Flags [P.], seq 423619794:423619795, ack 1318475689, win 512, options [nop,nop,TS val 2569499593 ecr 2569496588], length 1
```
 - Flags [P.] - avem P și . (punct) care reprezintă PUSH de mesaj nou și Ack ultimului mesaj
 - seq 423619794:423619795 - se trimite o singură literă (un byte) iar numărul de secvență indică acest fapt
 - ack 1318475689 - la orice mesaj, se confirmă prin trimiterea de Ack a ultimului mesaj primit, in acest caz se re-confirmă SYN-ACK-ul de la server
 - length 1 - se trimite un byte în payload
 
#### ACK:
```
19:12:32.782522 IP 127.0.0.1.8001 > 127.0.0.1.48178: Flags [.], ack 423619795, win 512, options [nop,nop,TS val 2569499593 ecr 2569499593], length 0
```
 - Flags [.] - flag de Ack
 - ack 423619795 - semnifică am primit octeți pana la 423619794, aștept octeți de la Seq 423619795
 - length 0 - un mesaj de confirmare nu are payload







