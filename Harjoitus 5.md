# Harjoitus 5 Täysin Laillinen Sertifikaatti

Harjoitukset osana T. Karvisen kurssia Tunkeutumistestaus (https://terokarvinen.com/tunkeutumistestaus/)

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

### File path traversal, simple case (https://portswigger.net/web-security/file-path-traversal/lab-simple)

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

### File path traversal, traversal sequences blocked with absolute path bypass (https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)

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


### File path traversal, traversal sequences stripped non-recursively (https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)

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

## Insecure Direct Object Reference (IDOR)

### Insecure direct object references (https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)

Tehtävässä oli annettu vinkiksi, että järjestelmä tallentaa chat logit suoraan palvelimen tallennustilaan ja hakee ne käyttäen staattisia URL osoitteita.
Tehtävän tavoitteena oli löytää käyttäjän carlos salasana ja kirjautua hänen käyttäjälleen.

Avattuani sivut, ensimmäisenä huomioni kiinnitti "Live chat" nappi.

Päätin seurata mitä sieltä löytyy, sillä tehtävänanto viittasi tähän kovasti.

Lähetin sivulta viestin ja painoin "View transcript"

Tämä latasi minulle uuden tiedoston.

Halusin nähdä, miltä tämä näytti ZAPissa

![kuva](https://github.com/user-attachments/assets/6127b7e5-0dda-48ed-89b9-69e710332477)

Näin, että olin tehnyt GET pyynnön tiedostosta 2.txt.
Mietin, miten tapahtuisi jos tekisinkin GET pyynnön 1.txt tiedostosta.

![kuva](https://github.com/user-attachments/assets/d6058fb2-a975-42f4-82e7-f550f44f0ce0)

Huomasin, että vastauskentässä oli uutta tekstiä. Etsin sieltä tietoa halutusta salasanasta.

![kuva](https://github.com/user-attachments/assets/0616c490-d5f4-41a6-8bd2-d7900ea35db0)

Sieltä löytyi.

Seuraavaksi yritin käyttää salasanaa kirjautuakseni käyttäjän carlos tilille.

![kuva](https://github.com/user-attachments/assets/c4e3d1b9-d3be-4b06-871e-c19872d63303)

Olin sisällä.

Harjoituksessa käytettiin huonosti toteutettuja staattisia linkkejä tietojen hakemiseen, joiden arvaaminen oli kovin helppoa ja tiedosto latautui ilman kirjautumista tai muuta vahvistusta vain näppäintä painamalla.
Harjoituksessa ei selvitetty miten käyttäjätunnus olisi selvinnyt. Oletettavasti vain arvaamalla tai tietämällä etukäteen.

## Server Side Request Forgery (SSRF)

### Basic SSRF against the local server (https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)

Harjoituksessa on tarkoituksena muuttaa tarkastettu URL kohteeseen http://localhost/admin ja poistaa käyttäjä carlos.

Aloitan avaamalla eri sivuja nähdäkseni saisinko niistä jotain apua, avaan osoitteen "My account" ja yhden tuotesivun

![kuva](https://github.com/user-attachments/assets/a824276d-0137-4ef6-8a5b-c1b9f3cf3817)

My account sisältää kirjautumisruudun

![kuva](https://github.com/user-attachments/assets/bfa0c31c-1cb9-4c3f-844a-e2967f787794)

Tuotesivu sisältää saatavuuden tarkistuksen. Klikkaan tätä, koska aiemmin luettuna opin, että SSRF tehdään usein POST pyyntöjen yhteydessä.

![kuva](https://github.com/user-attachments/assets/256f68ad-c2e7-413a-9917-2716e4324a93)

Totisesti näen API pyynnön. Yritän tehtävänannon ohjeen mukaisesti muokatata tähän http://localhost/admin

Sain vastauksena uuden sivun Response kenttään. Kelasin sen läpi ja näin tekstiä, jonka oletin olevan API kutsu käyttäjän 'carlos' poistamiselle

![kuva](https://github.com/user-attachments/assets/f0904630-b3f4-41a2-96a0-745f9dad04f8)

Ajoin komennon.

![kuva](https://github.com/user-attachments/assets/b140540e-5afb-468a-9ba5-fb284646f292)

Ajaessani API kutsun 'stockApi=http://localhost/admin/' uudelleen, näin alla kuvassa olevan tekstin, eikä käyttäjää carlos enää näkynyt.

![kuva](https://github.com/user-attachments/assets/f0399f5b-874e-4ca8-acaa-d7a54c62ff7a)

Carlos oli totisesti poissa.

![kuva](https://github.com/user-attachments/assets/cbcb7974-a990-4c6e-834e-2e52f904c631)

Tehtävässä käytettiin suodattamatonta API kutsua, jolla saatiin lisätietona ensin lisää komentoja ja niitä käyttämällä saatiin poistettua käyttäjä carlos ilman tunnistautumista tai salasanaa.

## Cross Site Scripting (XSS)

### Reflected XSS into HTML context with nothing encoded (https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)

Käytin harjoituksen tukena artikkelia (https://portswigger.net/web-security/cross-site-scripting/reflected)

Harjoituksen tehtävänä on ajaa 'alert' funktio käyttäen cross-site scriptingiä.

Ensimmäisenä haluan nähdä miten tämä funktio sivulla normaalisti ajetaan.

Sivulla oli etusivulla hakukenttä.

![kuva](https://github.com/user-attachments/assets/aa7dee20-4afe-449b-a038-1ad46cafd32d)

En kuitenkaan saanut siinä virhettä aiheutettua ja haku toimi vaikkei kirjaimet olisi oikein.

![kuva](https://github.com/user-attachments/assets/63dda501-439f-41d6-880c-b87c55cee0f1)

Yritin seuraavaksi katsoa blogikirjoituksen sisään.

Siellä oli kommenttikenttä.

Yritin kirjoittaa kommenttia.

![kuva](https://github.com/user-attachments/assets/ab1b4588-0648-4503-b1c2-8a86c438f870)

Tässä sain virheitä väärin formatoidusta tekstistä, mutta se ei näkynyt ZAPissa

En saanut kommenttia kirjoitettua.

Tässä vaiheessa alkoi oma osaaminen loppua kesken.

![kuva](https://github.com/user-attachments/assets/1b167de4-01a7-43a9-a52c-4d0578ce3bd3)

Katsoin vielä hakutoimintoa, koska se oli ainut tapa, että olin saanut syötettä vietyä eteenpäin.

Yritin tutkia onko tehtävänannossa annettu alert komento jokin yleinen JavaScript komento, sillä se ei ole itselleni tuttu.

Selvisi, että se on (https://www.w3schools.com/jsreF/met_win_alert.asp)

Yritin siis sisällyttää tätä hakukenttään PortSwiggerin ohjeen mukaisesi '<script> alert("Apua !") </script>'

Kirjoitin komennon hakukenttään ja sain virheikkunan ponnahtamaan

![kuva](https://github.com/user-attachments/assets/488271e2-0bff-4ea3-b91d-0f82b37f5288)

Harjoituksessa ajettiin suodattamatonta syötettä hakukentästä, joka mahdollisti JavaScript komentojen ajamisen.

![kuva](https://github.com/user-attachments/assets/a87fb98b-c364-4bbb-8cfa-1c1d43c37d57)

## Stored XSS into HTML context with nothing encoded (https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)

Harjoituksen tarkoituksena oli ajaa 'alert' komento käyttäen kommenttifunktionaalisuutta.

Sivu näytti samalta, kuin aiemmin tosin ilman hakukenttää.
Muisitin edellisestä tehtävästä, että blogikirjoituksissa on kommenttikenttä, joten päätin yrittää saada kommentin sinne.

![kuva](https://github.com/user-attachments/assets/4a127b0f-0c74-43f3-9f00-bc4d92b984be)

Jätin kommentin ja samalla läpipääsymerkinnän ja hyväksymisen kommentista. En ollut itse kuitenkaan vielä nähnyt funktiota, joten palasin blogiin.
Huomiona alla olevaan kuvaan, että kommentista piti poistaa verkko-osoite, jotta kommentin sai tallennettua.

![kuva](https://github.com/user-attachments/assets/c2f60757-6987-4adc-a02a-e4c9680d4a9f)

Blogin avatessa sain funktion toimimaan.

![kuva](https://github.com/user-attachments/assets/d66cc819-15b4-413b-bb7c-ac3c8e267e45)

Harjoituksessa jätettiin kommentteihin JavaScript komento, joka ajettiin sivun lataamisen yhteydessä.

## Server Side Template Injection (SSTI)

### Server-side template injection with information disclosure via user-supplied objects (https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-with-information-disclosure-via-user-supplied-objects)

Käytän harjoituksen tukena PortSwiggerin ohjetta (https://portswigger.net/web-security/server-side-template-injection)

Harjoituksen tarkoituksena on varastaa frameworkin salainen avain.

Harjoituksen mukana on annettu kirjautumistiedot.

Aloitan kirjautumalla sisään, koska epäilen hyökkäyksen liittyvän tähän.

![kuva](https://github.com/user-attachments/assets/2b1ee8f2-6ae8-4991-a4bb-8cc5a7b4581f)

Sain POST pyynnön ja epäilen hyökkäyksen liittyvän tähän.

Tässä vaiheessa kuitenkaan en keksi mitään ja käännyn ohjeen puoleen.
Yritin ohjeen mukaisesti ajaa osoitetta 'https://0a3000b20339a53180eecb2100c70030.web-security-academy.net/my-account?id=${7*7}'
Tämä kuitenkin heitti minut takaisin kirjautumissivulle.

![kuva](https://github.com/user-attachments/assets/094bc4af-ce90-4c31-ad2d-e68d9ad4f962)

Komennot eivät toimineet myöskään productID osiossa.

Olin jumissa.

Community solutionsissa on usein videoita, joten päätin katsoa videota (https://www.youtube.com/watch?v=8o5QPU-BvFQ) ainakin siihen asti, että ymmärtäisin miten hyökkäys kohdetta voisi edes tutkia.

Videossa näytettiin, että sisäänkirjautumisen jälkeen esineille on tullut uusi osio "Edit template"
Jätin videon sikseen tältä erää ja yritin ratkaista ongelmaa.

![kuva](https://github.com/user-attachments/assets/2ea5f24c-0720-47bb-ab12-90f54392e61c)

Tässä silmääni pisti lopussa olevat kentät.

![kuva](https://github.com/user-attachments/assets/0c0a51bd-da2f-4c5b-a496-12efad513648)

Ne hakevat tietoa, joten oletan voivani muokata näitä. Yritän PortSwiggerin ohjetta 7*7

![kuva](https://github.com/user-attachments/assets/0ad76d27-9cad-4132-9fb2-cfd48f902b4f)

En saanut tätä toimimaan, mutta ainakin näen nyt, että kyseessä on Django framework virheilmoituksesta.

![kuva](https://github.com/user-attachments/assets/81689433-9e6a-4674-9429-2fc5fb5edf81)

Yritin muokata kutsuun ensimmäiseen kenttään toisen kerran product.price. Tämä muutti tiedon myös preview ikkunaan. Nyt minun pitäisi vain keksiä mikä oikea arvo on.
En keksinyt taas ratkaisua vaikka yritin googlata tapoja löytää avaimia, käyttäjätunnuksia ja salasanoja. Näistä mikään ei toiminut. Tiesin tarvitsevani käytännössä yhden komennon.

Olin löytänyt jo frameworkin, joten päätin vielä yrittää hakea googlesta "Django SSTI"

Sain ensimmäisenä GitHub linkin aiheesta (https://github.com/Lifars/davdts)

Sieltä löysin, että kentän {{ messages.storages.0.signer.key }} tulisi näyttää avain.

Se ei näyttänyt mitään.

Seuraavaksi yritin sivun näyttämää komentoa '{% load log %}{% get_admin_log 10 as log %}{% for e in log %} {{e.user.get_username}} : {{e.user.password}}{% endfor %}' Tämän tulisi näyttää admin käyttäjän tunnus ja salasana.

![kuva](https://github.com/user-attachments/assets/96377e81-8fbb-4bf7-955d-210ee52b92b2)

Sain error viestin, mutta tämä komento ilmeisesti vaatii jonkin tietyn lokin olemassaolon.

Tässä vaiheessa tajusin, että minulla oli tuossa avain komennossa jäänyt kokonaan HTML koodaus pois, joten ihmekkään jos ei mitään tulostunut. Ei toiminut tämäkään.

Yritin toista sivua. (https://medium.com/@bdemir/a-pentesters-guide-to-server-side-template-injection-ssti-c5e3998eae68)
Sieltä löysin komennon {{settings.SECRET_KEY}}

![kuva](https://github.com/user-attachments/assets/9dbd68b0-be39-4d60-a150-32a8e0282b63)

Tämä näytti minulle salaisen avaimen.
Kuvasta ei selvästi näy, mutta komento laitettiin <p> </p> kenttien väliin, jotka ylemmässä kuvassa näkyvät. Tämä tulosti avaimen preview nappia painettuani.

Yritin tallentaa ikkunaa ja katsoa saanko siten labran valmiiksi.

Lisäsin löydetyn avaimen "Submit solution" kenttään.

![kuva](https://github.com/user-attachments/assets/c36fa266-9c5c-42fc-9b11-b6da55db2224)

Tehtävä oli valmis.

Käytin tehtävässä videota lopulta apuna oikeastaan vain muokkauskentän löytämiseen. En sitä itse olisi varmaan hetkeen löytänyt.
Harjoituksessa käytettiin Django frameworkin valmiita komentoja näyttämään salainen avain.

# pencode

## Asennus

Avasin annetun GitHub linkin ja siinä annettiin ohjeeksi käyttää Go asennusohjelmaa. (https://github.com/ffuf/pencode)

![kuva](https://github.com/user-attachments/assets/2b01fb86-bc27-421c-afda-01907f47b3e6)

Sain virheen ja päätin asentaa molemmat ehdotetut paketit. Tutkin mikä Go on ja ilmeisesti se on Googlen kehittämä ohjelmointikieli (https://go.dev/).

Asensin paketit komennolla 'sudo apt-get -y install gccgo-go golang-go'
Tämä antoi vain virheen, joten asensin ne omina paketteinaan.

![kuva](https://github.com/user-attachments/assets/aeee5c86-db61-47f1-9e31-148176fa55df)

Tällä kertaa asennus onnistui.

En silti löydä tiedostoa.

Koetin vielä Teron ohjeessa olevaa tapaa ladata tervapallo.

Käytin komentoja 'wget https://github.com/ffuf/pencode/releases/download/v0.4/pencode_0.4_linux_amd64.tar.gz' ja 'tar xf pencode_0.4_linux_amd64.tar.gz'

Tällä kertaa './pencode -h' näytti ohjeen.

![kuva](https://github.com/user-attachments/assets/5a898f03-6712-43d1-aa23-6848d5bf031f)

## Käyttö

Ajoin tekstin tiedostoon, mutta minulla oli vaikeuksia saada tekstiä luettua "input" kenttään. En keksinyt syntaksia.

Keksin kierron lukemalla sen ensin 'cat' komennolla ja putkittamalla pencodelle.

![kuva](https://github.com/user-attachments/assets/57ff0cfc-3534-48dc-a2bd-6513ce4bb2e7)

Tehtävä suoritettu onnistuneesti.

# Lähteet

1. T. Karvinen, 2024, Tunkeutumistestaus, https://terokarvinen.com/tunkeutumistestaus/, Luettu 24.11.2024
2. OWASP, 2021, A01:2021 – Broken Access Control, https://owasp.org/Top10/A01_2021-Broken_Access_Control/, Luettu 24.11.2024
3. OWASP, 2021, A10:2021 – Server-Side Request Forgery (SSRF), https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_(SSRF)/, Luettu 24.11.2024
4. PortSwigger Academy, 2024, Insecure direct object references (IDOR), https://portswigger.net/web-security/access-control/idor,
5. PortSwigger Academy, 2024, Path traversal, https://portswigger.net/web-security/file-path-traversal,
6. PortSwigger Academy, 2024, Server-side template injection, https://portswigger.net/web-security/server-side-template-injection
7. PortSwigger Academy, 2024, Server-side request forgery (SSRF), https://portswigger.net/web-security/ssrf
8. PortSwigger Academy, 2024, Cross-site scripting, https://portswigger.net/web-security/cross-site-scripting
9. PortSwigger Academy, 2024, Tehtävänannot (Linkki tehtävän vieressä)
10. Kadalon Security & Intelligence, 2021, Using Nested Traversal Sequences to Bypass File Path Traversal Defense, https://kadalonsecurity.medium.com/using-nested-traversal-sequences-to-bypass-file-path-traversal-defense-3982feb4e60b
11. W3schools, Window alert(), https://www.w3schools.com/jsreF/met_win_alert.asp
12. Seven Seas Security, SSTI Complete Lab Breakdown: SSTI w/ info disclosure via user-supplied objects, https://www.youtube.com/watch?v=8o5QPU-BvFQ
13. GitHub davdts, 2021, Django App Vulnerable to Django Templates SSTI, https://github.com/Lifars/davdts
14. B. Demir, 2020, A Pentester’s Guide to Server Side Template Injection (SSTI), https://medium.com/@bdemir/a-pentesters-guide-to-server-side-template-injection-ssti-c5e3998eae68
15. GitHub joohoi, 2024, pencode, https://github.com/ffuf/pencode
16. Google, 2024, Go, https://go.dev/            

