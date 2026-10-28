# Minutnik T1

Minutnik interwałowy jako aplikacja instalowana na telefonie (PWA): własna ikona na ekranie głównym, pełny ekran bez paska przeglądarki, działa offline, nie wygasza ekranu w trakcie treningu.

## Pliki

| Plik | Rola |
| --- | --- |
| `index.html` | cała aplikacja (HTML, CSS, JS) |
| `manifest.webmanifest` | nazwa, ikony, kolory, tryb pełnoekranowy, skróty |
| `sw.js` | tryb offline; zmień `VERSION` przy każdym wdrożeniu |
| `icons/` | ikony aplikacji (SVG + PNG 180/192/512, maskable) |

## Uruchomienie lokalne

```bash
npx serve .
# albo
python3 -m http.server 8080
```

Service worker działa tylko na `https://` albo `localhost`.

## Wdrożenie (dowolny hosting statyczny z HTTPS)

- **GitHub Pages**: wrzuć pliki do repozytorium → Settings → Pages → Deploy from branch `main`, folder `/`.
- **Netlify / Cloudflare Pages / Vercel**: przeciągnij folder albo podepnij repozytorium, bez kroku budowania.
- **Ten sam serwer co T1**: skopiuj pliki np. do `/minutnik/`.

## Instalacja na telefonie

- **Android (Chrome)**: otwórz adres → menu ⋮ → *Zainstaluj aplikację*.
- **iPhone (Safari)**: Udostępnij → *Do ekranu początkowego*.

## Otwieranie z aplikacji T1

Przycisk stopera w T1 otwiera adres minutnika. Można przekazać gotowy schemat albo konkretne czasy z planu:

```
https://twoj-adres/minutnik/?preset=tabata
https://twoj-adres/minutnik/?work=45&rest=15&rounds=6&sets=2&setRest=90&prep=10&cooldown=0
```

Parametry (sekundy lub liczby): `prep`, `work`, `rest`, `rounds`, `sets`, `setRest`, `cooldown`.
Schematy: `tabata`, `hiit`, `emom`, `boks`, `bieg`. Pominięte parametry zostają z ostatnich ustawień.

`plan` przyjmuje tekst rozpiski i sam go odczytuje, `title` ustawia podpis pod nagłówkiem:

| Rozpiska | Ustawienie |
| --- | --- |
| `Tabata` | 8 × 20 s / 10 s |
| `EMOM 12` | 12 × 60 s |
| `8x 40/20` | 8 × 40 s / 20 s |
| `6 x 3min/1min, 2 serie` | 2 serie po 6 × 3 min / 1 min |
| `10x1'/1'` | 10 × 1 min / 1 min |

Rozpiska bez rozpoznanego schematu (np. `plyo/siłka`) otwiera minutnik z ostatnimi ustawieniami.

### Przycisk stopera w T1

```js
const MINUTNIK_URL = 'https://twoj-adres/minutnik/';

function otworzMinutnik(trening) {
  const q = new URLSearchParams();
  if (trening.rozpiska) q.set('plan', trening.rozpiska);
  q.set('title', [trening.dyscyplina, trening.data].filter(Boolean).join(' · '));
  window.open(MINUTNIK_URL + '?' + q, '_blank', 'noopener');
}
```

## Wersja na Google Play (opcjonalnie)

Po wdrożeniu na HTTPS można z tej samej aplikacji zrobić plik APK/AAB przez
[PWABuilder](https://www.pwabuilder.com/) albo Bubblewrap (Trusted Web Activity) — bez przepisywania kodu.
