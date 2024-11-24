# Harjoitus 5 Täysin Laillinen Sertifikaatti

# Ympäristö

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

# x) Lue/katso ja tiivistä

*24.11.2024 klo 10:15*

## OWASP 2021: OWASP Top 10:2021

### A01:2021 – Broken Access Control (https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

- Puutteeliset tai virheelliset käyttäjäoikeudet olivat havaittavissa miltei 4 % tutkituista sovllluksista
- Johtuu usein siitä, että osalle sovellusta tai käyttäjälle on jätetty liian suuret oikeudet
- Toisaalta osaa komennoista voi hallita tavalla, mikä olisi tullut estää
- Puolustuksena paras pienimmän oikeuden periaate, eli oikeus annetaan vain siihen, mihin on pakko ja toimivat osat pilkotaan lohkoiksi
- API:t ja git hakemistot tulee suojata/piilottaa
- Suurin osa virheistä voitaisiin välttää helpoilla toimilla ja tarkkuudella, mutta suuriin kokonaisuuksiin jää helposti aukkoja

### A10:2021 – Server-Side Request Forgery (SSRF) (https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)

- Haavoittuvuus harvinainen, mutta vakava löytyessään
- Verkkosovellus hakee tietoa varmistamatta lähdeosoitteen oikeellisuutta.
- Voi ohittaa monet suojakerrokset ja on toimiessaan erittäin vakava
- Voi suojautua puhdistamalla ja vahvistamalla kaikki asiakkaan lähettämä data
- Ei lähetetä puhtaita vastauksia asiakkaalle
- Tässäkin pienimmän oikeuden ja "zero trust" ajatuksen voima vie pitkälle.
- Mikään muu kuin ennalta määritetyn tyyppinen liikenne, joka vahvistetaan välissä ei ole sallittua

## PortSwigget Academy

### Insecure direct object references (IDOR) (https://portswigger.net/web-security/access-control/idor)

- Hyökkäys, joka ohittaa tunnistuksen
- Liittyy yleensä oikeuksien nostamiseen, mutta käytetään myös sivuttaisliikkeeseen
- Toimii muuttamalla käyttäjän hallittavia parametrejä, joita ei ole suojattu ja jotka johtavat Back-end pyyntöihin ilman erillistä tunnistautumista

### Path traversal (https://portswigger.net/web-security/file-path-traversal)

- Hyökkäys, jossa päästään komennoilla siirtymään palvelimen kansiorakenteessa
- Joissain tapauksissa voidaan myös muokata palvelimen tiedostoja
- '../' siirtyy yhden kansion taaemmaksi Linuxilla, Windowsilla tulee käyttää '..\'
- Tehokkain tapa suojautua on puhdistaa ja tarkistaa asiakkaan syöte.

### Server-side template injection (https://portswigger.net/web-security/server-side-template-injection)

- Hyökkäys, jossa valmiisiin verkkopohjiin syötetään haitallista koodia.
- Hyökkäyksessä dataa ei syötetä suoraan sivulle vaan sen pohjaan.
- Valmiisiin hakupyyntöihin lisätään haittakoodia, joka saadaan ajettua palvelimen puolella.
- Helpoin tapa tunnistaa ajaako palvelin koodia on varmistaa saako siltä virheitä
- Hyökkäys tehdään osoitesivulta
- Väärän syntaksin lähettämisen vikakoodi kertoo monissa tapauksissa käytetyn pohjan
- Helpoin tapa estää on erottaa logiikka syötteestä

### Server-side request forgery (SSRF) (https://portswigger.net/web-security/ssrf)

- Hyökkäyksessä voidaan ajaa komentoja odottamattomissa sijainneissa palvelimella
- Esimerkissä pyydetään sovelluksena tietoa palvelimelta ja se palautetaan käyttäjälle virheellisesti
- Helpoin tapa puolustauta on suodattaa ja suojata käyttäjän komennot

### Cross-site scripting (https://portswigger.net/web-security/cross-site-scripting)

