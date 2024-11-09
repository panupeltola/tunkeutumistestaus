# Harjoitus 3 Nuuskija

# f) HTB

## Ennen aloittamista yhteyden varmistaminen

Ennen aloittamista halusin varmistaa yhteyteni vain Hack The Boxin kohdekoneelle. HTB:n säännöissä kerrottiin, että maalikoneet ovat verkossa 10.10.10.0/24.
Mietin toimivaa verkon rakennetta ja tulin tulokseen, että vain lähetettävää liikennettä tulee säädellä. Sisään tulevalla liikenteellä ei tässä tapauksessa ole väliä.
Googlasin tähän ratkaisua ja löysin 'iptables' komennon, jolla voi säätää tulevaa ja lähtevää liikennettä access listien tavoin.
Katsoin 'man iptables' ja totesin että oletuksena iptablesilla on kolme eri tyyppistä liikennettä:
- INPUT eli sisään tuleva liikenne
- FORWARD eli laitteen läpi kulkeva liikenne
- OUTPUT eli laitteen tuottama liikenne

Näistä halusin siis kieltää kaiken FORWARD ja OUTPUT liikenteen paitsi 10.10.10.0/24 menevän OUTPUT liikenteen
Liikenteen estäminen tehdään komennolla 'DROP'
Ohjeen löytämiltäni foorumeilta komento oli muutettu muotoon 'iptables -P OUTPUT DROP'
Koetin pingiä ennen ja jälkeen tämän komennon.

![kuva](https://github.com/user-attachments/assets/e299103d-6131-4fbd-a3ee-44ae8ba47177)

Olin sulkenut verkon.
Tein seuraavaksi saman FORWARD liikenteelle. En usko, että tällä on väliä, mutta pelaan mielummin varman päälle.
Liityin HTB sivuilta lataamaani ja VPN yhteyksiini lisäämän lab_passeli openvpn yhteyden ja hain sitä ifconfigilla

![kuva](https://github.com/user-attachments/assets/daa1bd3a-e3a5-4948-8a06-2c669478f7ac)

Näin sen käyttävän verkkokorttia tun0. Täten liikenne saisi kulkea vain sen kautta.
Tässä vaiheessa en oikein osannut edetä ja kysyin ChatGPT:ltä apua promptilla "using linux kali and iptables how do I accept outbound networking only to network 10.10.10.0/24"
Ohje oli mennyt oikein tähän asti, mutta nyt piti lisätä sääntö ulos menevälle liikenteelle.

GPT antoi ohjeeksi komennon 'sudo iptables -A OUTPUT -d 10.10.10.0/24 -o tun0 -j ACCEPT'

Katsoin manuaalista tämän komennon:

- '-A' lisää uuden säännön sääntölistan loppuun
- OUTPUT koskee lähtevää liikennettä
- '-d' määrittää kohdeverkon
- 10.10.10.0/24 määrittää verkon osoitteen
- '-o' määrittää uloslähtölaitteen
- 'tun0' on verkkokortti. Tällä kertaa kävi onni ja laite oli sama kuin ylempänä 'ifconfigilla' hakemani 
- '-j' Määrittää miten toimia jos nämä ehdot täyttyvät
- ACCEPT hyväksyy liikenteen

Ymmärtääkseni tämä komento lisää siis säännön, joka sallii koneelta ulos lähtevän liikenteen kohteeseen 10.10.10.0/24 VPN käyttämän verkkolaitteen kautta.

Tämän lisäksi ChatGPT luoma ohje suositteli sallimaan vielä liikenteen loopback adapterille komennolla 'sudo iptables -A OUTPUT -o lo -j ACCEPT'
Lisäsin myös sen.

![kuva](https://github.com/user-attachments/assets/4347090f-98af-4b53-9281-d1ed58edb591)

Nyt sain yhteyden aiemmin HTB luomaan virtuaalikoneeseen, mutten Googlen DNS palvelimeen. Nähdäkseni tämä konfiguraatio siis teki mitä pitää.
Jatkoin tehtävän tekemistä onnellisena ja päätin miettiä asetusten nollaamista myöhemmin. Ajattelin, että jos en tallenna asetuksia niin ne eivät luultavasti säily koneen uudelleen käynnistämisen yli.

## Hyökkäys
Valitsin kohteekseni koneen PermX

Ensimmäisenä oli ongelma, että en saanut yhteyttä koneeseen. Ongelman syynä oli, että koneen osoite oli sallimani OUTPUT liikenteen ulkopuolella.
Sallin sen komennolla 'sudo iptables -A OUTPUT -d 10.10.11.23 -o tun0 -j ACCEPT'
Tämän jälkeen vielä varmistin, ettei kone ollut muuhun ulkoverkkoon yhteydessä.

![kuva](https://github.com/user-attachments/assets/4f32960f-9716-4624-a4e8-6c58af6986c5)

Kaikki näytti hyvältä ja uskalsin aloittaa koneeseen hyökkäämisen.

Tässä vaiheessa hiiren kursori katosi täysin näytöltä ja tuleepahan selvitettyä tallentuvatko asetukset iptablesiin koska kone piti käynnistää uudelleen.

![kuva](https://github.com/user-attachments/assets/440ee6bd-01c0-4955-91cc-44b1f2024586)

Ping toimi, eli asetukset nollaantuivat.

Päätin ajaa komennot:
'sudo iptables -P OUTPUT DROP'
'sudo iptables -P FORWARD DROP'
'sudo iptables -A OUTPUT -d 10.10.11.23 -o tun0 -j ACCEPT'

Ennen ajamista testasin toimiiko verkko VPN läpi ylipäätään.

![kuva](https://github.com/user-attachments/assets/bfce77e4-571d-400c-9c40-8cb36c9b4edc)

Totesin, etten ole verkkoon yhteydessä tämän VPN yhteyden kautta.

Katsoin vielä löytyykö kone:

![kuva](https://github.com/user-attachments/assets/45471cf9-fbd1-484f-97d2-eae80febff8a)

Kone löytyi. Totesin kuitenkin, että aiemmin tekemäni muutokset iptablesilla eivät olleet tässä tapauksessa tarpeellisia, mutta hyvä oli tämäkin taito jatkon kannalta opetella.

Aloitin hyökkäyksen skannaamalla aktiiviset portit komennolla 'sudo nmap -A -p- 10.10.11.23'.

Yrittäessäni tehdä seuraavaa askelta ja katsoa auki porttiskannauksesta löytynyttä http osoitetta, totesin että verkkosivut toimivat äärimmäisen hitaasti eivätkä suostu avautumaan.
Sama toistui toista konetta yrittäessäni. Päätin tässä vaiheessa siirtyä askeleen taakse päin ja tehdä lisää Starting Point harjoituksia, koska ne tuntuivat enemmän omalle tasolleni sopivilta.

Aloitin harjoituksen Appointment

## Appointment 

Vastasin ensimmäisiin kysymyksiin samalla kun ajoin komentoa 'sudo nmap -sV 10.129.225.218' skannatakseni kohdekoneen portit

![kuva](https://github.com/user-attachments/assets/4e81bb94-5fa9-44c6-a9d2-5ccab60a9e05)

Skannauksesta totesin, että vain yksi portti on auki. Se on 80 ja sitä kuuntelee Apache.

Jatkoin yksinkertaisiin kysymyksiin vastailua, kunnes tuli kysymys, mikä on työkalun gobuster kansioita hakevan komennon nimi.
Vastauksen löysin kalin verkkosivuilta. Se on 'Dir' (https://www.kali.org/tools/gobuster/)


















