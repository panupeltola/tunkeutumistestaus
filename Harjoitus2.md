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

Seuraavaksi skannasinn kaikki Metasploitablen yleisimmät portit komennolla 'db_nmap nmap -A -p- 192.168.56.103' joka tallentaa tiedot Metasploitin tietokantaan ja 'sudo nmap -A -p- -oA foo 192.168.56.103'
En ollut täysin varma onko nmapin tallennuksen funktio oikein, mutta päätin sitä silti kokeilla ja korjaan sen jos se ei toimisi.
Nmapin vaihtoehto 'oA' on esitetty seuraavalla tavalla sen ohjeissa:
-oA <basename>: Output in the three major formats at once

Ajoin komennot.

![kuva](https://github.com/user-attachments/assets/13e751f7-fbe8-496c-9b85-ff7dede4823f)

*'db_nmap nmap -A -p- 192.168.56.103'*


![kuva](https://github.com/user-attachments/assets/fe9a32c0-0e7c-464c-9d48-7b48d122c02b)

*'sudo nmap -A -p- -oA foo 192.168.56.103'*


![kuva](https://github.com/user-attachments/assets/1a8d9a5b-016d-4f7e-bbc7-7fc4dea5de39)

Myös syntaksi näytti olleen oikea, sillä kolme uutta tiedostoa oli ilmestynyt hakemistoon.

# e) Hae tietokannoista

Ajoin ensimmäisenä Metasploitin konsolissa komennot 'hosts' ja 'services'

![kuva](https://github.com/user-attachments/assets/7e33f0da-4db5-44e3-aad7-e4d14308dafb)

Sain vastaukseksi pitkän listan verkonskannauksen 'db_nmap -sn 192.168.56.0/24' löytämistä laitteista, sekä porttiskannauksen 'db_nmap nmap -A -p- 192.168.56.103' löytämistä avoimista palveluista.

Hosts listassa oli kaikki laitteet, niiden tyyppi sekä käyttöjärjestelmä, mikäli se oli saatu.

Services listassa oli host, jolla palvelu oli auki, palvelun portti, protokolla, palvelun nimi sekä siihen liittyvät lisätiedot.

Seuraavaksi halusin hakea Services osioista ftp liittyviä tietoja.
Yritin ensin komennolla 'services | grep vsftp' tästä sain vain virheilmoituksen "Invalid host parameter, |."
Päätin seuraavaksi yrittää komentoa 'grep ftp'. Tästä sain kuitenkin vain ohjeen apuikkunan auki, enkä mitään oikeaa tietoa. Pitänee kerrata taas grepin käyttö.

Lopuksi yritin vielä komentoa 'services -h' nähdäkseni onko tähän valmista hakutoimintoia

![kuva](https://github.com/user-attachments/assets/bcbc504f-43a2-49b5-a76b-7b0be918711d)

Ilokseni huomasin, että tässä on suora '-S' hakuvaihtoehto.
Ajoin komennon 'services -S ftp'

![kuva](https://github.com/user-attachments/assets/ecc8a5b2-6fe6-4be7-90df-d9a90e820d6f)

Sain vastaukseksi vain filtterin sisältämät tiedot. Koetin vielä samaa komentoa hosts kenttään hakusanalla "Linux"

![kuva](https://github.com/user-attachments/assets/1cdcf730-261d-47e2-84ce-e30b58aa3a23)

Löysin ainoan Linux koneeni listalta. Nämä hakutoiminnot itsessään jo ovat erittäin hyvä tapa selvittää potentiaalisia kohteita.

# f) Vertailu

Tutkin ensin miltä avatut tiedostot näyttävät.

Tiedostomuodoista vain .xml on itselleni tuttu ja tiedän sen olevan selkokielinen. Avasin sen komennolla 'less foo.xml'

Tiedon määrä oli aivan liikaa, sillä palveluun oli tallennettu kaikki skannauksessa saatu tieto

Seuraavaksi kertasin grepin käyttöä ja hain ftp liittyviä rivejä. Hain komennolla 'grep 'ftp' foo.xml'

![kuva](https://github.com/user-attachments/assets/e13329fe-8286-4b51-9550-fcbb155c6c9d)

Mietin, että tämäkin on vielä aika kamalan näköistä ilman erillistä ohjelmaa, joka osaisi rikkoa tiedon luettavaan muotoon. Yritin seuraavaksi onko foo.nmap selkokielistä luettavaa komennolla 'less foo.nmap'

![kuva](https://github.com/user-attachments/assets/d61cc767-9c89-48d1-9f2b-b997e16290ab)

Vastaukseksi sain miellyttävän raportin luettavaksi, jossa tärkeänä tietona on ylärivillä löytyneet Nmapin versio, skannauksen aika seskä ajettu komento.

Muuten tässä on enemmän tietoa, kuin Metasploitin tietokannassa. Suuri osa infosta on tiivistetty pois Metasploitin tietokannasta, mutta se on sitä kautta paljon nopeampi ja helppokäyttöisempi.
Siinä tulee myös eri avoimet portit esiin hostin kera, nmapin omassa tulosteessa nämä on itse hallittava esimerkiksi tiedostonimillä.

Metasploit DB hyödyt ja haitat:
+ Helppo katsoa montaa laitetta kerralla
+ Tärkeimmät infot, kuten palvelu ja versio näkyvät
+ Listaa hostit samalla

- Helppolukuisuuden varjolla tietoa jätetty näyttämättä

Nmapin tulosteen hyödyt ja haitat:
+ Usea eri tiedostomuoto, joista voi käsitellä dataa itselleen sopivalla tavalla
+ Tarkka tuloste, jossa näkyy kaikki skannauksen tiedot

- Vain yksi skannaus per tuloste, ellei yhdistele
- Hostit ja ajantasaisuus työläämpi tarkistaa.
























