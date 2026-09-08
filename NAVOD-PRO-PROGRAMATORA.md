# BBCREW — web · návod pro programátora

Kompletní předávací dokumentace k nasazení webu do produkce (návrh domény: **bbcrew.cz**).

---

## 1. Co to je

Statický jednostránkový web pro **BBCREW** (IČO 24863246, Sokolova 720/7, 180 00 Praha 8 – Karlín) — smash burger crew s vlastní restaurací v Karlíně, pop-upy na festivalech a cateringem.

- **Stack:** čisté HTML + CSS + vanilla JS, **žádný build step, žádné závislosti, žádný framework**
- **1 soubor:** `index.html` (~1 000 řádků, CSS i JS inline) + složka `assets/` (fotky, menu, hero video)
- Externí zdroje: Google Fonts + Google Maps embed (bez API klíče)
- `nahled.html` = identická kopie `index.html` (historický alias, držet v sync nebo smazat)

## 2. Kde web žije teď

| Co | Kde |
|---|---|
| Zdroj pravdy (lokální) | `~/Desktop/Claude & Jakub/BBCREW/web/` |
| Git repo | `https://github.com/jakub332/bbcrew-web-zadani` (public, větev `main`) |
| Dočasný hosting | GitHub Pages: `https://jakub332.github.io/bbcrew-web-zadani/` |
| Lokální náhled | `npx http-server -p 8765` v kořeni repa (⚠️ `python3 -m http.server` nestreamuje hero video — neumí Range requesty) |
| Brand manuál | `assets/bbcrew-brand-manual.pdf` |

**Úkol: napojit rezervace (viz §6) a dostat web na doménu.**

## 3. Struktura stránky (sekce & kotvy)

| Pořadí | Sekce | ID kotvy | Obsah |
|---|---|---|---|
| 1 | Sticky nav | — | objeví se po odscrollování hero; Menu · Tour · Rezervace · IG |
| 2 | Hero | `#top` | video pozadí (`assets/hero.mp4` + poster) pod fialovým overlay, logo, žlutý rotující sticker, CTA Menu / Rezervuj stůl / Kde nás potkáš |
| 3 | Marquee | — | běžící pás „Smashed in Prague" |
| 4 | Crew | `#crew` | „Nejsme burgrárna. Jsme crew." + foto v šachovnicovém rámu |
| 5 | Menu | `#menu` | **ceník 1:1 z podkladů** (7 kategorií, tečkované řádky) + rotující stack fotek + odkaz na `assets/menu.jpg` |
| 6 | Tour | `#tour` | eventy z pole `EVENTS` v JS — proběhlé se škrtají samy, nejbližší dostává badge „Nejblíž" |
| 7 | Galerie | `#film` | 6 fotek v nakloněných šachovnicových rámech |
| 8 | Rezervace | `#catering` | taby **Stůl v restauraci / Catering na akci** + klikací Google mapa |
| 9 | Patička | — | odkazy, adresa, IČO 24863246 |

## 4. Design tokeny (NEMĚNIT bez souhlasu — brand manuál v assets)

```css
--purple:  #5E17EB;   /* Electric Purple — hlavní */
--offwhite:#F6F4F0;   /* pozadí */
--ink:     #17121F;   /* text, tmavé sekce */
--lilac:   #C9B4F5;   /* světlý akcent */
--lilac-tint:#EAE2FB; /* pozadí menu */
--cheese:  #FFC42E;   /* žlutá — JEN akcent (sticker, CTA hover, marquee) */
```

**Fonty (Google Fonts):** nadpisy `Archivo Black` uppercase · text `Archivo` · popisky/labels `IBM Plex Mono` letter-spacing ~0.14–0.22em.

**Podpisové prvky:** šachovnicové pruhy a foto-rámy (`repeating-conic-gradient`), nakloněné fotky (±2°), pill tlačítka, žlutý kruhový sticker v hero. Zachovat všude.

**Breakpointy:** 1000 px (mapa pod formulář), 960 px (menu 3→2 sloupce), 760 px (1 sloupec).

## 5. JavaScript — co dělá

Vše vanilla JS na konci `index.html`:

| Funkce | Implementace |
|---|---|
| Tour | pole `EVENTS` (`{date, name, place}`) → řazení, auto-škrtání proběhlých, badge nejbližší |
| Hero video | při chybě načtení se `#heroMedia` odstraní → fialový fallback |
| Taby rezervace | `segTable`/`segCat` přepínají `#tableForm` / `#cateringForm` |
| Formulář Stůl | validace (jméno, telefon ≥9 číslic, datum ≥ dnes, čas, 1–20 lidí, souhlas) → `sendToBackend()` + shrnutí + Zkopírovat/IG DM |
| Formulář Catering | <5 lidí → hláška „rezervuj si stůl"; ≥5 → validace, lead time 7/14 dní dle počtu, tier badge → `sendToBackend()` + shrnutí |
| `sendToBackend()` | POST JSON na `FORM_ENDPOINT`; když není nastavený nebo selže, zůstává IG DM fallback |
| Ostatní | scroll reveal (IntersectionObserver), rotující foto stack, sticky nav, honeypot `website` |

