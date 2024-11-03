# Harjoitus 2

# Ympäristö:

## Fyysinen ympäristö:
- Intel i7-10700K @ 3,80GHz (AMD64)
- Nvidia RTX 3080
- 32 Gt RAM
- Windows 11 Education 23H2

## Virtuaaliympäristö (Kali)
- Oracle VirtualBox 7.1.4
- 8 Prosessoriydintä
- 16 Gt RAM
- Kali Linux 2024.3
## Virtuaaliympäristö (Metasploitable)
- Oracle VirtualBox 7.1.4
- 1 Prosessoriydin
- 2 Gt RAM
- Metasploitable 2.0

# a) Verkkojen toimivuus

**3.11.2024 10:05**

Aloitin testaamalla, että verkot toimivat keskenään, mutta Kali ei ole yhteydessä verkkoon.
Tein tämän pingaalmalla molemmilla koneilla osoitetta 8.8.8.8 komennolla 'ping 8.8.8.8'

![kuva](https://github.com/user-attachments/assets/74b49bb2-0b8d-452d-b014-01b667d268f4)
*Kali ei julkisessa verkossa*

![kuva](https://github.com/user-attachments/assets/ffa17500-cf13-4e4f-8389-ceacf08fe472)
*Metasploitable ei julkisessa verkossa



Tämän jälkeen selvitin koneiden IP-osoitteet 'ifconfig' komennolla.

![kuva](https://github.com/user-attachments/assets/045441e2-9733-4372-a0b4-d2821427ff26)





