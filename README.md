# Haarajoen asukasyhdistys ry – verkkosivut

Haarajoen asukasyhdistyksen staattinen verkkosivusto. Rakennettu [Hugo](https://gohugo.io/)-sivugeneraattorilla, sisällönhallinta [Sveltia CMS](https://github.com/sveltia/sveltia-cms):llä ja julkaisu [GitHub Pages](https://pages.github.com/) -palvelussa.

**Tuotantosivusto:** https://haarajoki.fi

## Miten sivusto toimii

Sivusto on täysin staattinen – se koostuu pelkästä HTML:stä, CSS:stä ja pienestä määrästä JavaScriptiä. Taustajärjestelmää (backend) ei ole.

Sisällöntuottajat muokkaavat sivustoa Sveltia CMS -käyttöliittymässä osoitteessa `haarajoki.fi/admin`. CMS tallentaa muutokset Markdown-tiedostoina suoraan tähän Git-repositorioon. Jokainen tallennus luo automaattisesti Git-commitin, joka käynnistää GitHub Actions -työnkulun: Hugo kääntää Markdown-tiedostot staattiseksi HTML:ksi ja julkaisee sivuston GitHub Pagesiin.

```
Sisällönmuokkaus (Sveltia CMS)
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
│   ├── admin/                  Sveltia CMS -konfiguraatio
│   └── favicon.svg             Favicon
├── .github/workflows/
│   └── deploy.yml              GitHub Actions -julkaisutyönkulku
└── hugo.toml                   Hugon asetukset ja valikot
```

## Sisällönhallinta (CMS)

Sisällönhallinta on käytettävissä osoitteessa https://haarajoki.fi/admin. Käytössä on [Sveltia CMS](https://github.com/sveltia/sveltia-cms), joka käyttää samaa `config.yml`-tiedostoa kuin Decap CMS. Kirjautuminen tehdään GitHubin henkilökohtaisella pääsytunnisteella (personal access token), joten erillistä kirjautumispalvelinta ei tarvita ja ratkaisu on maksuton.

### Kirjautuminen ensimmäistä kertaa

Tarvitset GitHub-tunnuksen, jolla on kirjoitusoikeus repositorioon `Haarajoki/website`. Pyydä oikeus yhdistyksen GitHub-ylläpitäjältä.

1. Kirjaudu GitHubiin ja avaa **Settings → Developer settings → Personal access tokens → Fine-grained tokens** (suora linkki: https://github.com/settings/personal-access-tokens/new).
2. Valitse **Generate new token** ja täytä:
   - **Token name:** esim. `Haarajoki CMS`
   - **Expiration:** pisin sallittu aika (esim. 1 vuosi)
   - **Resource owner:** `Haarajoki`
   - **Repository access:** *Only select repositories* → `Haarajoki/website`
   - **Permissions → Repository permissions → Contents:** *Read and write*
3. Valitse **Generate token** ja kopioi tunniste (alkaa `github_pat_`). GitHub näyttää sen vain kerran.
4. Avaa https://haarajoki.fi/admin, valitse **Sign In with Token** ja liitä tunniste.

Selain muistaa kirjautumisen, joten tunnistetta ei tarvitse syöttää joka kerta. Kun tunniste vanhenee, luo uusi samalla tavalla.

**Tietoturva:** Pidä tunniste salassa kuten salasana. Älä kirjaudu yhteiskäyttöisellä tietokoneella. Jos tunniste pääsee vääriin käsiin, poista se GitHubissa kohdasta *Fine-grained tokens*.

### Käyttöoikeuksien hallinta (ylläpitäjille)

Tunnisteella pääsee tekemään vain sen, mihin tunnisteen luoneella GitHub-käyttäjällä on oikeus. Kuka tahansa voi avata `/admin`-sivun, mutta muutoksia voi tallentaa vain käyttäjä, jolla on kirjoitusoikeus repositorioon.

- **Uusi sisällöntuottaja:** lisää käyttäjä GitHub-organisaatioon `Haarajoki` ja anna hänelle *Write*-oikeus repositorioon `Haarajoki/website`.
- **Oikeuden poisto:** poista käyttäjä repositoriosta tai organisaatiosta. Hänen tunnisteensa lakkaa toimimasta heti.
- Organisaation asetuksissa (*Settings → Personal access tokens*) voi vaatia, että ylläpitäjä hyväksyy jokaisen uuden tunnisteen ennen käyttöä.

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
