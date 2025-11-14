---
layout: post
author: M0hmedGamal (gem0x00)
categories: [CTF, Web]
tags: [CTF-Solutions] 
---
#Gogeta

Hello , At first when I saw the website it was like this :
<img width="1145" height="643" alt="Pasted image 20251114230844" src="https://github.com/user-attachments/assets/fe80b3b2-2c1b-4d2c-af52-485384397f03" />


The source Code is very simple but there is something took my eye :

<img width="471" height="162" alt="Pasted image 20251114230956" src="https://github.com/user-attachments/assets/e7fb8332-6644-4d3c-a8f4-8de7d4067722" />


After researching the vulnerability and methods of exploitation, the following GitHub issue provided essential insight and a proof-of-concept that facilitated successful exploitation: [https://github.com/golang/go/issues/23867](https://github.com/golang/go/issues/23867).

At first i tried the exploit in it :
```
khashaev.ru/go-vuln/index.html
```

that is the online page that carry the payload and it worked :
<img width="989" height="48" alt="Pasted image 20251114232502" src="https://github.com/user-attachments/assets/284a2d33-7f8a-4bd7-b57e-6c80eae2d079" />

<img width="913" height="198" alt="Pasted image 20251114232549" src="https://github.com/user-attachments/assets/21f5d3a6-727e-4e99-90a2-40a09475bd40" />


Then I crafted my own exploit to get the flag and hosted it via `ngrok`:
<img width="1339" height="151" alt="Pasted image 20251114232654" src="https://github.com/user-attachments/assets/4848b8cc-c8c4-468f-a846-cdecd139c9da" />


The Exploit and got the flag :
<img width="875" height="180" alt="Pasted image 20251114232829" src="https://github.com/user-attachments/assets/7dd09a11-dd39-4975-b098-921a9c8e802b" />

