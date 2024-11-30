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

























