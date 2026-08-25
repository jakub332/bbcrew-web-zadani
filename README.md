# BBCREW — Zadání webu (pro vývojáře)

> **Verze:** 1.0 · 25. 8. 2026
> **Kontakt za klienta:** Jakub Richter — jakubrichter332@gmail.com
> **Cíl:** Jednostránkový web pro BBCREW (@burgerbycrew) — smash burger pop-up crew z Prahy. Dvě hlavní úlohy: (1) přivést lidi na Instagram a na eventy, (2) sbírat **poptávky na catering** přes rezervační formulář.

---

## 1. O značce (kontext)

- BBCREW = „3 kluci · 1 grill · totální smash chaos". Pop-up crew na festivalech a akcích v Praze.
- Nejsou to „burgrárna", je to **crew** — brand stojí na energii party, ne na korporátní gastro estetice.
- Instagram: [@burgerbycrew](https://instagram.com/burgerbycrew)
- Web musí vizuálně 1:1 sedět na brand manuál (viz sekce 2). Brand manuál PDF: `bbcrew-brand-manual.pdf` (dodá Jakub spolu s tímto zadáním).

---

## 2. Vizuální identita (závazné)

### Barvy

| Název | HEX | Použití | Poměr |
|---|---|---|---|
| Electric Purple | `#5E17EB` | značka, plochy hero/CTA | 30 % |
| Off-White | `#F6F4F0` | hlavní pozadí ploch | 55 % |
| Ink | `#17121F` | text, tmavá sekce TOUR | 8 % |
| Lilac | `#C9B4F5` | tinty, jemné pozadí | 4 % |
| Cheese | `#FFC42E` | POUZE bodový akcent (hover, badge, zvýraznění) | 3 % |

Nikdy nepoužívat jiné barvy. Cheese žlutá nesmí tvořit plochy — jen detaily.

### Typografie (Google Fonts)

| Role | Font | Použití |
|---|---|---|
| Display | **Archivo Black** | nadpisy, VERSÁLKY, těsný prostrkávání (letter-spacing ~ -0.02em) |
| Body | **Archivo** (400/600) | běžný text |
| Utility | **IBM Plex Mono** | data, štítky, ceny, eyebrow labely, formulářové popisky |

### Logo

- Custom lettering „BBCREW" — dodá Jakub jako SVG. Do té doby nadpis sadit v Archivo Black.
- Ochranná zóna = výška písmene B na všech stranách. Min. šířka 120 px na obrazovce.
- Nenaklánět, nedeformovat, neměnit barvu mimo paletu, žádné stíny/obrysy.
- Varianty: fialová na off-white, off-white na fialové, off-white na fotce.

### Signature prvky

1. **Marquee pás** (nekonečně rotující text): `Smashed in Prague · BBCREW · Burger by Crew · ` — použít jako oddělovač mezi sekcemi (CSS animace, pauza při `prefers-reduced-motion`).
2. **Šachovnicový vzor** (purple/off-white, jako papír na burger) — okraje fotek v galerii, drobné dekorativní pásy.
3. **Kruhová samolepka / razítko** „SMASHED IN PRAGUE · BURGER BY CREW" — pomalu rotující badge v hero (SVG text na kružnici).

---

## 3. Struktura stránky (one-page + kotvy)

Pořadí sekcí odshora dolů. Texty níže jsou **finální copy** — nasadit tak, jak jsou.

### 3.1 Hero (fialová plocha `#5E17EB`)

- Obří nápis **BBCREW** (off-white), pod ním tagline v IBM Plex Mono:
  `3 KLUCI · 1 GRILL · TOTÁLNÍ SMASH CHAOS`
- Rotující samolepka-badge (signature prvek 3).
- Dvě CTA: **„Kde nás potkáš"** (kotva na TOUR) a **„Chci vás na akci"** (kotva na Catering).
- Pod hero marquee pás.

### 3.2 O crew (off-white)

Eyebrow: `KDO JSME` (mono)

> **Nejsme burgrárna. Jsme crew.**
> Tři kluci, jeden grill a smash burgery, který nezvedáš — ty zvedají tebe. Jezdíme po festivalech, dvorcích a akcích po Praze. Kde stojí náš gril, tam je fronta.

- Foto: `post-film.png` (polaroid crew fotka) — orámovaná šachovnicí.

### 3.3 Menu (off-white / lilac tint)

Eyebrow: `CO SMASHUJEME`

| Položka | Popis |
|---|---|
| **CLASSIC SMASH** | Hovězí, čedar, cibule, smash omáčka, máslová bulka |
| **DOUBLE TROUBLE** | Dvakrát maso, dvakrát čedar, žádný kompromisy |
| **SÝROVEJ ZÁTAH** | Speciál. Sýr teče, ty brečíš štěstím |
| **HRANOLKY** | Čerstvě řezaný, dvakrát smažený |

- Ceny na webu **neuvádět** — mění se dle akce. Pod menu řádek v mono: `CENY NA MÍSTĚ · KAŽDÁ AKCE JE JINÁ`
- Foto: `post-syr.png` (cheese pull) vedle menu.

### 3.4 TOUR (tmavá sekce `#17121F`, text off-white)

Eyebrow: `TOUR 2026`

- Seznam eventů jako řádky v IBM Plex Mono: `DATUM — NÁZEV AKCE — MÍSTO`.
- Data eventů: **needitovat v kódu** — viz sekce 5 (správa obsahu).
- Když není žádný nadcházející event, zobrazit: `DALŠÍ ZASTÁVKY BRZY. SLEDUJ @BURGERBYCREW` s odkazem na IG.
- Cheese žlutá jako akcent na nejbližší event (badge `NEJBLÍŽ`).

### 3.5 Galerie „ON FILM" (off-white)

Eyebrow: `ON FILM`

- 4–8 fotek, šachovnicové rámečky, lehce rotované (±2°) jako vylepené fotky.
- Startovní fotky dodá Jakub: `foto-styl.png`, `post-syr.png`, `post-hranolky.png`, `post-film.png` (další doplní klient z akcí).
- Pod galerií CTA: **„Víc na Instagramu →"** (odkaz @burgerbycrew).

### 3.6 Catering + rezervační systém (fialová plocha) — **KLÍČOVÁ SEKCE, viz kapitola 4**

### 3.7 Patička (Ink)

- Logo, IG odkaz, e-mail, marquee pás.
- Řádek: `© 2026 BBCREW · SMASHED IN PRAGUE` + odkaz na zásady ochrany osobních údajů (viz 4.6).

---

## 4. Rezervační systém — catering

### 4.1 Princip

Eyebrow: `CHCEŠ NÁS NA SVOJÍ AKCI?`

> **Grilujeme i pro tebe.**
> Firemní akce, párty, svatba, festival. Přijedeme, postavíme gril a smashneme to.

**Základní pravidlo:** catering pro **5 a více lidí se musí objednat dopředu** a vždy proběhne **konzultace** (krátký call), kde se doladí menu, kapacita a cena. Web nesbírá platby — sbírá **poptávky**.

Do 4 lidí se nic nerezervuje — člověk prostě přijde na pop-up. Web ho v tom případě nasměruje na sekci TOUR.

### 4.2 Krok 1 — počet lidí (branching)

První pole formuláře: **„Pro kolik lidí?"** (number input / stepper, min 1).

- **1–4 lidi** → formulář se schová a zobrazí se friendly hláška:
  > `Na tohle nepotřebuješ rezervaci. Přijď na nejbližší pop-up a burger máš do ruky.`
  - - tlačítko **„Kde nás potkáš"** (kotva na TOUR).
- **5+ lidí** → rozbalí se plný poptávkový formulář (krok 2).

### 4.3 Krok 2 — poptávkový formulář (5+ lidí)

| Pole | Typ | Povinné | Validace |
|---|---|---|---|
| Jméno a příjmení | text | ✅ | min. 3 znaky |
| E-mail | email | ✅ | formát e-mailu |
| Telefon | tel | ✅ | CZ/SK formát, +420/+421 tolerantně |
| Datum akce | datepicker | ✅ | viz pravidla níže |
| Místo akce | text (město/adresa) | ✅ | — |
| Počet lidí | number (přenesené z kroku 1, editovatelné) | ✅ | ≥ 5 |
| Typ akce | select: Firemní akce / Soukromá párty / Svatba / Festival · trh / Jiné | ✅ | — |
| Poznámka | textarea | ❌ | max 1000 znaků |
| Souhlas se zpracováním OÚ | checkbox | ✅ | musí být zaškrtnut |

**Pravidla pro datum (lead time) — validovat na frontendu I backendu:**

| Počet lidí | Minimální předstih |
|---|---|
| 5–49 | **7 dní** od dneška |
| 50+ | **14 dní** od dneška |

Datepicker dřívější data zablokuje. Když uživatel zvýší počet lidí a datum přestane vyhovovat, zobrazit inline hlášku: `Pro akci nad 50 lidí potřebujeme aspoň 14 dní. Vyber pozdější termín.`

**Kapacitní tiers** (jen informační badge vedle pole počtu lidí, žádná další logika):

- 5–30 → `STANDARD SETUP`
- 31–100 → `VELKÁ AKCE — 2 GRILY`
- 100+ → `MEGA. DOMLUVÍME INDIVIDUÁLNĚ`

### 4.4 Krok 3 — po odeslání

1. **Thank-you stav** (nahradí formulář, žádný redirect):
   > `Poptávka je u nás. Do 24 hodin se ti ozveme a domluvíme konzultaci — krátký call, kde doladíme menu, kapacitu a cenu.`
2. **E-mail crew** (interní notifikace) na adresu BBCREW (❗ doplní Jakub — placeholder `TODO@burgerbycrew.cz`): předmět `🍔 Nová poptávka: {datum} · {počet} lidí · {typ akce}`, v těle všechna pole.
3. **Auto-potvrzení zákazníkovi**: rekapitulace poptávky + věta o konzultaci do 24 h + odkaz na IG. Odesílatel „BBCREW".
4. Volitelně (nice-to-have): do potvrzovacího e-mailu vložit odkaz na rezervaci termínu konzultace (Calendly/Cal.com) — link dodá klient, připravit jako konfigurační proměnnou.

### 4.5 Technické požadavky na formulář

- **Backend validace** všech polí (frontend nestačí) — počet lidí, lead time, e-mail.
- **Anti-spam:** honeypot pole + rate limit (např. 5 odeslání / IP / hodinu). Bez CAPTCHA, ať to neubíjí konverzi.
- Odeslání přes vlastní API endpoint (serverless function stačí) → e-maily přes transactional službu (Resend / Postmark / SMTP — dle preference vývojáře).
- Chybové stavy: srozumitelné CZ hlášky u polí, ne alerty. Při pádu odeslání: `Nepovedlo se odeslat. Zkus to znovu, nebo nám napiš rovnou na Instagram.` + odkaz.
- Poptávky ideálně zároveň ukládat (Google Sheet / DB / e-mail stačí jako minimum) — ať se nic neztratí.

### 4.6 GDPR

- Checkbox se souhlasem + krátká stránka/modal „Ochrana osobních údajů": kdo data zpracovává (BBCREW), za jakým účelem (vyřízení poptávky), jak dlouho (do vyřízení + max 1 rok), kontakt pro výmaz.
- Data neposílat žádným třetím stranám kromě e-mailingu poptávky.

---

## 5. Správa obsahu (eventy)

Eventy v sekci TOUR se mění často — **nesmí** být zadrátované v HTML. Minimální řešení dle stacku:

- statický web → eventy v jednom JSON/YAML souboru (`events.json`: `date`, `name`, `place`, `link?`), klient pošle změny Jakubovi;
- lepší varianta → napojit na jednoduchý headless zdroj (Google Sheet / Notion / CMS), ať si crew edituje eventy sama.

Eventy s datem v minulosti se automaticky skryjí (nebo přesunou do „PROBĚHLO" — volitelné).

---

## 6. Technický rámec

- **Stack:** dle preference vývojáře — jde o one-page + 1 API endpoint. Doporučení: Astro/Next.js + serverless funkce; ale klidně i čisté HTML/CSS/JS + malý backend. Žádný heavy framework kvůli jedné stránce.
- **Hosting:** cokoliv s HTTPS a custom doménou (Vercel/Netlify/VPS). Doménu zajistí klient (návrh: `bbcrew.cz` — ověřit dostupnost).
- **Výkon:** Lighthouse 90+ mobile. Fotky ve WebP/AVIF, lazy-load mimo hero.
- **Responzivita:** mobile-first (většina trafficu půjde z IG bia). Breakpointy dle uvážení.
- **Přístupnost:** viditelný keyboard focus, kontrast textů dle WCAG AA, `prefers-reduced-motion` vypne marquee/rotace, formulář s labely a `aria-describedby` u chyb.
- **SEO/meta:** title `BBCREW — Smash burgery · Praha`, description, OG obrázek (dodá Jakub), favicon z loga.
- **Analytika:** jednoduchá cookieless (Plausible/Umami) — event tracking na odeslání formuláře a kliky na IG.
- **Jazyk:** pouze čeština.

---

## 7. Podklady (ve složce `assets/` tohoto repozitáře)

| Soubor | Účel |
|---|---|
| `bbcrew-brand-manual.pdf` | závazná vizuální identita |
| `foto-styl.png` | galerie / hero foto (burger v šachovnicovém papíru) |
| `post-syr.png` | menu / galerie (cheese pull „Sýrovej zátah") |
| `post-hranolky.png` | galerie (hranolky v kuchyni) |
| `post-film.png` | sekce O crew (polaroid) |
| logo SVG | dodá klient dodatečně (do té doby Archivo Black lettering) |

❗ **Před spuštěním doplnit od klienta:** e-mail pro poptávky, doménu, případně Calendly link.

---

## 8. Akceptační kritéria

1. Web vizuálně odpovídá brand manuálu (barvy, fonty, poměry, logo pravidla).
2. Formulář: 1–4 lidi → přesměrování na TOUR; 5+ → plný formulář; lead-time pravidla fungují na frontendu i backendu.
3. Po odeslání dorazí interní notifikace i auto-potvrzení zákazníkovi; poptávka je uložena.
4. Honeypot + rate limit aktivní; nevalidní vstupy backend odmítne.
5. Eventy jdou změnit bez zásahu do kódu.
6. Lighthouse mobile: Performance/Accessibility/SEO ≥ 90.
7. Web funguje na mobilu od 360 px šířky, `prefers-reduced-motion` respektováno.
