Tehtävänanto osa Tero Karvisen pitämää kurssia [Tunkeutumistestaus](https://terokarvinen.com/tunkeutumistestaus/#h7-hakkeroimaan-oppii-hakkeroimalla)

# x) Lue/katso ja tiivistä

Päätin lukea EA. Altulaihanin, A. Alismailin ja M. Frikhan Electronics lehdessä vuonna 2023 julkaiseman artikkelin A Survey on Web Application Penetration Testing
Julkaisu on Julkaisufoorumin arvioiden perusteella tason 1 lähde

![kuva](https://github.com/user-attachments/assets/fd46411e-1085-4d73-8cdf-78dfeb9c3959)


Artikkeli on yli 20 sivua pitkä, joten kävin artikkelin läpi silmäilemällä.

- Verkkosovellusten haavoittuvuudet ovat kasvussa ja niistä monet johtuvat huonosta validoinnista ja syötteen suodattamisesta.
- Artikkelin tarkoitus on käydä läpi työkaluja, joilla kehittäjät voivat testata omia sovelluksiaan

## 1 Introduction

- Haavoittuvuuksien eliminointi verkkosovelluksissa on haastavaa niiden nopean kehittymisen ja muuttumisen takia
- Tunkeutumistestauksella voidaan varmistaa sovelluksen turvamekanismien toimivuus
- Tunkeutumistestauksella voidaan määrittää kohteen haavoittuvuus ja tämän tiedon pohjalta voidaan verrata onko yrityksen vastatoimet riittäviä
- Tunkeutumistestauksen tärkein tavoite on kouluttaminen ja haavoittuvuuksien löytäminen sekä yrityksen prosesseissa, että sovelluksissa
- Tunkeutumistestausta voi olla automatisoitua ja manuaalista
- Manuaalinen testaus on tarkempaa, mutta hitaampaa ja kalliimpaa

## 2 Methology

- Artikkelissa on käytetty tutkimuksia ajalta 1/2018-12/2022


## 3 Literature Review

Alla on viitattu numeroilla artikkelissa viitattuihin läheisiin niiden jäljittämisen helpottamiseksi.

1. Kirjallisuudessa ehdotettu automaattista framework mallia verkkopalveluiden testaamiseen, joka koostuisi integroiduista ulkoisista työkaluista.
- Tämä sallisi nopean testaamisen muuttuvassa ympäristössä.
5. XSS hyökkäykset voidaan välttää hyvällä ohjelmointitavalla, automaattisilla työkaluilla, manuaalisella koodin tarkastuksella, sekä OWASP Security Shepardilla.
8. Automaattisten työkalujen testaamisessa on havaittu, että ne eivät aina havaitse luotettavasti tai laisinkaan mahdollisia haavoittuvuuksia.
- Automaattiseen testaamiseen tarvitaan useampia työkaluja sekä manuaalista arviointia.
11. Testin mukaan 80% testatuista sivuista oli haavoittuvaisia SQL injektiolle
15. Esitetty systeemiä, joka voisi skannata ja tunnistaa haavoittuvuudet verkkosivuilta käyttäen hyväksi Pythoinin moduuleja, kuten Djangoa.
- Skannerin nimi on SCANSCX


## 4 Web App Vulnerabilities

- Lomakkeiden tekstikentät ja napit sekä HTTP protokollan komennot ovat yleisiä hyökkäyspintoja.
- Käytiin läpi OWASP top 10 haavoittuvuudet, joita en ala luettelemaan tai tiivistämään
- Käytiin läpi SQL injektion estotapoja, jotka olivat sen verran sekavia, etten niitä ymmärtänyt, kuten SQL kyselyn entropian laskeminen ja varmentaminen
- Session kaappaamista on suojattu IP-osoitteen sitomisella tokeniin, mutta tässä on ongelmana, että jos kutsu tulee saman NAT alueen sisältä, ei osoitteella voi eritellä hyökkääjää ja oikeaa liikennettä.
- Toinen tapa on seurata käyttäjän selaimen liikkeitä ja sormenjkälkeä.
- XSS suojauksessa on väärän ja haitallisen syötteen suojaaminen tai hylkääminen tärkeää
- OWASPilla on projekti CSRFGuard


## 5 Web penetration test

- Artikkelissa oli hyvä lista eri hyökkäystyökaluista eri tarkoituklsiin:

![kuva](https://github.com/user-attachments/assets/1675738c-b223-4374-a530-cbefdb1e3059)

![kuva](https://github.com/user-attachments/assets/cd3d968c-ea0b-469d-87c4-139fe58e8537)

- Kappaleessa käytiin läpi näitä ohjelmia

## 6 - 9

- Lopputuloksena oli, että työkaluja on paljon ja niitä pitää käyttää ristiin hyvän lopputuloksen saavuttamiseksi
- Käsin testaaminen on hidasta ja kehitettävä verkkoympäristö muuttuu nopeasti

Lähteet:

T. Karvinen, 2024, Tunkeutumistestaus, https://terokarvinen.com/tunkeutumistestaus/#h7-hakkeroimaan-oppii-hakkeroimalla, Luettu 11.12.2024
JUFO-Portaali, Electronics, https://jfp.csc.fi/jufoportaali?Jufo_ID=84608, luettu 11.12.2024
EA. Altulaihan, A. Alismail ja M. Frikha, 2023, A Survey on Web Application Penetration Testing, https://www.mdpi.com/2079-9292/12/5/1229, Luettu 11.12.2024