## 6. Rezervační systém (priorita č. 1)

Kompletní architektura vč. hotového kódu Cloudflare Workeru: **`REZERVACE-ARCHITEKTURA.md`**.

TL;DR: nasadit Worker (§5–6 tamtéž) → notifikace do **Telegram skupiny crew**; e-mail přes Resend se dopojí doplněním dvou proměnných, **až adresa vznikne**. Na webu pak stačí:

```js
var FORM_ENDPOINT = 'https://bbcrew-rezervace.<subdoména>.workers.dev';
var CONTACT_EMAIL = 'rezervace@bbcrew.cz';   // až bude — zobrazí i tlačítko „Poslat e-mailem“
```

## 7. Checklist pro produkci (dle priority)

### 7.1 Rezervace (kritické)
- [ ] Telegram bot + skupina (návod §3 architektury — zvládne i crew)
- [ ] Cloudflare Worker nasadit, nastavit `TG_TOKEN`, `TG_CHAT`, `ALLOWED_ORIGIN`
- [ ] `FORM_ENDPOINT` do `index.html`, otestovat oba formuláře
- [ ] až bude e-mail: Resend + `RESEND_KEY`/`TO_EMAIL` + `CONTACT_EMAIL` na webu

### 7.2 Doména (návrh bbcrew.cz)
GitHub Pages (zdarma): Settings → Pages → Custom domain `bbcrew.cz`; DNS: `A` → `185.199.108.153`, `.109.`, `.110.`, `.111.`; `www` CNAME → `jakub332.github.io`; Enforce HTTPS. Poté přepsat `ALLOWED_ORIGIN` ve Workeru a URL v meta tazích (`og:url`, `canonical`, JSON-LD).

### 7.3 Obsah
- [ ] Ostrá data eventů v `EVENTS` (teď ukázková)
- [ ] Otevírací doba restaurace (doplnit do resto-info boxu + JSON-LD `openingHours`) — **od klienta**
- [ ] Logo v SVG (teď textové/emoji favicon) — **od klienta**
- [ ] Zásady zpracování OÚ (formuláře sbírají osobní údaje; zpracovatelé: Cloudflare, Telegram, Resend)

### 7.4 SEO & výkon
- [ ] OG image: vytvořit kompozit 1200×630 z `assets/foto-double-cheese.jpg` (teď se používá starší foto)
- [ ] Favicon sada z loga (32/180/512 px + webmanifest) — teď inline SVG emoji
- [ ] `robots.txt` + `sitemap.xml`, Google Search Console + **Google Business Profile** (restaurace!)
- [ ] Fotky: převést na WebP (`foto-*.jpg` mají 200–390 kB, WebP ušetří ~50 %), přidat `width`/`height`
- [ ] Hero video má jen 376 kB — OK, neřešit

## 8. Jak dělat úpravy (rychlé recepty)

**Přidat akci do Tour** — v JS poli `EVENTS`:
```js
{ date: '2026-10-17', name: 'HALLOWEEN SMASH', place: 'KARLÍN' },
```
(řazení, škrtání i badge se dopočítají samy)

**Změnit cenu/položku v menu:** sekce `#menu`, řádky `<li><span class="mi">…</span><span class="dots"></span><span class="mp">CENA</span></li>`. Při změně menu vyměnit i `assets/menu.jpg` (odkaz „menu ke stažení").

**Vyměnit fotku:** nahradit soubor v `assets/` (JPEG, delší strana ≤1600 px) a případně `alt` text.

**Vyměnit hero video:** `ffmpeg -i vstup.mov -an -c:v libx264 -crf 26 -movflags +faststart -pix_fmt yuv420p assets/hero.mp4` + nový poster `ffmpeg -ss 2 -i assets/hero.mp4 -frames:v 1 assets/hero-poster.jpg`. Svislé video ořezat z černých pruhů (`cropdetect`).

**Deploy:** `git add -A && git commit -m "…" && git push` → Pages se přegeneruje do ~1 min. (`index.html` a `nahled.html` držet identické: `cp index.html nahled.html`.)

## 9. Co v produkci NEDĚLAT

- Neměnit barvy/fonty/šachovnici — nová identita dle brand manuálu (assets/bbcrew-brand-manual.pdf)
- Nemazat IG DM fallback formulářů — je to pojistka pro výpadek Workeru
- Nepřidávat framework/build — síla webu je v jednoduchosti (1 soubor + assets)
- Neměnit ceny v menu bez podkladu od crew

---
*Podklad připravil Jakub Richter · září 2026 · zdroj: repo `jakub332/bbcrew-web-zadani`*
