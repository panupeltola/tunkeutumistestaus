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






