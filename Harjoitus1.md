# Harjoitus 1

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