- Lähetetään haitallista JavaScript koodia muille käyttäjille
- Yksi hyökkäyksen tapa on lähettää koodi HTTP pyynnön muodossa
- Toinen tapa on syöttää haittakoodia esimerkiksi viestin tai käyttäjänimen muodossa, joka ajetaan muille käyttäjille heidän avatessaan sivun
- Kolmas hyökkäys on tilanne, jossa hyökkääjä saa haltuunsa täytettävän kentän.
- Helpoin tapa estää on suojata käyttäjän syöte

# a) Totally Legit Sertificate

Aloitin lataamalla OWASP ZAP paketin komennolla 'sudo apt-get -y install zaproxy'
Olin hakenut paketin aiemmin luennolla komennolla 'sudo apt-cache search zap' ja asentanut sen toiseen vituaalikoneeseeni, joten tiesin paketin olevan oikea.
Koetin vielä asennuksen onnistumista komennolla 'zaproxy'

![kuva](https://github.com/user-attachments/assets/881f0adf-68dc-4b02-9aa2-326ae5295074)

Oikean näköinen ikkuna aukesi.

Seuraavaksi katsoin asetukset kuntoon.
Avasin graaffisesta käyttöliittymästä polun Tools --> Options
Tämä avasi asetusruudun. Luennolla näytetyn ohjeen mukaan etsin hausta "image" ja etsin oikean kohdan "Display" osiosta ja klikkasin sen aktiiviseksi.

![kuva](https://github.com/user-attachments/assets/a21817ae-f740-4749-8a56-9b796e6075b7)

Seuraavaksi halusin testata verkkoliikenteen kaappaamista HTTP formaatissa. Tätä varten käynnistin Apache2 demonin komennolla 'sudo systemctl start apache2'
Tein myös tässä vaiheessa kohdan b) osan ja asensin virtuaalikoneelleni Firefoxin lisäosa pankista FoxyProxy proxypalvelimen.

![kuva](https://github.com/user-attachments/assets/d20ffe76-d4da-4bef-8def-a3b1cf9fc088)

Tein tämän, jotta saan localhost liikenteen reititettyä proxyn kautta.
Lisäsin proxyyn osoitteen 127.0.0.1 ja portin 8080. Portti oli automaattisesti määritetty ZAP:iin (näkyy kuvakaappauksen ikkunan laidassa)

![kuva](https://github.com/user-attachments/assets/83e701d3-1a42-4388-a7e7-512742d62dc8)

Laitoin proxyn päälle lisäosan asetuksista ja menin osoitteeseen "localhost"

![kuva](https://github.com/user-attachments/assets/9a299ff1-a0fa-40f4-8e91-d0f582fe3384)

Katsoin miltä liikenne näytti ZAP palvelussa.

![kuva](https://github.com/user-attachments/assets/96bd4ffe-d92e-453d-9a15-90072590121b)

Olin napannut liikennettä.

Seuraavaksi suljin Apache2 demonin komennolla 'sudo systemctl stop apache2'

Tämän jälkeen generoin ZAPissa uuden sertifikaatin.

Tein sen luennolla näytetysti graaffisen käyttöliittymän asetuksista kohdasta "Network" --> "Server Certificates"

Tallensin sieltä generoidun sertifikaatin ja katsoin sitä komennolla 'head zap_root_ca.cer'

![kuva](https://github.com/user-attachments/assets/db23de45-71a1-400d-92a2-c989975b1bf9)

Seuraavaksi menin firefoxin asetuksiin ja etsin kohdan View Certificates

![kuva](https://github.com/user-attachments/assets/16a9b7ec-bff8-47f0-a5d5-9764f7d36194)

Valitsin valikosta vaihtoehdon "Import"

Lisäsin halutun tiedoston

![kuva](https://github.com/user-attachments/assets/5ef640ed-ef46-4286-8e9d-7c4b472ffca9)

Suljin proxyn ja koetin ladata HTTPS liikennettä käyttävän sivun. Totesin, ettei liikenne vielä näkynyt ja oletin sen johtuvan siitä, ettei proxy ohjannut liikennettä.

Laitoin proxyn uudelleen päälle ja menin osoitteeseen "https://terokarvinen.com/tunkeutumistestaus/"

Tällä kertaa kaikki liikenne tallentui.

![kuva](https://github.com/user-attachments/assets/34884348-e968-47ca-86e4-05e762a8c197)

Otin proxyn pois päältä ja seuraavassa kohdassa määritin sille tarkemmat säännöt.

# b) Kettumaista

Olin jo edellisessä kohdassa asentanut FoxyProxyn ja saanut sen toimimaan.
Menin seuraavan tehtävän PortSwigger harjoitukseen ja katsoin, millainen verkon osoite siellä on, jotta saan sille osoitteen kuntoon.

Osoite päättyi web-security-academy.net
Lisäsin sen Proxyn ohjeeksi

![kuva](https://github.com/user-attachments/assets/da52b4e6-b4df-438c-9fcf-be2e2a6b3a54)

Laitoin FoxyProxystä tilan "Proxy by Patterns" päälle ja koetin tallentaako ZAP liikennettä.

![kuva](https://github.com/user-attachments/assets/ab9738ed-68ce-42db-bcd1-569f0d4fc621)

Ainakin tämä sivu kaappantui, URL on erittäin pitkä ja hämäävä.
Koetin vielä mennä osoitteeseen terokarvinen.com nähdäkseni, ettei se tähän kaappaannu.

Tätä ei tapahtunut. Päivitin sivun, mutta uusia tietueita ei tullut.

![kuva](https://github.com/user-attachments/assets/eacc37d3-4bf4-4d18-bf22-7bf1bb005c01)

Totesin siis Proxyn ja ZAPin toimivan oikein.

# c) PortSwigger Labs

## Path traversal

Käytin apuna PortSwiggerin ohjetta https://portswigger.net/web-security/file-path-traversal

Yritin kuitenkin mahdollisimman paljon tehdä itsenäisesti, kohdat joissa apua on pitänyt katsoa on merkattu erikseen.

### File path traversal, simple case

Aloitin ensimmäisenä tekemään Path traversal tehtäviä.
Harjoituksen tavoitteena oli päästä käsiksi /etc/passwd tiedostoon.

![kuva](https://github.com/user-attachments/assets/06790995-7e86-443b-837c-7a2d99bdc287)

Katsoin osoitteen muutosta url rivillä ja huomasin, että product?productId=* muuttui eri tuotteiden välillä.
Päätin yrittää PortSwiggerin ohjeessa ollutta ../ komentoa nähdäkseni pääsenkö liikkumaan heti oikeaan tiedostoon.
Yritin seuraavaa komentoa sivuksi

![kuva](https://github.com/user-attachments/assets/1af59027-b13e-4787-8011-b5ead49ed46f)

Tämä antoi minulle virheen

![kuva](https://github.com/user-attachments/assets/e27a1c2d-e181-4b1e-9963-278ca0ea6d09)

Päätin yrittää lisätä komennon kohtaan productId=

![kuva](https://github.com/user-attachments/assets/e11c9d80-1d36-498d-8ea8-f4cd84f111a5)

Tämä antoi erilaisen virheen.

Päätin katsoa kaapattua dataa ZAPin kautta

![kuva](https://github.com/user-attachments/assets/b5cdb157-6c83-45b5-8e09-1d9fd7192621)

Huomasin, että GET pyynnössä pyydetään aivan eri tavalla tiedostoa. Tässä kuvaa haetaan filename=15.jpg

Yritin muokata tätä kenttää samalla tapaa kuin aiemmin.

![kuva](https://github.com/user-attachments/assets/56d45822-48ef-433c-b13d-327d7409ceb7)

Eri virhe, eli "filename=" on pakollinen kenttä.

Yritin muokata 15.jpg osaa

![kuva](https://github.com/user-attachments/assets/b9eb972e-cbc9-4bbc-8bbf-9ae57a192096)

Vaihdoin vielä Response Headerin kuvasta tekstiin ja näin, että olin saanut salasanat näkyviin.

Kohteessa hyökättiin suojaamattoman hakukentän kimppuun, missä kuvan lähdettä muuttamalla pystyi hakemaan myös muita palvelimen tiedostoja.

Sain sivulta tiedon, että olin ratkaissut ongelman:

![kuva](https://github.com/user-attachments/assets/778a7d64-0b13-4023-9310-e60b72c06169)

### File path traversal, traversal sequences blocked with absolute path bypass

Harjoituksen tavoitteena oli saada /etc/passwd tiedosto

Aloitin samalla tapaa kuin edellisessä.

Huomasin, että kohteessa kutsuttiin samalla tapaa kuvaa kuin edellisessä "filename=?"

Yritin samaa kuin aiemmassa ja yritin siirtyä taaemmaksi tiedostopolussa.

![kuva](https://github.com/user-attachments/assets/783383bf-1f6f-45c2-abf3-56c40e941846)

Sain vastaukseksi "Bad Request" "No such file"

Muistin ohjetta lukiessani, että joissain tapauksissa suoralla tiedostopolulla voi myös saada tiedoston. Yritin sitä tässä välissä.

![kuva](https://github.com/user-attachments/assets/6e89e40f-2925-41ea-8299-9480eacb78a5)

Olin sisällä. Tässä tapauksessa tiedostopolkua kulkeminen oli estetty, mutta suora tiedostopolku toimi silti.
Harjoituksen nimi antoi kyllä melkoisen vinkin.

![kuva](https://github.com/user-attachments/assets/68f01b7a-73cb-4d38-b907-b286906fe1f6)


### File path traversal, traversal sequences stripped non-recursively

Tehtävässä oli sama tavoite.

Tehtävän annossa ilmoitettiin, että käyttäjän syötteestä riisutaan siirtymiseen käytettävät komennot, joten oletin että komento pitää piilottaa siivouksen sisään.
Muistan lukeneeni aiheesta, mutten muista miten tämä tehtiin. Päätin ennen sitä kuitenkin etsiä taas missä etsittävä tiedostopolku menee.

![kuva](https://github.com/user-attachments/assets/2974941e-8b6e-4c99-b8ad-0d28b5541186)

Jälleen kerran virhe oli kuvassa.

Luin tässä vaiheessa ohjeesta, miten siirtyminen voitaisiin piilottaa.
Ohjeen mukaan näitä voi piilottaa esimerkiksi tavalla ....//, jolloin sisemmät tiedot ilmeisesti katoavat. Päätin lähteä kokeilemaan.

Yritin monia tapoja, mutta sain aina vastaukseksi "No such file". Yritin hakea lisää, kunnes löysin artikkelista (https://kadalonsecurity.medium.com/using-nested-traversal-sequences-to-bypass-file-path-traversal-defense-3982feb4e60b) logiikan, jolla nämä "nested" komennot toimivat. Ilmeisesti ne tulee laittaa jokaisen '../' komennon tilalle. Omat yritykseni perustuivat, että suodatin ei olisi osunut kaikkiin.
Yrtin mm. polkua 'filename=..../../../etc/passwd/'
Päätin seuraavaksi yrittää polkua filename=....//....//....//etc/passwd

![kuva](https://github.com/user-attachments/assets/4bea1cdc-21b2-4c60-8f82-85fb7482b554)

Olin sisällä.
Nyt ymmärrän syntaksin, miten nämä nested komennot toimivat. Eli hiukan kuin Tetriksessä, sisältä löytyneet kielletyt merkkiyhdistelmät poistettiin, mutta poiston jälkeen uudet merkkijonot yhdistyivät uudelleen muotoon '../' eikä suodatin tarkistanut tätä.

Tässä raktaisussa siis vain ajettiin komento, jossa pyydettiin palvelinta hakemaan tiedosto osoitteesta ../../../etc/passwd. Eli sama ratkaisu, kuin ensimmäisessä Path Traversal tehtävässä, mutta syntaksi oli eri suodattimen takia.






























































