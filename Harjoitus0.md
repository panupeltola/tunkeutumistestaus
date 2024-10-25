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

GET pyyntö on selaimen pyyntö näyttää sivulta löytyvää dataa. (https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET)

Avaamalla ensimmäisen pyynnön lisätiedot, näen GET pyynnön statuksen, sekä vastauksen tiedot

![kuva](https://github.com/user-attachments/assets/7e1d7e99-b5f6-4b28-8760-ddca15f0ad27)

GET pyynnön vastauksesta voin päätellä käytetyn protokollan (HTTP 1.1) sekä pyynnön statuksen olevan 200 OK, eli pyyntöön on vastattu onnistuneesti.









