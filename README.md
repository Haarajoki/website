# Haarajoen asukasyhdistys ry – verkkosivut

Haarajoen asukasyhdistyksen staattinen verkkosivusto. Rakennettu [Hugo](https://gohugo.io/)-sivugeneraattorilla, sisällönhallinta [Decap CMS](https://decapcms.org/):llä ja julkaisu [GitHub Pages](https://pages.github.com/) -palvelussa.

**Tuotantosivusto:** https://haarajoki.fi

## Miten sivusto toimii

Sivusto on täysin staattinen – se koostuu pelkästä HTML:stä, CSS:stä ja pienestä määrästä JavaScriptiä. Taustajärjestelmää (backend) ei ole.

Sisällöntuottajat muokkaavat sivustoa Decap CMS -käyttöliittymässä osoitteessa `haarajoki.fi/admin`. CMS tallentaa muutokset Markdown-tiedostoina suoraan tähän Git-repositorioon. Jokainen tallennus luo automaattisesti Git-commitin, joka käynnistää GitHub Actions -työnkulun: Hugo kääntää Markdown-tiedostot staattiseksi HTML:ksi ja julkaisee sivuston GitHub Pagesiin.

```
Sisällönmuokkaus (Decap CMS)
        ↓
Git commit → GitHub
        ↓
GitHub Actions: hugo --minify
        ↓
GitHub Pages (haarajoki.fi)
```

## Paikallinen kehitys

### Vaatimukset

- [Hugo](https://gohugo.io/installation/) (extended-versio, v0.152.2 tai uudempi)
- [Git](https://git-scm.com/)

### Asennus ja käynnistys

```bash
git clone git@github.com:Haarajoki/website.git
cd website
hugo server
```

Kehityspalvelin käynnistyy osoitteeseen http://localhost:1313. Hugo seuraa tiedostomuutoksia automaattisesti ja päivittää selaimen.

### Rakentaminen

```bash
hugo              # Kääntää sivuston public/-kansioon
hugo --minify     # Tuotantokäännös (minifoitu)
```

`public/`-kansio on gitignoressa – se luodaan aina uudelleen käännösvaiheessa.

## Projektin rakenne

```
├── content/                    Sivuston sisältö (Markdown)
│   ├── _index.md               Etusivu
│   ├── ajankohtaista/          Uutiset ja tiedotteet
│   ├── yhdistys.md             Yhdistyssivu
│   ├── werso.md                Kylätalo Werso
│   ├── yhteystiedot.md         Yhteystiedot
│   └── ...                     Muut sisältösivut
├── themes/haarajoki/           Sivuston teema
│   ├── layouts/                Hugo-templateit
│   └── static/css/style.css    Tyylitiedosto
├── static/
│   ├── admin/                  Decap CMS -konfiguraatio
│   └── favicon.svg             Favicon
├── .github/workflows/
│   └── deploy.yml              GitHub Actions -julkaisutyönkulku
└── hugo.toml                   Hugon asetukset ja valikot
```

## Sisällönhallinta (CMS)

Decap CMS on käytettävissä osoitteessa `haarajoki.fi/admin`. Kirjautuminen tapahtuu GitHub-tunnuksilla.

CMS:stä voi muokata:

- **Ajankohtaista** – uutisten ja tiedotteiden lisäys, muokkaus ja poisto
- **Sivut** – kaikkien sisältösivujen muokkaus (etusivu, yhdistys, Werso, yhteystiedot, yritykset jne.)

CMS-konfiguraatio sijaitsee tiedostossa `static/admin/config.yml`.

## Julkaisu

Julkaisu tapahtuu automaattisesti: `main`-haaraan pushattu muutos käynnistää GitHub Actions -työnkulun, joka kääntää ja julkaisee sivuston.

Työnkulku (`.github/workflows/deploy.yml`):

1. Asentaa Hugon (v0.152.2, extended)
2. Ajaa `hugo --minify`
3. Julkaisee `public/`-kansion GitHub Pagesiin

Manuaalista julkaisua ei tarvita.

### Mukautettu verkkotunnus

GitHub Pages -asetuksissa on konfiguroitu verkkotunnus `haarajoki.fi`. DNS-asetukset osoittavat GitHubin palvelimille.
