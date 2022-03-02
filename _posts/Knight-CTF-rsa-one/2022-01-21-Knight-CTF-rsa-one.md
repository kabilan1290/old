---
layout: post
title:  "Knight CTF -RSA one!"
date:   2022-01-21 09:29:20 +0700
categories: ctf
---

   <img src ="https://raw.githubusercontent.com/kabilan1290/crypto/main/challenge/knightCTF/RSA-one/RSA2.png">
<br>
In this Challenge we were given with two file flag.enc and private.pem.

Challenge Description says
 `Our security agency has got hold of a ciphertext and a key. Well..the key got corrupted and the lost character is represented by a x. Can you decipher the message for us?`
<br>
Viewing the private.pem file,we can notice a emoji is replaced in the place of a letter,which would make the private key invalid.
<br>

<img src="https://raw.githubusercontent.com/kabilan1290/crypto/main/challenge/knightCTF/RSA-one/rsa1.png">
<br>

We can brutefoce all possible lowercase+uppercase alphabets and for each iteration decrypt the flag.enc.
<br>

Print the flag if we got a hit on the string which matches `KCTF`
<br>

Below is the Exploit script.
<br>

```
from Crypto.Util.number import long_to_bytes
from Crypto.PublicKey import RSA
import string
import re
a='''-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAyiytHt1AKzYLwZPm1dd9uT7LgsqVj0eSLpheNd0H4xyiZCYG
ZtRYnNtGNnq7A/ubyFalExm61QNewfy71h6xhM/'''

b = '''IEIoNT0VfMOIzcq0Jmoh+v6k
6/x/3GRkk/vLVolsLbkOKd4aorPMwEsZX4vMd+Sga5Mz0tx5xLFZsbl0r1vvtBl7
CtC/ojWX4+/RSGuaUVVayrU32kyCjJo3hniSaY2EvSXXHdE6nOKkF725LVrnOOIz
1/n9CYrYPV6ESEBdwS7VOen8uPwh5cFGHOV49ofmvVZNvcV2qoKFjY5UXf8fDzZ+
jBzWiCukE+3WFwgEYaBGg/a6HomkobpDqxkrYwIDAQABAoIBABht45FaLLnL8wm2
BGuMeV2b791i+0Vv4YMN2Dxr89sGh7zQN2/PctGpUUed9uEZUw6XIaU4M7IvkRCh
qFTMKqkgrVd4hwE/20vTGMG9H52Qr4Bzqpv1S8Hmw5x6DWzseAziUorOkqtcTH5j
1LIN42wNTTESfW2aRIB26Z6nCSlzHD8jpBYlrBFNsXydApEtA86PPtgs8MUsABFa
Rhy6VG9rNfzaBeRDX1m1lX+yNkqPb3xgABeYgURYgUneiTY/S5GrFfrtRAnLWVm4
audCUkxvF8OV0vJnazcMUopleBonMH2FCl3vKAjTX2xq9X24PeNXDg6SfiEEuI/g
EDtJO1ECgYEAzwBWVwbx/lvc5PP3oYXRr9IpflZ3Z9xSyopY0KpOakAXn6717x6i
s/1DwGvpmFBqUd25vhcn9ztj18GtMCtZ4dNvvyGpPwvM41Z1RVHY5REfC7sgBp8W
0N+IVR2QlyU3pjoS5t19O3g48fhOp8o3wsZ+05RpLtUhNXe0yHxk7fsCgYEA+gfZ
aCr+dgzHfdBOEwwozaRpJANchnGeILSgZZEeYmyE0RuBcatpwxKs+jG82mWYnosN
KR5CZZiPn/laySUQEB5H6Cg/OQDVyj5r49adc2H8hTCluaXtiVyxA3JqV8Ixc9TM
cRWJZdokaDbkyNXCuUuTMinzWjrNBKBZ+zg5w7kCgYBQkjwJEb39mHoJb+CSMUkl
23KlJzjA52QeS+04AyIUfy/yyqIVWeJQlqLZcedxjtNjXB9hGxhGRgqdv1gO6MDK
gob7aTm8PXaZglyRB8OZnals4oAbs66ozGj/YEuYWTco72/OBqYpEKlxnYnYC4Da
wnI5Hoo2XWTYr+hhJPIQIwKBgAxMxo0xUENObaHq1WxqdLdpFyMGZ07V2AmT2TAl
63C8FeyThdKptBI8oPXN7JRx2wgxnvwe2PVWg/pCsgyjHh8s3iy1jianu9yvJW+X
5zb94wZKVlzDpOPVA4A/6KtYikZAea42eQPhr1jRGoAmw+WJqjwVhDs0GVHY8ZRC
N9VBAoGBAJTZwrY+tZkNzURk9JLWzrevfD6BpYrQ0jchaGtzdgjdOpHo3++cdUag
9oQ8ZNKaUVDm3lyzUhO41Hw7xMmmW8JwsVvKdrRL+ZG12Ts/uiy1P0DY+HsNMr9d
xqG9YAHVmm4iJzcHeMdzLwmzR6D/x6+k2cFWwox6PxvA7ikJQEYr
-----END RSA PRIVATE KEY-----
'''
alphabet_string = string.ascii_lowercase
alphabet_string2 = string.ascii_uppercase
ct = 3727860015271984841324159765928537422111591563168703246274534058212860148324984926533129834972015501237164337596283528586314608137883075991757210424415228060370930359406872327945577230190541395424158421129540532799576716844437077695468441298361281287281500731107510110919792316394598802631743356037461925682629386640483698027096813857434182454560793760145474202157717338360918130962780548424537332305217288434027436974151260268471169603628280221742021890088311856300748132434057776577308980718407083905792718517692206286940837721132833739445400411565013638730221599142594621268990711490374043387501971024260884088096
alphabets = alphabet_string+alphabet_string2
length = len(alphabets)
for i in range(0,length):
	key = RSA.importKey(a+alphabets[i]+b)
	n = key.n
	d = key.d
	m = pow(ct,d,n)
	m = long_to_bytes(m)
	if (m.find('KCTF') != -1):
		flag = re.compile('KCTF+.*')
		print(flag.findall(m)[0])
		
#Flag KCTF{M4Y_TH3_8RUT3F0rc3_B3_W1TH_Y0U}
```

<br>


Flag : KCTF{M4Y_TH3_8RUT3F0rc3_B3_W1TH_Y0U}
