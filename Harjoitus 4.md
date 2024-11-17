# a) Hashcat

Seurasin tehtävässä T. Karvisen ohjetta (https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)
Aloitin asentamalla/varmistamalla onko ohjelmat jo asennettu komennolla 'sudo apt-get install hashid hashcat'
Hashcat toimii itse tiivisteiden murtajana ja Hashid on apuohjelma, jolla voidaan tunnistaa tiivisteen tyyppi.

Seuraavaksi latasin rockyou paketin, jossa on yleisimmät salasanat.
Latasin sen komennolla 'wget https://github.com/danielmiessler/SecLists/blob/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz'
Varmistin linkin ennen lataamista GitHubista.
Purin kansion komennolla 'tar -xf rockyou.txt.tar.gz'
Katsoin komennon toiminnan, sillä tar ei ole itselleni järin tuttu ohjelma.
'-xf' lippu purkaa paketin sisällön valittuun kansioon
Komento siis purkaa tervapallon.
Sain virheen yrittäessäni purkaa tiedostoa, pienen googlailun jälkeen löysin aiheesta stackoverflow keskustelun (https://stackoverflow.com/questions/39643013/gzip-stdin-not-in-gzip-format-tar-child-returned-status-1-tar-error-is-not-r)

Katsoin ohjeesta ehdotettua 'head <tiedosto>' komentoa ja sain vastauksen, että yritin purkaa html sivua.

![kuva](https://github.com/user-attachments/assets/136af5a4-ff79-4aee-9be7-ccadf62333c7)


Katsoin Teron ohjeen latauslinkkiä ja yritin mennä github hakemistossa syvemmälle ja kopioida osion Raw linkin.

Päivitetty komento: 'wget https://github.com/danielmiessler/SecLists/raw/refs/heads/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz'

Lataus oli pidempi ja tiedosto isompi tällä kertaa.

![kuva](https://github.com/user-attachments/assets/20012314-2f37-460c-885e-9ad3aef8c5d1)

Tällä kertaa lataus ja purku onnistuivat.

Poistin vielä pakatun tiedoston komennolla 'rm rockyou.txt.tar.gz'

Seuraavaksi piti tunnistaa millä funkiolla ohjeessa annettu tiiviste "f2477a144dff4f216ab81f2ac3e3207d" oli tehty.

Vein ensin tiivisteen tekstitiedostoon komennolla 'echo 6b1628b016dff46e6fa35684be6acc96 > hash.txt'

Tarkastin sen tiivisteen tyypin komennolla 'hashid -m hash.txt'
Tässä '-m' lippu kertoo mitä Hashcat muotoa tulee käyttää ohjelmaa ajaessa.

![kuva](https://github.com/user-attachments/assets/af329a2c-bb9c-49a3-9a08-efa9ed4de98c)

Sain vastaukseksi, että todennäköisimmät ovat MD luokan tiivistefunktiot. Näistä yleisin on MD5, joten päätin käyttäväni Hashcatin moodia 0.

![kuva](https://github.com/user-attachments/assets/50898e6e-a115-4ffc-8cec-83d2fd37da5f)

Katsoin hashcatin käyttöesimerkin ja totesin 'hashcat --help' ohjeen perusteella komennon olevan 'hashcat -m 0 hash.txt rockyou.txt'
Komennossa '-m 0' lippu määrittää käytettyä tiivistefunktiota.

Yritin komentoa.

![kuva](https://github.com/user-attachments/assets/78331872-83c7-4ba6-876c-daaff09b69d2)

Salasaana murtui hetkessä ja sain salasanaksi "summer".

Viimeisenä tarkastuksena varmistin vielä, että merkkijonon "summer" MD5 tiiviste on annettu komennolla 'echo -n summer | md5sum'

![kuva](https://github.com/user-attachments/assets/caa3e315-28bf-46ef-918e-a408625f38ec)

Tiiviste täsmäsi ja salasana oli murrettu.

# JumboJohn

Käytin ohjeessa apuna T. Karvisen ohjetta (https://terokarvinen.com/2023/crack-file-password-with-john/)

Asensin heti aluksi kaikki ohjeessa ehdotetut paketit. Näistä zlib-gst oli poistunut ainakin Kalin jakelusta, joten jätin sen pois komennosta ja päätin katsoa onko se tässä tapauksessa tarpeellinen.

Seuraavaksi latasin github repositorion komennolla 'git clone --depth=1 https://github.com/openwall/john.git'

Tässä komennossa kopioidaan repositorio omalle koneelle. '--depth=1' lippu lataa vain viimeisimmän version.

Siirryin ohjelman /src/ kansioon ja ajoin komennon ./configure, joka tunnistaa laitteen ympäristön ja luo makefile tiedoston.

Tiedoston luominen onnistui.

![kuva](https://github.com/user-attachments/assets/9a785dfb-cd2c-451e-bfef-f6a2a98b2ca2)

Ihmettelin, minkä takia minulle ehdotettiin eri komentoa, kuin Teron ohjeessa oli. Viimeinen lippu -sj4 oli omassa ohjelmassani ehdotettu -sj8.
Googlailin asiaa ja löysin aiheesta foorumikirjoituksen (https://stackoverflow.com/questions/15289250/using-make-with-j4-or-j8).
Ilmeisesti kyse on koneessa havaittujen prosessorien määrä. Omassani niitä oli 8.
Muuten 'make' komennon yhteydessä käytetty lippu '-s' määritti hiljaisen tilan, eikä ohjelma vastaile echoilla.
'&&' sitä seuraavan komennon vain jos edellinen komento on ajettu onnistuneesti.

Ajoin komennon 'make -s clean && make -sj8' ja ohjelman luonti onnistui. Sain paljon virheitä oletettavasti puutteellisita paketeitsta.

Yritin ajaa ohjelmaa komennolla $HOME/john/john/run/john

![kuva](https://github.com/user-attachments/assets/71c6ae8f-8fb1-4267-b403-c61688bed2cb)

Asennus oli onnistunut.

Latasin Teron ohjeesta zip paketin, mutten saanut sitä avattua, sillä se oli salasanasuojattu.

Ajoin ensin komennon $HOME/john/john/run/zip2john tero.zip >tero.zip.hash viedäkseni salasanan hash tiedon omaan tiedostoonsa.

![kuva](https://github.com/user-attachments/assets/cb0d9fe4-d57b-4620-8c9b-5bf67428dfa0)

Tiiviste oli saatu irti. Seuraavaksi päästin Johnin vauhtiin komennolla '$HOME/john/john/run/john tero.zip.hash'

Salasanaksi paljastui butterfly

![kuva](https://github.com/user-attachments/assets/b2dc9c24-f245-4a61-93f5-e79f1fac725e)

Katsoin salasanan määrittävän lipun unzip komennolle komennolla 'man unzip | grep password' ja näin sen olevan -P

Yritin siis komentoa 'unzip -P butterfly tero.zip'

Uusi kansio ilmestyi ja sen sisällä oli tiedosto SECRET.md.

Tutkin sen sisällön 'cat' komennolla ja totesin tehtävän onnistuneen.

![kuva](https://github.com/user-attachments/assets/cb0ace01-93e2-4600-b2e8-d1f7beec88e4)

# c) Fuffme

## Maalin asentaminen

Asensin ensin kaikki paketit ja kloonasin repositorion.

Seuraavaksi loin uuden kontin komennolla sudo docker build -t ffufme .
Tässä komennossa 'build' luo kuvan Dockerfilestä ja -t vie sen rakennustilan muotoon "build" tai lisää sille tagin. ('docker --help' ja 'docker build --help')

![kuva](https://github.com/user-attachments/assets/ac4258b0-1be7-49ed-9293-c53ec611ec66)

Kontin kuvan luominen onnistui.

Seuraavaksi ajoin kontin komennolla 'sudo docker run -d -p 80:80 ffufme'

Tässä komennossa käsittääkseni '-d' lippu irrottaa kontin ajettavaksi taustalle, lisäten sille host koneen ja '-p 80:80' lippu avaa HTML portit nähtäväksi host koneelle  

![kuva](https://github.com/user-attachments/assets/4bca9cc0-e7b2-4a28-8e1d-75bba6bc5818)

Koneen luonti onnistui.

Latasin vielä kaikki ohjeessa olevat sanalistat kansioon $HOME/wordlists.

Tämän jälkeen irrotin virtuaalikoneeni verkosta ja yritin ohjeessa olevaa komentoa.

![kuva](https://github.com/user-attachments/assets/b4b64459-051a-457d-860e-876877368c5c)

Kone oli pois verkosta, mutta localhost löytyi vielä.

Ajoin komennon 'ffuf -w $HOME/wordlists/common.txt -u http://ffuf.me/cd/basic/FUZZ'

Tämä kuitenkin aiheutti virheen. Muuttaessani ffuf.me osan localhost muotoon, kohteet löytyivät.

![kuva](https://github.com/user-attachments/assets/90e5e1b1-57c5-41e4-aed1-4f3429f8299b)

## Harjoitukset

### Basic

Ensimmäinen tehtävä tuli jo tietämättäni tehtyä edellisessä osassa.

### Recursion

Tehtävässä tehtiin erillinen haku, jossa aina uuden hakemiston löytäessä ffuf aloitti uuden haun jokaista kansiota kohti, kunnes uusia kansioita ei enää löytynyt.
Tein haun komennolla 'ffuf -w $HOME/wordlists/common.txt -recursion -u http://localhost/cd/recursion/FUZZ'

Tällä löytyi hakemistot /admin, /admin/users ja /admin/users/96

![kuva](https://github.com/user-attachments/assets/e4139a2a-4e1c-4db0-8222-95c28dee0899)























 
















