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
