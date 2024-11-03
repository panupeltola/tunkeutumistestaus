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

*Metasploitable ei julkisessa verkossa*

Tämän jälkeen selvitin koneiden IP-osoitteet 'ifconfig' komennolla.

![kuva](https://github.com/user-attachments/assets/045441e2-9733-4372-a0b4-d2821427ff26)

*Kali*

![kuva](https://github.com/user-attachments/assets/96258f9f-438a-43eb-907e-028d50ff1290)

*Metasploitable*

Kun olin saanut molempien koneiden IP-osoitteet selville koetin 'ping komentoa molemmilta koneilta toisilleen.

- Kali: 192.168.56.104
- Meta: 192.168.56.103

![kuva](https://github.com/user-attachments/assets/e14abd7f-1668-4cec-84d2-0c84f608c27f)

*'ping' toimii molempiin suuntiin, verkot ovat yhteydessä lähiverkossa*

# b) MSFconsole

Loin ensin tietokannan komennolla 'sudo msfbdb init'. Alla olevassa kuvassa olin luonut sen jo aiemmassa sessiossa, joten sitä ei uudelleen voinut luoda.

![kuva](https://github.com/user-attachments/assets/9fe91982-790c-47f5-88ac-35a916444274)

Avasin konsolin komennolla 'sudo msfconsole'

![kuva](https://github.com/user-attachments/assets/118887b0-c9df-46d7-be33-52a38ce66c18)

Olin sisällä.

# c) Skannaa Metasploitable verkosta

Tietäen, että verkkoni osoite on 192.168.56.0/24 ajoin tämän skannaukseen komennolla 'db_nmap -sn 192.168.56.0/24' Tarkemmat selitykset komennosta löytyy tekemästäni Harjoituksesta 1.

![kuva](https://github.com/user-attachments/assets/2376d5d7-4e70-45b8-aa6b-dd7f44a39da6)

Skannauksen tuloksena näin, että osoite oli 192.168.56.103. Kalin verkon osoite on .104 päätteinen, oletettavasti default gateway on .1 päätteinen ja DHCP-palvelin on .100 päätteinen.

Tästä pääteltynä jäljelle jää vain edellä mainittu osoite.

# d) Skannaa Metasploitablen portit

Seuraavaksi skannasinn kaikki Metasploitablen yleisimmät portit komennolla 'db_nmap nmap -A -p- 192.168.56.103' joka tallentaa tiedot Metasploitin tietokantaan ja 'nmap -A -p- -oA foo 192.168.56.103'
En ollut täysin varma onko nmapin tallennuksen funktio oikein, mutta päätin sitä silti kokeilla ja korjaan sen jos se ei toimisi.
Nmapin vaihtoehto 'oA' on esitetty seuraavalla tavalla sen ohjeissa:
-oA <basename>: Output in the three major formats at once

Ajoin komennot.



![kuva](https://github.com/user-attachments/assets/13e751f7-fbe8-496c-9b85-ff7dede4823f)

*'db_nmap nmap -A -p- 192.168.56.103'*


















