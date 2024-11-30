# x) Try Web Hacking on New Webgoat 2023.4

- Ohjeessa ladataan Webgoatin versio 2023.4 ja siihen liittyvät paketit
- Webgoat on Java paketti, joka luo kohdemaalin localhostille.
- Lataamisessa tulee varmistaa oikea versio

# a) Asenna Webgoat 2023.4

Seurasin tehtävässä T. Karvisen ohjetta (https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/)
Aloitin lataamalla Webgoatin Github sivulta oikean paketin "Releases" osiosta

![kuva](https://github.com/user-attachments/assets/6b3be61b-3437-45d0-afb2-4168e82e1d15)

Valitsin jar paketin oikealla hiiren näppäimellä ja kopioin sen linkin. Latasin paketin oikeaan kansioon komennolla 'wget https://github.com/WebGoat/WebGoat/releases/download/v2023.4/webgoat-2023.4.jar'
Ajoin Teron ohjeessa olleen komennon 'java -Dfile.encoding=UTF-8 -Dwebgoat.port=8888 -Dwebwolf.port=9090 -jar webgoat-2023.4.jar'
Tämä alustaa Webgoat ja Webwolf palvelut portteihin 8888 ja 9090.

Tarkisitin löytyykö Webgoat nyt ympäristöstä http://127.0.0.1:8888/WebGoat, kuten viimeinen rivi alustuksen lokista kertoi.

![kuva](https://github.com/user-attachments/assets/27a75eb0-9db8-4037-8d6c-8abd3d33f03b)

Webgoat oli asennettu.

# b)  (A1) Broken Access Control

## Hijack a session

Harjoituksen tarkoituksena on arvata "hijack_cookie" arvo

Kaappasin liikennettä ja huomasin kohdan JSESSIONID=zEbHaV7SkZw3QEPu_rSZUeOcGpkyZnYNaI4fHPEO.

Tämä ei suoranaisesti kertonut itselleni mitään. Koodit näyttivät satunnaisilta. Tämä ei myöskään ollut keksi, jota murtaa.
Sen löysin Firefoxin "Cookies" osiosta.

![kuva](https://github.com/user-attachments/assets/8792bb98-b2cb-4edf-8e85-b2c64779790b)

Poistin keksit ja aloitin uuden session.

![kuva](https://github.com/user-attachments/assets/c7c07870-4b46-42f8-8606-85c739388d2d)

Aloin nähdä kaavan

![kuva](https://github.com/user-attachments/assets/0fafed65-a8b9-49d9-8b58-fe35a5a5d045)

Osa ennen viivaa kasvoi yhdellä ja seuraavassa osassa muuttui vain loppuosa.
Halusin koettaa vielä kerran nähdäkseni olinko oikeassa.

Poistin keksit ja latasin sivun uudelleen.

![kuva](https://github.com/user-attachments/assets/8d583112-f8aa-4e35-b6fd-75ec347c3115)

Kolmannen kohdalla aloin jo luottamaan teoriaani, että ensimmäinen osa on juokseva ja kasvava numero ja toisessa osassa numero muuttuu -173290* muodossa.
Jäljelle jää siis 7 arvoa joiden määräytymistä en ymmärrä. Kaikki ovat kuitenkin numeroita joten oletettavasti tämän voisi Brute forcettaa.

Ongelmaksi kuitenkin muodostuu, etten tiedä mistä tämän tekisin. 

![kuva](https://github.com/user-attachments/assets/e334bb6b-bb2c-43f5-b4a0-d1660a90602a)

Tarkastin vielä, ettei numeroiden välillä ollut vain jotain helppoa lukua.
Tässä vaiheessa jouduin katsomaan apua vinkeistä ja sieltä näin, että ensimmäinen luku oli järjestysnumero ja seuraava numero oli Unix Epoch aika.

Tämä selittää miksi vain loppu muuttui eri keksin nollaamisien välillä.

Nyt ymmärsin miten logiikka toimi. Jäljellä olisi siis yritys saada Brute Forcettua jokin sessio. Oletin, että ensimmäinen luku pysyisi saman ja kaikki hyökkäykset keskittyisivät toisen osan FUZZaamiseen.

Jällen tuli ongelma: Miten sen teen.
ZAProxy ei näyttänyt hijack_cookie arvoa, joten en voinut sitä käyttää, enkä keksinyt tapaa varmistaa toimivaa keksiä muuta kuin kirjoittaa se käsin, enkä sillä olisi vielä ensi viikolla valmis.

Tässä vaiheessa olin jumissa. En keksinyt mitä työkalua käyttää tai mitä yrittää. Päätin katsoa löytämääni [videota](https://www.youtube.com/watch?v=R5YPRhM5GyE), ainakin siihen asti, että saisin vinkin seuraavasta askeleesta, sillä WebGoatin vinkeistä ei ollut juurikaan apua.

Kävin välissä nukkumassa ja tehtävä jatkui *30.11.2024 klo 8:26*

Ennen videota kuitenkin halusin vielä miettiä mitä tiedän ja miten oletin hyökkäyksen tapahtuvan:
- Sessioilla on juokseva numerointi, mikäli saisin nopeasti luotua monta tokenia niin välissä oleva olisi jonkin muun käyttämä.
- Tähän väliin osuneen sessio on ajan määrittämä, joten sen lopun arvaamista varten minun pitäisi ajaa saadut ajat läpi yli hypätylle sessio numerolle.

Ensimmäinen ongelmani ZAPilla oli, etten nähnyt hijack_cookie keksiä ZAPissa

Ennen kuin pääsin sitä ratkomaan tuli ongelmaksi, ettei ZAP kaapannut mitään liikennettä vaikka proxy oli päällä ja eilen kaikki toimi samoilla asetuksilla.

![kuva](https://github.com/user-attachments/assets/b8016419-ff51-41c3-99c2-19400967cc5f)

Hetken vihaani pannukakkuja tehneenä ja mieleni rauhoittaneena tajusin, että olin kirjoittanut edellisellä kerralla osoitteen suoraan 127.0.0.1:8888 ja tällä kertaa käytin "localhost"
Lisäsin sen listaan ja katsoin miten käy.

![kuva](https://github.com/user-attachments/assets/360d69a9-0344-4192-9ccb-c08c649837f2)

Ongelma ratkaistu.

Seuraavaksi keksien kimppuun.

![kuva](https://github.com/user-attachments/assets/4abba6fe-ade0-4aad-b485-7181c8108d38)

Tänään oikea keksi näkyi heti, olin edellisellä kerralla oletettavasti kaapannut liikennettä ennen keksin määrittämistä.

Löysin ZAP dokumentaation lisäosasta, jonka pitäisi tuottaa juurikin tuota haluamaani tietoa.

![kuva](https://github.com/user-attachments/assets/8d046ca7-0d66-4b1e-b6fb-5f2ba3058dc8)

Latasin haluamani lisäosan ZAP proxyyn.

Nyt sain mahdollisuuden generoida tokeneita.

Työkalu antoi kuitnkin jokaisesta pyynnöstä vastauksen 401 Unauthorized.

Päätin tässä vaiheessa mennä videon pariin ja siinä käytettiin Burpsuitea, joten avasin ohjelman.

## Burp

Burpsuitessa valitaan ensin POST pyyntö, jonka yhteydessä hijack_cookie on luotu. Tämä pyyntö lähetetään ominaisuuteen nimeltä Sequencer ja aloitetaan ominaisuus "Start live capture".
Käytännössä mitä ymmärtääkseni tämä tekee on lähettää erittäin monia POST pyyntöjä sisäänkirjautumista varten. POST pyyntö ja keksin luonti tapahtuivat sillä hetkellä, kun yritin kirjautua sisään.
Oletettavasti sillä hetkellä myös sivu luo uuden keksin, joka sisäänkirjautumisen onnistuessa pysyy käyttäjällä aktiivisena ja sen liittäminen omaan selaimeen päästää kirjautumaan sisään halutulla vaihtoehdolla.

![kuva](https://github.com/user-attachments/assets/b0dc44b9-12a9-4eca-a64d-8527059e7afa)

![kuva](https://github.com/user-attachments/assets/dba40f7d-6819-43ad-b3b0-b77264e210d4)

Ajettuani komennon katsoin tiedostoa johon ne olin tallentanut:

![kuva](https://github.com/user-attachments/assets/aabeca6b-333a-46e3-ad44-f8ec7450dfed)

Tässä huomasin, että palvelimien vastauksien epätasaisuuden vuoksi lista ei ollut täysin järjestyksessä ja yritin säätää sen ensin kuntoon. Tein sen komennolla 'sort -n tokens.txt > tokenssort.txt'

Nyt katsoessani listaa huomaan katsoin puuttuvia tokeneita komennolla 'tail -10 tokenssort.txt'. Komento näyttää kymmenen viimeistä riviä tekstitiedostosta

![kuva](https://github.com/user-attachments/assets/2600f802-68fb-496c-8ce7-5ac37526ce05)

Huomasin, että näistä kymmenestä puuttuu 71 ja 78 loppuiset sessiot.

78 kuitenkin saattaa johtua vain siitä ettei palvelin ole ehtinyt vastata.

Yrtiin siis murtaa tokenia 71.

Koska sitä ympäröivät keksit olivat:

8593078645793923770-1732951095310 ja 8593078645793923772-1732951095311

Pystyin päättelemään tästä ajan olevan joko 310 tai 311 loppuinen.
Vaikka tokenit olivat eri järjestyksessä ladatussa tiedostossa palvelimen vastauksesta johtuen, mikään myöhemmin tehty post pyyntö ei ikinä ole loppunumeron ajasta otettussa numerossa alempana kuin aiemmin tehty pyyntö. 

![kuva](https://github.com/user-attachments/assets/467298c0-5839-49a1-974c-103061cd173b)

*Ote sortatusta keksiluettelosta*

Koska vaihtoehtoja oli vain kaksi päätin yrittää syöttää niitä käsin hijack_cookie osaan, päivittää sivun ja kirjautua sisään.

Kumpikaan näistä ei toiminut. En keksinyt mikä tässä oli vikana, joten jatkoin videon katsomista. Ehkä kirjautumistavassani oli vikaa.

Ohjevideossa käytettiin Burpsuiten Intruder ominaisuutta. Päätin yrittää samaa. Siinä myös todettiin, että mikäli yksi numero ei toimi valitse vain toinen. Näin tein.

Seuraavat arvot, joita tutkin olivat:
8593078645793923758-1732951095289
8593078645793923760-1732951095295

Eli 59 loppuinen oli se mitä hain. Näissä myös aika välissä oli hiukan pidempi.

Käytin ohjeen mukaan Burpsuiten Intruder osiota ja lisäsin viimeiselle kahdelle numerolle payloadin, joka kasvaa yhdellä ja yrittää kaikki kahden kirjautumisen välillä olleet ajat läpi.

![kuva](https://github.com/user-attachments/assets/d9a2ad8d-b75d-4aae-a5fa-f23bc25dfcc0)

Painoin "Start attack" nappia.

![kuva](https://github.com/user-attachments/assets/f1fdc44f-326c-499b-92cb-7c7204e6968a)

Mikään vaihtoehto ei toiminut.

Nyt kun osasin tavan päätin yrittää tehdä asiat uusiksi, mikäli tokenit olisivat vanhentuneet ja yrittää sopivaa väliä, kunnes jokin toimisi.

Yritin samaa monta kertaa, mutta mikään välissä olleista ei kelvannut.

Päätin yrittää vielä samaa löytämäni [ohjeen](https://docs.cycubix.com/application-security-series/web-application-security-essentials/solutions/a5-broken-access-control/a1-2021-or-hijack-a-session-or-cycubix-docs/a1-2021-or-hijack-a-session-2-or-cycubix-docs) mukaan ZAPilla.

Siinä tehdään sama, mutta manuaalisesti.

Löysin välin käsin lähettämistäni POST pyyntöjen Response headereista väliltä.

8593078645793932782-1732954305052
8593078645793932784-1732954305218

Säädin ZAPin Fuzzeriin oikean aikavälin.

Ensimmäisellä yrityksellä sain ison kasan 500 virheitä, oletettavasti johtuen liian nopeista pyynnöistä.

Toiselle kierrokselle laitoin 10 ms viiveen jokaisen Fuzzin välille.

![kuva](https://github.com/user-attachments/assets/b5920613-fb60-4d0a-8e8e-d816ba20b8ef)

Mikään ei tälläkään kertaa ollut oikein. Tässä vaiheessa olin aidosti jumissa. En keksinyt mikä tässä oli vielä väärin. Ymmärsin täysin logiikan, jolla välejä etsin ja sain sen tutkittua kahdella eri ohjelmalla, mutta mikään ei kelvannut vastaukseksi.

Päätin jättää tehtävän tältä erää tähän ja palata myöhemmin sen pariin.

## Insecure Direct Object References

### Authenticate First, Abuse Authorization Later

Tehtävässä piti löytää tapa ohittaa luvat kirjautumisen jälkeen.
Kirjauduin sisään käyttäjällä tom:cat

### Observing Differences & Behaviors

Klikkasin "View Profile" nappia ja näin, että näytetyn lisäksi ZAP näytti minulle vielä role ja userID kentän

![kuva](https://github.com/user-attachments/assets/b23b99f4-1798-48d3-b9e6-28b67ac9c927)

Hetken yrittämisen jälkeen sain vielä vastauksen kirjoitettua oikeaan muotoon ja sain tehtävän raktaistua.

![kuva](https://github.com/user-attachments/assets/6ee76036-2c29-4447-a82d-4289f32f507b)

### 

Seuraavassa kohdassa piti löytää vaihtoehtoinen reitti profiiliin.

![kuva](https://github.com/user-attachments/assets/45b9e172-53e2-4165-b1f9-066c4100edd8)

Katsoin tekemääni GET pyyntöä profiilia avatessani ja huomasin sivun */WebGoat/IDOR/profile

Päätin yrittää kirjoittaa tätä selaimeen.

![kuva](https://github.com/user-attachments/assets/f424d420-9acc-432d-be71-0aaa05566add)

Tämä avasi itselleni profiilisivun JSON näkymän.

Mietin miten tämän saisi tehtyä muulla tapaa, sillä selain tunnisti minut oletettavasti keksillä. Päätin koettaa miten käy jos laitan perään ainoan erottavan tekijän, eli userID:n

![kuva](https://github.com/user-attachments/assets/7d3313c4-28a3-49da-9ecc-092d69ae82db)

Sain eri virheilmoituksen tällä kertaa.
Koetin siitä huolimatta polkua vastaukseksi ja sain oikein menneen raktaisun.

![kuva](https://github.com/user-attachments/assets/388fa313-bfe2-4e82-a413-695e48edc546)

### Playing with the Patterns

Viimeisenä piti päästä jonkun muun käyttäjälle.

Yritin ensin muokata vain yhtä numeroa userID kohdassa.

Tämä aiheutti vain 500 errorin, eli yksinkertainen numeron nostaminen ei tässä riitä.

Yritin siis fuzzata 100 seuraavaa numeroa nähdäkseni toimiiko näistä mikään.

![kuva](https://github.com/user-attachments/assets/cb6528c6-0fa2-4a92-ae32-e053d544223e)

Löysin yhden käyttäjän.

![kuva](https://github.com/user-attachments/assets/06f992c1-3e71-4969-810d-80faeb056b95)


![kuva](https://github.com/user-attachments/assets/93aac5ec-6ac3-4277-af5a-88a641f24013)

Seuraavaksi tätä piti saada muokattua.

Luin Restful API:n dokumentaatiota ja huomasin, että muokkaus tapahtui komennolla PUT.

Päätinkin siis yrittää vaihtaa ensin oman käyttäjäni roolia komennolla 'PUT http://localhost:8888/WebGoat/IDOR/profile/2342384' ja laittamalla alla olevaan kenttään muuten samat arvot, mutta kohtaan role arvon 1.

![kuva](https://github.com/user-attachments/assets/dcf398d5-f5fc-4bb7-9ef6-94fc2d567432)

Olin jälleen muuttanut maailmaa.

Seuraavaksi piti muuttaa Billin arvoja.


Päätin muuttaa Buffalo Billin nimen Chill Billiksi kuvaamaan paremmin hänen luonnettaan.

![kuva](https://github.com/user-attachments/assets/4e6e7a67-146b-4c82-bf13-72e6824262af)

Totesin, että nimen vaihto tuottaa virheen

![kuva](https://github.com/user-attachments/assets/bf8ef48b-84d5-442a-9b10-42d2599ad1d9)

Roolin kuitenkin pystyin vaihtamaan. Tämä antoi seuraavan haasteen muuttaa väri mikä ei ollut kuin sanan muutos PUT komentoon.

![kuva](https://github.com/user-attachments/assets/a620a642-d374-4e6c-abf9-b57a936abdc2)

Nyt oli tehtävä ratkaistu.

## Missing Function Level Access Control

Aloitin kliksuttelemalla kaikki näppäimet läpi, mutta ZAPiin ei tullut mitään järkevää.

Yritin tämän jälkeen käyttää Inspect ominaisuutta selaimessa ja löysin riittävän pitkälle mentyäni valikon, jota ei näkynyt graaffisessa käyttöliittymässä.

![kuva](https://github.com/user-attachments/assets/dd97313d-fd67-470e-863e-bd35e5a05837)

Sen sisältä löytyi kohta Admin ja adminvalikosta vaikka mitä herkkuja

![kuva](https://github.com/user-attachments/assets/73be49bb-f5f3-4767-9910-de2ddb6509a9)

### Try it

Seuraavassa kohdassa piti löytää hash arvo.

Aloitin klikkaamaal submit ja näkemällä mistä osoitteesta viestit tulevat.

Päätin yrittää kirjata äsken löytämiäni arvoja tähän polkuun.

![kuva](https://github.com/user-attachments/assets/cf10dad4-c88c-4139-9df2-2a6a659428e6)

*Users*

![kuva](https://github.com/user-attachments/assets/c740164d-687a-427b-b069-800b5fae1652)

*Config*
Näistä vain users antoi järkevän ratkaisun ja sekin antoi virheen 415 "Unsupported Media Type"

Luin Mozillan [dokumentaatiota](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/415) aiheesta ja siellä huomautettiin, että vastaus ilmoittaa oikean tyyppisen viestin.

Tässä tapauksessa Responde headerissa oli Accept: application/json

Yritin siis muuttaa kenttääni "Content Type" kohtaan tämän arvon.

![kuva](https://github.com/user-attachments/assets/08378937-1cf4-4cb1-992a-389c20f011e0)

Tällä kertaa sain eri vastauksen

![kuva](https://github.com/user-attachments/assets/4ea0dd9e-2880-4ff2-85bd-2ec10457d7e5)

Aloin myös miettiä, mitä hyötyä minulla on lähettää POST pyyntöjä.
Yritin muottaa tätä GET muotoon.

Nyt sain ison kasan käyttäjiltä tietoa mukaan lukien hash arvot.

![kuva](https://github.com/user-attachments/assets/d9a151a0-1b46-4d7e-a326-ba0124653d29)

Kohta oli ratkaistu.

### 3

Viimeiseksi piti vielä tehdä sama uudelleen sen jälkeen kun ylläpitäjät olivat parantaneet turvallisuutta.
Aloitin klikkaamalla "Submit"

Sain tismalleen samalla komennolla kaikki tiedot ulos.

![kuva](https://github.com/user-attachments/assets/a5e4c406-7517-413d-9c4e-7ced570acc5e)

Ilmeisesti tämä oli kuitenkin väärin.

Yritin toista löytämääni piilotettua kansiota, mikäli /users-admin-fix/users näyttäisi oikeat tiedot.

![kuva](https://github.com/user-attachments/assets/00fda489-b26f-49a9-8b01-b25ca64a24d2)

Uskoin osuneeni lähelle. Ongelma oli, että minun pitäisi olla Admin tämän haun tehdäkseni.

Päätin yrittää voinko vain päivittää itsestäni ylläpitäjän. Tässä vaiheessa jäin hiukan jumiin ja katsoin WebGoatin vinkkejä.

Näissä ohjeistettiin luomaan uusi käyttäjä POST komennolla. Minulla ei ollut yhtään käyttäjää tiedossa, joten uuden luominen olisi helpoin, jos tätä ei ole estetty.

![kuva](https://github.com/user-attachments/assets/543ce066-4ca6-40b0-82da-619db6ea69fe)

Uuden käyttäjän luonti onnistui.

![kuva](https://github.com/user-attachments/assets/ca9ae21e-d5dd-49fa-9dd2-b89918eb9a36)

Koetetaan PUT komentoa tehdä kaikista admineita. Tämä ei toiminut. Eli nyt minun pitäisi keksiä, miten pääsen toisella käyttäjällä käsiksi haluttuihin materiaaleihin. 

Hyvin pitkän tutkimisen, tuskailun yrittämisen ja muun kikkailun jälkeen löysin ratkaisun.

Käyttäjä piti luoda samaksi kuin oma WebGoat käyttäjä. Tähän oli erittäin huonosti ohjetta, eivätkä vinkit suoranaisesti auttaneet.

Piilotetun sivun /users-admin-fix löysin samasta piilosta, kuin users ja config sivut "Inspect" ominaisuudella.

Eli ratkaisuna oli lisätä käyttäjä passeli salasanalla jihuu ja arvolla admin: true.

![kuva](https://github.com/user-attachments/assets/64bc00de-d369-4241-a646-cb0b7f56c1d9)

Tämän jälkeen sivu latasi oikein.

Sain hashin Jerrylle: d4T2ahJN4fWP83s9JdLISio7Auh4mWhFT1Q38S6OewM= Tämä ei kuitenkaan jostain syystä kelvannut vastaukseksi.
Omasta mielestäni olen nyt tämän tehtävän ratkaissut, vaikka vastaus ei kelvannut WebGoatille.

Hash ei ole sama kuin aiemmassa ratkaisussa ja sen hakemiseen tuli avata uusi linja käyttäjälle ja korottaa oikeudet missä onnistuin. En jaksa selvittää epäselvän tehtävänannon/vastauksen syntaksin toimimattomuutta pidempään.









































































































