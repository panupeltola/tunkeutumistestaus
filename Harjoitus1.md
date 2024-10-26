# Harjoitus 1

# Ympäristö:

## Fyysinen ympäristö:
- Intel i7-10700K @ 3,80GHz (AMD64)
- Nvidia RTX 3080
- 32 Gt RAM
- Windows 11 Education 23H2

## Virtuaaliympäristö (Kali)
- Oracle VirtualBox
- 8 Prosessoriydintä
- 16 Gt RAM
- Kali Linux 2024.3

# x)

## Darknet Diaries jakso 146 ANOM

- Käytiin läpi ANOM puhelinta
- Yksityisyyteen keskittyvä puhelin, joka oli ns. rikollisilta rikollisille puhelin
- Päästä päähän salatussa liikenteessä ongelmana, että kaikki liikkui myös FBI:n palvelimille
- Todellisuudessa honeypot ja jokaista puhelinta seurattiin kansainvälisessä yhteistyössä
- FBI ei saanut kuunnella oman maan asukkaita, joten ainakin teoriassa seurattiin vain ulkomaalaisia keskusteluja
- Nosti esiin hyviä pointteja esimerkiksi miten rikollisuus, jota saadaan seurata määritetään
- Hyvänä pointtina on myös, miten digitaaliseen liikenteeseen liittyvää yksityisyyttä kunnioitetaan suhteessa vähemmän kuin fyysistä yksityisyyttä
- Mobiililaitteita voidaan tutkia satunnaisemmin kuin koteja
- Nosti myös kysymyksen millainen valmius vakoiluun jää valtioilla, vaikka operaatio on loppunut

##  Intrusion Kill Chain

- Systemaattinen prosessi tunnistaa haluttu kohde ja saavuttaa halutut vaikutukset
- Samat askeleet vaaditaan halutun vaikutuksen saamiseen
- Askeleet lyhyesti:

1. Tarkkailu --> Kohteen tutkiminen ja hyökkäykseen vaadittavien tietojen kerääminen kohteesta
2. Aseistaminen --> Hyökkäävän ohjelmiston kiinnittäminen hyötykuormaan
3. Toimittaminen --> Haittapaketin siirtäminen kohteeseen
4. Hyväksikäyttö --> Haavoittuvuuden hyväksikäyttäminen kohteeseen tunkeutumista varten
5. Asentaminen --> Haitan tai takaoven asentaminen kohteeseen
6. Käsky ja hallinta --> Etähallittavan yhteyden mahdollistaminen kohteeseen
7. Tavoitteisiin johtavat toimet --> Tavoitteiden toteuttaminen, kuten tiedon varastaminen

## The Art of Hacking

4.1

- Aktiivisessa tarkailussa lähetetään paketteja kohteeseen
- Esimerkkinä porttiskannaus
- Tiedustelu alkaa olemaan "äänekkäämpää" tai tunnistettavaa

4.2

- Ennakkoon selvittäminen on tärkeää, jotta selviää mihin laitteisiin kannattaa hyökätä
- Verkon rakenne on tärkeää ymmärtää ennen haavoittuvuuksien selvittämistä
- Tämä on "äänekästä" tutkimista

4.3

- Nmap on paras porttiskanneri
- Pitkäaikainen työkalu, jota on tutkittu paljon
- Masscan on nopein verkkoskanneri, saman tyyppinen kuin nmap
- Nopeampi, muttei yhtä monipuolinen
- Skannerit tarkkailevat yleensä TCP portteja
- nmapin '-Pn' komento ei ohita porttia, vaikka se näkyisi epäaktiivisena porttiskannausken aikana
- UDP skannaus antaa myös paljon tietoa kohteesta
- EyeWitness näyttää nopeasti monesta sivusta koosteen
- Helpottaa porttiskannauksessa löydettyjen tulosten analysointia

4.4

- Erillisiä verkon ja verkkosovelluksien haavoittuvuusskannereita
- Nmap kykyenee myös haavoittuvuusskannauksiin



# a) Asenna Kali

26.10.2024 14:18

