# Harjoitus 3 Nuuskija

# a) Hyökkää

Valitsin hyökkäykseksi edellesissä harjoituksessani tekemäni UnrealIRCd hyökkäyksen (https://github.com/panupeltola/tunkeutumistestaus/blob/main/Harjoitus2.md)
Teen hyökkäyksen samoin, kuin edellisessä harjoituksessa. Kuuntelen tällä kertaa kuitenkin kaiken liikenteen Wiresharkilla.
Hyökkäys tehdään kahden oikeasta verkosta poistetun virtuaalikoneen välillä, siten ulkopuolisen liikenteen riski ei ole olennainen, eikä tiedoissa ole peiteltävää.

Tein normaalit ping testit tämän varmistaakseni ja säädin metasploitin kuntoon.

![kuva](https://github.com/user-attachments/assets/4a3f4e9c-b676-4050-a364-9aa42401129d)

Valitsin oikean hyökkäyksen, payloadin ja kohteen. Tällä kertaa katson, toimiiko ohjelma kun olen avannut portin 4444 palomuurista. Edellisellä kerralla se aiheutti ongelman, eikä yhdistäminen onnistunut ennen palomuurin sulkemista.

![kuva](https://github.com/user-attachments/assets/a07813d5-6d9c-4e7c-9ebf-e1c167134f30)

Hyökkäys onnistui. Itseäni jäi hyökkäyksessä hämäämään näkyvät echo komennot. Tutkin niiden kohtaloa seuraavissa kohdissa enemmän.
Hyökkäys itsessään käyttää IRC palvelinta hyökkäyksen toteuttamiseen. Kohteena on UnrealIRCd demoni, joka on avoimen lähdekoodin IRC palvelin. Katsoin nopeasti Wiresharkin kaappaamaa tietoa ja huomasin hyökkäksen ilmeisesti injektoivan shellin telnet yhteyden avulla.

![kuva](https://github.com/user-attachments/assets/83640498-54b3-4091-b50c-15a9b18677f9)

Halusin ensin nähdä, miten koodi toimii ennen sen syvällisempää analyysiä. Analyysi tehdään seuraavissa kahdessa kohdassa. 

# b) Koodi

Minulla ei ollut tällä koneella Microa asennettuna, joten käytin Nanoa. Katsoin käytetyn exploitin viemällä metasploit session taka-alalle CTRL+Z näppäinyhdistelmällä.

Katsoin hyökkäyksen olevan kansiossa unix/irc/unreal_ircd_3281_backdoor.

![kuva](https://github.com/user-attachments/assets/25f3df88-b595-43bd-9153-c55b40afd4bd)


Tämän jälkeen avasin tiedoston kulkemalla luennolla opittua polkua sekä tabulaattoria ja avasin tiedoston komennolla 'nano /usr/share/metasploit-framework/modules/exploits/unix/irc/unreal_ircd_3281_backdoor.rb'

Katsoin koodia ja ensimmäisenä tuli vastaan kohteen tiedot määrittelevä kenttä.

![kuva](https://github.com/user-attachments/assets/1e716c5b-e6b5-4a98-a82e-3ea3163eea91)

Siinä määriteltiin sen tiedot ja vaatimukset Metasploitin toimintoja ja hakuominaisuuksia varten.

Tämän alla oli itse haitallinen koodi.

![kuva](https://github.com/user-attachments/assets/022ea0fa-9bf3-44d1-947e-cebd51fc59f3)

Koodi oli erittäin lyhyt.

Ymmärtääkseni koodissa haettiin ensin palvelimen banner tiedot ja käyttäjänimeä pyydettäessä lähetettiin "AB; ja payloadin koodi sekä rivin vaihto"

Halusin ymmärtää hiukan miten tämä pystyi toimimaan. Koodin kuvauksessa mainitaan myös, että tämä käyttää "malicious backdooria" eli oletettavasti takaovi olisi tahallinen.

Googlasin asiaa ja löysin aiheesta bugi-ilmoituksen, jossa asia selitettiin ainakin jollain tasolla.
Version latausta oli ulkopuolisen tason osalta muokattu ja siihen oli istutettu takaovi, joka teki systeemitason kutsun debuggauksen muodossa.
Tämä takaovi antoi hyökkääjälle oikeudet ajaa mitä tahansa komentoa palvelinta käyttävän käyttäjän oikeuksilla ja ohitti kaikki salasanat sekä muut turvatoimet (https://www.openwall.com/lists/oss-security/2010/06/14/11)

Parhaan ymmärrykseni mukaan komento "AB;" nimikentässä avasi siis oikeuden hyökkääjälle ajaa haluamansa komennot

# c) snifsnif
Aloitin ensimmäisestä rivistä ja valitsin siitä "Follow TCP trail"

![kuva](https://github.com/user-attachments/assets/f09ebcbd-6432-42de-90bd-c05ae3d1055e)

Alla näkyi tutun näköinen koodi. "AB;" jälkeen oli kuitenkin itselleni oudon näköistä koodia. Tutkin oliko kyseessä payloadin ajama koodi ja komennolla 'nano /usr/share/metasploit-framework/modules/payloads/singles/cmd/unix/reverse.rb' avasin käyttämäni payloadin lähdekoodin.

![kuva](https://github.com/user-attachments/assets/db311408-89b8-4469-bb01-84cca2e74cbd)

Siitä katsoin, että koodi näyttää samalta, eli vain AB; osa on payloadin osaa.
Wireshark siis näyttää, että tällä komennolla luotiin telnet yhteys metasploitablen ja hyökkäyskoneen välille.

![kuva](https://github.com/user-attachments/assets/3700f949-44a5-4c04-8742-276372738d57)

Seuraavassa vaiheessa näin, kun payloadin määrittämä portti 4444 lähettää kohteelle echo sanomia.

![kuva](https://github.com/user-attachments/assets/eda55a4b-3430-46ec-b52a-70a7419e8dcb)

Vastaus tulee kuitenkin vasta seuraavassa ketjussa, jossa metasploitable lähettää virhesanomia ja vastaa 'echo' pyyntöihin

![kuva](https://github.com/user-attachments/assets/033e3ef9-053f-431b-97dd-c9dda12cb940)

Katsoessani paketteja huomasin, että hyökkäyskone lähettää saman 'echo' komennon kaksi kertaa riveillä 26 ja 28. Vastaukseksi se saa kuitenkin rivillä 27 sekavaa mössöä ja rivillä 29 oikean echo vastauksen.
Rivillä 32 lähetetty echo saa myös vastauksen.

Muuta syytä echojen lähettelylle kuin yhteyden testaamisen tai isännän määrittämisen lisäksi en ymmärrä.

![kuva](https://github.com/user-attachments/assets/45cfac49-1e4e-43f5-8430-bc99a24bfbdb)

Katsoin Metasploitin ilmoituksia vielä kerran.
Näin Wiresharkista, miten yhteys muodostettiin ja miten takaovi lähetettiin. Näin myös 'echo' komennot ja ilmeisesti kyseessä oli juurikin isännän valinta.
Ymmärtääkseni siis tässä hyökkäyksessä ei ole sen monimutkaisemmasta haavoittuvuudesta kyse, kuin tahallisesti asennetusta takaovesta, joka mahdollistaa haitallisen koodin ajamisen käyttäjän tunnuksilla.

Alla linkki dataan.

https://easyupload.io/ng17si

# e) 





















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


















