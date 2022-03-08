---
layout: post
title:  "BlindScout Crypto Challenge"
date:   2022-03-08 09:29:20 +0700
categories: ctfwriteup
---

### Challenge Name : BlindScout

<img src="https://raw.githubusercontent.com/kabilan1290/WebCTF/master/pragyan/Screenshot_2022-03-09_03-08-05.png">
<p>• BlindScout is a challenge from PragyanCTF and this is the only crypto challenge i was able to solve during the competition :( .</p>

<p>**“Failure is delay, not defeat. It is a temporary detour, not a dead end.” ― Denis Waitley**</p>

<p>• Btw our team had the first blood of the challenge and happy about it!</p>

<img src="https://raw.githubusercontent.com/kabilan1290/WebCTF/master/pragyan/Screenshot_2022-03-09_03-16-39.png">

<p>• The challenge gives us a zip file and inside the zip,there are about 5 pem key files and a ciphertext</p>

<img src="https://raw.githubusercontent.com/kabilan1290/WebCTF/master/pragyan/Screenshot_2022-03-09_03-08-48.png">

<p>• We may guess this might be a challenge related to faulty modulus generator(Since there are 5 pem keys and only one ciphertext),for example two different public modulus n shares common prime p or q!</p> 

<p>• There are 5 modulus n1,n2,n3,n4,n5 in the pem file and i began my check through GCD(n1,n2),GCD(n1,n3) and so on...</p>

<p>• So i found that n2 and n4 shares a prime and we can decrypt the ciphertext with d1,d2 and checks which of this yield a printable message.</p>

<p>• Its founded that we can obtain the flag with modulus n4.</p>

<p>• Below is the script for decryption! </p>

```
from Crypto.PublicKey import RSA
from gmpy import *
from Crypto.Util.number import *
import base64
from Crypto.Cipher import PKCS1_OAEP
key1 = RSA.importKey(open("pub1.pem").read())
key2 = RSA.importKey(open("pub2.pem").read())
key3 = RSA.importKey(open("pub3.pem").read())
key4 = RSA.importKey(open("pub4.pem").read())
key5 = RSA.importKey(open("pub5.pem").read())


p_2_4 = gcd(key2.n,key4.n)

q_2 = key2.n // p_2_4

q_4 = key4.n // p_2_4

cipher = "Z9jO5jqN9+fKNYJ14xA3QV96x4AlIIjOwoGSSq2D0G6ddMnKipNJkS2n0IS3blQAMym5dnzKC5MIetKikgozmzruuKDn2Xbkdv529Na2MXizJEMTxP/ioYzUFl2rJfg7xvyrNxEyPRWoJievmjpnum2pkrWAknAb+6Hj0Qv5yIo="
cipher = base64.b64decode(cipher)


cipher = bytes_to_long(cipher)

phi1 = (p_2_4-1)*(q_2-1)

d1 = inverse(key2.e,phi1)

m1 = pow(cipher,d1,key2.n)

phi2= (p_2_4-1)*(q_4-1)

d2 = inverse(key4.e,phi2)

m2 = pow(cipher,d2,key4.n)

print(long_to_bytes(m2))

#p_ctf{010100_100000101100_100110100000111010100010100110100010111001010100111000}
```

<p>• The challenge does not stopped here,since the flag value looked like a set of binaries "p_ctf{010100_100000101100_100110100000111010100010100110100010111001010100111000}" </p>

<p>• Reading the challenge again "BLIND" which is a reference to braille and decoding with braille gives us the flag</p>

<p>• Braille Decoder - <a href="https://www.dcode.fr/braille-alphabet">https://www.dcode.fr/braille-alphabet</a></p>

#### Flag:

p_ctf{I_AM_DAREDEVIL}