Latasin Kalin uusimman version osoitteesta kali.org. Latasin 64 bittisen version Kali 2024.3 jakelusta. Päätin ladata ISO-tiedoston ja asentaa koko version, sillä siinä sain helposti määritettyä myös käyttäjän ja salasanan.
Loin uuden virtuaalikoneen Oraclen VirtualBoxiin ja annoin koneelle 16 gigatavua muistia, riittävästi prosessorin ytimiä, sekä 50 gigaa kovalevytilaa.
Asensin tämän jälkeen käyttöjärjestelmän asennuskuvan graaffista käyttöliittymää käyttäen.
Asennuksen jälkeen päivitin järjestelmän komennoilla 'sudo apt-get update', 'sudo apt-get upgrade' ja 'sudo apt-get dist-upgrade'
Päivityksien jälkeen asensin koneelle vielä palomuurin komennolla 'sudo apt-get -y install ufw' ja 'sudo ufw enable'
Koetin vielä, että virtuaalikone on verkossa avaamalla googlen

![kuva](https://github.com/user-attachments/assets/ee607131-4c6a-4b48-b3bb-d544dd39e95c)

Yhteys saatiin ja totesin asennuksen onnistuneen.


# b) Irrota Kali-virtuaalikone verkosta

26.10.2024 14:27

Aiemmin käyttämäni tapa irroittaa Kali verkosta on säätää VirtualBoxin asetuksista verkkokortin asetuksia, joista kytken internettiin yhteydessä olevan verkkokortin pois päältä.

![kuva](https://github.com/user-attachments/assets/27fe1ade-741b-46b6-80c2-b2e4d4f8283e)

Yhteydessä oleva verkkokortti, verkkoyhteys todettu a) kohdassa toimivaksi.

![kuva](https://github.com/user-attachments/assets/24a50b11-1ae4-41a6-98a5-bb85d03c5136)

Tilaksi valittu "Not attached"
Näin verkkokortin pitäisi olla poissa verkosta.

Koetin komentoa 'ping 8.8.8.8'

![kuva](https://github.com/user-attachments/assets/93f6db76-6014-4fd5-9ede-b465f83867e9)

Tämä kuitenkin vastasi vielä kutsuun. Hetken ihmeteltyäni totesin, etten ollut hyväksynyt muutosta VirtualBoxin asetuksissa.
Valitsin asetuksissa "OK" ja yritin komentoa uudelleen.

![kuva](https://github.com/user-attachments/assets/a68c680c-6ae2-4247-acfd-02aacd183dcc)

Tällä kertaa osoite 8.8.8.8 ei enää vastannut ja myös Kali huomautti, ettei ole enää verkossa.
Totesin tämän olevan tapa, jolla verkkokortin asetuksia voi muokata lennosta. Erityisen tärkeäksi tuli kuitenkin huomata hyväksyä muutokset.
Verkon tila tulee siis aina testata ennen kuin tekee mitään potentiaalisesti verkkoon karkaavaa asiaa.

# c) Porttiskannaus

Skannasin yleisimmät portit komennolla 'nmap -A localhost'

![kuva](https://github.com/user-attachments/assets/18f34a1d-b7e9-4c03-bf75-74d51870af1e)

Vastauksena sain, että kaikki portit ovat kiinni. Tämä on sinänsä oletettava vastaus, sillä virtuaalikone ei ole verkossa, eikä siinä ole yhtään demonia ajossa. Skannauksen tulos kertoo siitä, ettei mikään laitteen palvelu kuuntele porttia.

# d) Demonien porttiskannaus

Aloitin palauttamalla koneen verkkoon, jotta saan demonit asennettua. Tein sen samalla tapaa, kuin kohdassa b), mutta palautin verkkokortin tilaksi NAT.

Asensin demonit Apache2 ja Open-SSH server komennolla 'sudo apt-get -y install apache2 openssh-server'

![kuva](https://github.com/user-attachments/assets/a158cfc9-e713-48d5-b4ed-0f9bcc997ad1)

Olin ne jo näköjään käyttöjärjestelmän asennuksen yhteydessä asentanut.
Seuraavaksi ne tuli käynnistää, koska niitä ei edellisellä porttiskannauksella näkynyt. Tein sen komennolla 'sudo systemctl start apache2 ssh'
Tämän jälkeen suljin taas verkon, testasin sen olevan poissa pingaamalla osoitetta 8.8.8.8 ja ajoin komennon 'nmap -A localhost'

![kuva](https://github.com/user-attachments/assets/4bdefc89-43ad-453d-9b90-6d0e9d3cb701)

Tällä kertaa näkyy, että kaksi porttia on avoimena. Ne ovat 22 eli ssh ja 80 eli HTTP jota käyttää Apache.
Mielenkiintoista tässä on, että tällä kertaa kun portteja oli auki, 'nmap' komento näytti myös enemmän tietoa itse laitteesta. Tällä kertaa näkyi esimerkiksi käyttöjärjestelmään liittyvää tietoa ja myös laitteen tyyppi ja verkon etäisyys.
Tässä vaiheessa muistin myös, että luennolla suositeltiin ajamaan 'nmap' pääkäyttäjän oikeuksilla. Koetin vielä muuttaako tämä tulosta.

Nopealla silmäyksellä tiedot olivat tismalleen samat.

![kuva](https://github.com/user-attachments/assets/5da213b1-3de9-464a-bd9b-59ac9b3d6e55)

Testattuani nämä asiat suljin demonit komennolla 'sudo systemctl stop apache2 ssh'

# e) Asenna Metasploitable 2

Käytin tässä ja seuraavassa kohdassa ohjeena Tuomas Valkamon raporttia samasta aiheesta. Pyrin kuitenkin tekemään työn mahdollisimman pitkälle itse ja turvautumaan raporttiin vain jos en millään pääse eteenpäin.
Latasin Metasploitable 2 virtuaalikoneen osoitteesta https://sourceforge.net/projects/metasploitable/, otin linkin ohjeesta koska tällä hakusanalla löytyi erittäin paljon muutakin googlesta.
Ladattuani paketin purin sen.

Tässä vaiheessa tuli pieni miettimishetki, koska en ollut ennen asentanut valmista konetta muusta kuin valmiista tuetusta snapshotista.
Löysin kuitenkin artikkelin aiheesta, josta tajusin, että yrittämäni tavat lisätä konetta VirtualBoxin "Add" painikkeesta tai uuden koneen luomisen yhteydessä ISO-kuvan etsiminen olivat vääriä tapoja.
(https://www.geeksforgeeks.org/how-to-install-metasploitable-2-in-virtualbox/)
Kone piti jättää ilman ISO kuvaa ja klikkailla eteenpäin.

![kuva](https://github.com/user-attachments/assets/804b396c-a2af-401b-8ccd-b8450395269c)

Valitsin koneelle pienet resurssit ja kovalevyn valinnassa valitsin "Use Existing Virtual Hard Disk File"

Hain paketin mukana tulleen kovalevyn ja valitsin sen koneen virtuaalikovalevyksi.

![kuva](https://github.com/user-attachments/assets/05a69e7c-f77b-451b-a084-acd9a4a58b1d)

Koetin vielä käynnistää koneen nähdäkseni toimiiko se.

![kuva](https://github.com/user-attachments/assets/79c7c9fe-9e92-4bb7-9b5a-616bf73de08c)

Kone lähti käyntiin.

![kuva](https://github.com/user-attachments/assets/b93084c9-a694-43a0-8552-8049abd1f441)

Myös kirjautuminen onnistui tunnuksilla msfadmin:msfadmin.

# f) Tee koneiden välille virtuaaliverkko

Aloitin verkon määrittämisen katsomalla miten verkon voisi luoda. Totesin, että Kali kone ainakin tarvitsee kaksi verkkokorttia, sillä toisen niistä on oltava verkkoon yhteydessä ja se on tarvittaessa laitettava pois päältä.

![kuva](https://github.com/user-attachments/assets/2dbc2fa1-c790-4d27-b6d6-3819deefa707)

Katsoin VirtualBoxin dokumentaatiosta sopivaa asetusta ja näistä Host-only ja Internal näyttäisivät olevan täysin sisäiseen keskittyviä, joten valitsin näistä Host-only verkon.


Menin KALI virtuaalikoneen asetuksissa "Network" osioon ja valitsin Adapter 2, laitoin sen päälle ja valitsin sen tilaksi Host-only. 
Metasploitableen laitoin verkkokortin sisäiseksi asetukseksi Host-Only.

Valitsin molemmille laitteille saman ethernet adapterin.

Käynnistin molemmat koneet.

Etsin ensin Metasploitable koneen IP-osoitteen komennolla ip addr

![kuva](https://github.com/user-attachments/assets/4e1ad629-9ae8-4ff0-8175-5d672ee14062)

Sain sieltä näkymään, että se on 192.168.56.103.
Seuraavaksi yritin katsoa miten käy jos ajan komennon 'ping 192.168.56.103' Kali virtuaalikoneeltani.
Mikäli osoite vastaa, olisi näiden virtuaalikoneiden välillä yhteys.
Koetin vielä ennen, että en ole yleiseen verkkoon yhteydessä komennolla 'ping 8.8.8.8'

![kuva](https://github.com/user-attachments/assets/4b15092d-3ead-4889-9e7d-a09e59f5b209)

En ollut julkisessa verkossa, yritin pingata metasploitablea.

![kuva](https://github.com/user-attachments/assets/084058dc-8154-48d8-b0a4-e56c333482e4)

Sain vastauksen, eli yhteys toimi, vaikka Kali ei ollut verkossa.

Koetin vielä saanko Metasploitablelta pingattua Kalia.

![kuva](https://github.com/user-attachments/assets/997f28fb-7371-4fc0-b3b3-278af2d1a085)

Verkko toimii molemmin päin.

# g) Tarkista, mitä löysit

Ajoin ohjeen mukaisen komennon 'nmap -sn'

Luin nmapin manuaalista ensin mitä tämä komento tekee:
-sn tekee ping scannin ja ohittaa porttiskannauksen

![kuva](https://github.com/user-attachments/assets/60e52191-e2a2-4ded-8b9b-827ff695131b)


Ajoin komennon enkä saanut vastausta. Olin ylipäätään tässä vaiheessa vähän hämilläni, mitä tehtävässä oli tarpeen tehdä, koska olin jo osoitteen löytänyt.
Otin kuitenkin asenteekseni, että selvitän miten löydän sen mikäli en osoitetta tietäisi ja oletuksena olisi vain, että se on samassa verkossa kuin Kali.
Löysin aiheeseen liittyvän artikkelin Redhatin foorumeilta https://www.redhat.com/en/blog/quick-nmap-inventory
Tästä tajusin, että voin käyttää verkon osoitetta, jonka kartoitan.
Päätin siis katsoa ensin, mikä on Kalin verkon osoite komennolla 'ifconfig'

![kuva](https://github.com/user-attachments/assets/6795f989-3bc0-40f0-b232-426175860694)

Koska laitteen IP-osoite oli 192.168.56.104 ja verkkomaski 255.255.255.0 pystyin päätelemään verkon osoitteen olevan 192.168.56.0/24.
Yritin siis skannata verkon komennolla 'nmap -sn 192.168.56.0/24'

![kuva](https://github.com/user-attachments/assets/bfd934ec-9599-4164-a032-94d5dc16c98d)

Tulokseksi sain, että IP-osoite toisella VirtualBoxin laitteella olisi 192.168.56.103.
Kokeilin vielä onko osoite oikea kirjoittamalla sen selaimeen.

![kuva](https://github.com/user-attachments/assets/89e702a3-9312-45b9-8bbb-e0dc43f99ecd)

Vastauksena sain Metasploitablen verkkosivun ja totesin haun onnistuneeksi.

# h) Porttiskannaa Metasploitable

26.10.2024 klo 15:58

Varmistin vielä kerra, ettei kali ollut ihmeen kaupalla mennyt välissä verkkoon.

Nyt kun IP-osoite oli tiedossa, aloitin metasploitablen porttiskannauksen komennolla 'sudo nmap -A -p- 192.168.56.103'

Sain vastaukseksi melkoisen määrän auki olevia portteja.

## FTP

Ensimmäisenä silmääni iski auki oleva portti 21, johon oli yhteydessä ftp eli File Transfer Protocol.
Löytämäni artikkelin (https://www.digitalguardian.com/blog/what-ftp-security-securing-ftp-usage) mukaan ftp on vanhentunut tiedoston siirto teknologia, jonka kirjautuminen tapahtuu selkokielisenä ja sen liikenne on salaamatonta.
Siten sitä vastaan voidaan kohdistaa esimerkiksi kuuntelua, spooffausta tai brute force hyökkäyksiä.

![kuva](https://github.com/user-attachments/assets/6b603b70-5220-4cc8-a2db-030777b6b984)

## MySQL

Toisena silmääni iski mysql portissa 3306

![kuva](https://github.com/user-attachments/assets/f31298b7-4de2-4d96-bfe3-acdce6a47a67)

Ensinnäkin tietokannan portin auki oleminen suoraan verkkoon on riski. Tietokannan versio on oletettavasi vanha ja siinä on mahdollisia haavoittuvuuksia.
Enemmän minua kuitenkin kiinnostivat rivit Status:autocommit ja Salt.
MySQL dokumentaatio (https://dev.mysql.com/doc/refman/8.4/en/commit.html) määrittää tilan autocommit siten, että siinä tilassa tehtyjä muutoksia ei voi enää perua, eli jos tietokantaan saataisiin tehtyä muutos, tietoa ei voisi palauttaa ilman varmuuskopiota ja muutos tulisi heti voimaan.
Saltista mietin, onko tässä kaikille näkyvä salasanojen salt arvo, jonka kaikille näkyminen helpottaisi huomattavasti salasanan tiivisteen murtamista.

## Telnet 

Viimeisenä katsoin vielä porttia 23, eli Telnet.

![kuva](https://github.com/user-attachments/assets/5732fea6-1261-4d14-856a-e9c94edb9971)


Telnet on vanhentunut etäkäyttöprotokolla, josta puuttuu SSH:n mukaiset suojaukset, kuten avainparitunnistus, päästä päähän salattu liikenne ja kirjautumisen brute force estot.
Tämän portin kautta voisi saada siis koneeseen etähallinnan. (https://web.synametrics.com/risks-running-telnet.htm)


# Lähteet

1. T. Karvinen, 2024, Tunkeutumistestaus, https://terokarvinen.com/tunkeutumistestaus/, Luettu 26.10.2024
2. T. Valkamo, 2022, Hacking into a Target Using Metasploit, https://tuomasvalkamo.com/PenTestCourse/week-2/, Luettu 26.10.2024
3. Geeks for Geeks, 2022, How to install Metasploitable 2 in VirtualBox, https://www.geeksforgeeks.org/how-to-install-metasploitable-2-in-virtualbox/, Luettu 26.10.2024
4. 'man nmap'
5. V. Bajarami, 2020, Running a quick NMAP scan to inventory my network, https://www.redhat.com/en/blog/quick-nmap-inventory, Luettu 26.10.2024
6. N. Lord, 2024, What is FTP Security? Securing FTP Usage, https://www.digitalguardian.com/blog/what-ftp-security-securing-ftp-usage, Luettu 26.10.2024
7. Oracle, 2024, 15.3.1 START TRANSACTION, COMMIT, and ROLLBACK Statements, https://dev.mysql.com/doc/refman/8.4/en/commit.html, luettu 26.10.2024
8. Synametrics Technologies, Security risks involved with running Telnet client and server, https://web.synametrics.com/risks-running-telnet.htm, luettu 26.10.2024
9. J. Rhysider ja J. Cox, 2024, Darknet Diaries EP 146: ANOM, https://darknetdiaries.com/episode/146/, Kuunneltu 26.10.2024
10. Hutchins et al 2011: Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains, chapters Abstract, 3.2 Intrusion Kill Chain
11. Santos et al: The Art of Hacking (Video Collection): 4.3 Surveying Essential Tools for Active Reconnaissance    
