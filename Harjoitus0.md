# Harjoitus 0

# Ympäristö

## Fyysinen ympäristö

- CPU: Intel i7-8665U CPU @ 1.90GHz
- 16 GB Ram
- Windows 11 Pro 23H2

## Virtuaaliympäristö:

- Oracle VM Virtualbox
- Linux Kali
-  8 GB ram
-  4 prosessoriydintä

## Liikenteen kappaaminen

Päätin seurata liikennettä Firefoxin sisään rakennetulla seurantatoiminnolla. Sivuna käytin kurssisivua "https://terokarvinen.com/tunkeutumistestaus/"

![kuva](https://github.com/user-attachments/assets/956ce117-eb73-4c08-af62-154d5e973d84)

Vastaukseksi sain yllä olevan kuvan, joista nopeasti katsottuna voin päätellä, että kaikki selaimeni tekemät tai ainakin näyttämät toiminnot ovat GET pyyntöjä.

Sivujen domainina toimii käytännössä kaikissa terokarvinen.com. Suurin osa muista oli linkkejä, jotka sivulta löytyy.
Liikenteestä näkee myös mitä kaikkia protokollia ja ladattavien osien tyypit, sekä mitä tiedostomuotoa tai ohjelmointikieltä ne käyttävät.

GET pyyntö on selaimen pyyntö näyttää sivulta löytyvää dataa. (https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET)

Tästä näen myös ladattujen aineistojen koot. Suurin osa tiedosta on cached, oletettavasti johtuen siitä, että olin ladannut sivun jo aiemmin.

## Pyynnön sisällä

Avaamalla ensimmäisen pyynnön lisätiedot, näen GET pyynnön statuksen, sekä vastauksen tiedot

![kuva](https://github.com/user-attachments/assets/7e1d7e99-b5f6-4b28-8760-ddca15f0ad27)

GET pyynnön vastauksesta voin päätellä käytetyn protokollan (HTTP 1.1) sekä pyynnön statuksen olevan 200 OK, eli pyyntöön on vastattu onnistuneesti.
Ainut poikkeus oli https://gc.zgo.at/count.js, jonka GET vastauksen status oli 304.
Tämä vastaus tarkoitti, että ulkoisesta palvelusta uudelleen ohjattavaa tietoa ei ole muokattu, eikä sitä tarvitse ladata uudelleen  

## Muuta

Totesin myös, että Firefoxin verkon seuranta ei näytä portteja tai ainakaan en niitä tässä ajassa löytänyt.

# Lähteet:

- Mozzilla Corporation, Get, https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET
- Mozzilla Corporation, 304 Not Modified, https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/304?utm_source=mozilla&utm_medium=devtools-netmonitor&utm_campaign=default














