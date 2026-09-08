# BBCREW — architektura rezervačního systému

Cíl: rezervace stolu i poptávky cateringu z webu chodí crew **okamžitě do Telegramu** (na mobil) a **na e-mail, jakmile bude existovat** — bez placeného serveru, bez databáze, bez údržby.

---

## 1. Přehled — jak to celé funguje

```
 návštěvník webu (GitHub Pages / bbcrew.cz)
        │
        │  vyplní formulář (Stůl / Catering)
        │  JS validace → sendToBackend() → POST JSON
        ▼
 ┌──────────────────────────────┐
 │  Cloudflare Worker (zdarma)  │   ← FORM_ENDPOINT v index.html
 │  · CORS jen pro náš web      │
 │  · honeypot + validace       │
 │  · rate limit                │
 └──────┬──────────────┬────────┘
        │              │
        ▼              ▼ (jakmile bude e-mail)
 Telegram Bot API   Resend API
 zpráva do skupiny  e-mail na CONTACT_EMAIL
 „BBCREW Rezervace" (kopie rezervace)
        │
        ▼
 crew na mobilu do 3 sekund 📱
```

**Fallback:** dokud `FORM_ENDPOINT` není nastavený (nebo požadavek selže), web funguje jako teď — vygeneruje shrnutí a pošle hosta do IG DM. Nic se nerozbije.

## 2. Proč takhle

| Varianta | Cena | Telegram | E-mail | Verdikt |
|---|---|---|---|---|
| **Cloudflare Worker** | 0 Kč (100k req/den) | ✅ nativně | ✅ přes Resend | ✅ **doporučeno** |
| Formspree free | 0 Kč (50/měs.) | ❌ (jen přes Zapier, platí se) | ✅ | ❌ bez Telegramu |
| Vlastní VPS/PHP | od ~100 Kč/měs. | ✅ | ✅ | zbytečná údržba |

Worker je jeden soubor JavaScriptu, nasazuje se zkopírováním do Cloudflare dashboardu. Žádný server, žádné aktualizace.

## 3. Telegram bot — setup (10 minut, udělá kdokoli z crew)

1. V Telegramu napsat **@BotFather** → `/newbot` → jméno např. `BBCREW Rezervace` → uložit **bot token** (`123456:ABC-…`).
2. Založit skupinu **„BBCREW Rezervace"**, pozvat všechny tři kluky + bota.
3. Napsat do skupiny cokoli, pak otevřít `https://api.telegram.org/bot<TOKEN>/getUpdates` → z odpovědi opsat `chat.id` (záporné číslo, např. `-100987654321`).
4. Token a chat_id se vloží do Workeru jako proměnné `TG_TOKEN` a `TG_CHAT` (Settings → Variables → **Encrypt**).

> Bot píše jen do téhle skupiny — crew má všechny rezervace na jednom místě a vidí, kdo na co reaguje.

## 4. E-mail — napojí se, jakmile bude adresa

- Služba: **Resend** (resend.com) — free tier 3 000 e-mailů/měs., stačí navždy.
- Setup: registrace → ověřit doménu (až bude bbcrew.cz) nebo použít jejich testovací doménu → API klíč.
- Ve Workeru se doplní proměnné `RESEND_KEY` + `TO_EMAIL`. **Kód je na to připravený — dokud proměnné nejsou, Worker e-mail prostě přeskočí a posílá jen Telegram.** Žádná změna kódu později.

## 5. Kód Workeru (kompletní, copy-paste)

```js
// BBCREW rezervace — Cloudflare Worker
// Proměnné (Settings → Variables): TG_TOKEN, TG_CHAT, volitelně RESEND_KEY, TO_EMAIL, ALLOWED_ORIGIN

const CORS = (env) => ({
  'Access-Control-Allow-Origin': env.ALLOWED_ORIGIN || '*',
  'Access-Control-Allow-Methods': 'POST, OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type',
});

const czDate = (iso) => iso ? iso.split('-').reverse().join('. ') : '—';

function formatMsg(d) {
  if (d.typ === 'stul') {
    return `🍔 REZERVACE STOLU\n` +
      `👤 ${d.jmeno}\n📞 ${d.telefon}\n` +
      `📅 ${czDate(d.datum)} v ${d.cas}\n` +
      `👥 ${d.pocet} lidí` +
      (d.poznamka ? `\n📝 ${d.poznamka}` : '');
  }
  return `🎪 POPTÁVKA CATERINGU\n` +
    `👤 ${d.jmeno}\n📞 ${d.telefon}\n✉️ ${d.email}\n` +
    `📅 ${czDate(d.datum)} · ${d.misto}\n` +
    `👥 ${d.pocet} lidí · ${d.typAkce}` +
    (d.poznamka ? `\n📝 ${d.poznamka}` : '');
}

export default {
  async fetch(request, env) {
    if (request.method === 'OPTIONS')
      return new Response(null, { status: 204, headers: CORS(env) });
    if (request.method !== 'POST')
      return new Response('Method Not Allowed', { status: 405 });

    let d;
    try { d = await request.json(); }
    catch { return Response.json({ ok: false }, { status: 400, headers: CORS(env) }); }

    // honeypot — boti vyplní skryté pole, tváříme se, že prošlo
    if (d.website) return Response.json({ ok: true }, { headers: CORS(env) });

    // minimální validace
    if (!['stul', 'catering'].includes(d.typ) || !d.jmeno || !d.telefon)
      return Response.json({ ok: false }, { status: 400, headers: CORS(env) });

    const text = formatMsg(d);

    // 1) Telegram — hlavní kanál
    const tg = await fetch(`https://api.telegram.org/bot${env.TG_TOKEN}/sendMessage`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ chat_id: env.TG_CHAT, text }),
    });

    // 2) E-mail — jen pokud je nakonfigurovaný (RESEND_KEY + TO_EMAIL)
    if (env.RESEND_KEY && env.TO_EMAIL) {
      await fetch('https://api.resend.com/emails', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json', Authorization: `Bearer ${env.RESEND_KEY}` },
        body: JSON.stringify({
          from: 'BBCREW web <rezervace@bbcrew.cz>',   // po ověření domény v Resend
          to: [env.TO_EMAIL],
          reply_to: d.email || undefined,
          subject: d.typ === 'stul'
            ? `Rezervace stolu — ${d.jmeno} (${czDate(d.datum)} ${d.cas})`
            : `Poptávka cateringu — ${d.jmeno} (${d.pocet} lidí)`,
          text,
        }),
      }).catch(() => {}); // e-mail nesmí shodit rezervaci
    }

    return Response.json({ ok: tg.ok }, { headers: CORS(env) });
  },
};
```

## 6. Nasazení Workeru (15 minut)

1. Účet na **dash.cloudflare.com** (zdarma) → Workers & Pages → **Create Worker** → pojmenovat `bbcrew-rezervace`.
2. Vložit kód z §5 → **Deploy**.
3. Settings → Variables and Secrets: `TG_TOKEN`, `TG_CHAT` (obojí Encrypt), `ALLOWED_ORIGIN` = `https://jakub332.github.io` (později `https://bbcrew.cz`). Volitelně později `RESEND_KEY`, `TO_EMAIL`.
4. Zkopírovat URL workeru (`https://bbcrew-rezervace.<subdoména>.workers.dev`).
5. V `index.html` nastavit:
   ```js
   var FORM_ENDPOINT = 'https://bbcrew-rezervace.<subdoména>.workers.dev';
   ```
6. Otestovat oba formuláře → zpráva musí přijít do Telegram skupiny.

**Frontend je už hotový** — funkce `sendToBackend()` v `index.html` posílá JSON přesně ve formátu, který Worker čeká (`typ: 'stul' | 'catering'` + pole). Po úspěšném odeslání web hostovi zobrazí „odesláno, ozveme se", při selhání spadne zpět na IG DM flow.

## 7. Payload — kontrakt mezi webem a Workerem

```jsonc
// typ „stul"
{ "typ": "stul", "jmeno": "…", "telefon": "…", "datum": "2026-09-20",
  "cas": "18:00", "pocet": 4, "poznamka": "…" }

// typ „catering"
{ "typ": "catering", "pocet": 40, "jmeno": "…", "email": "…", "telefon": "…",
  "datum": "2026-10-01", "misto": "…", "typAkce": "Firemní akce",
  "poznamka": "…", "website": "" }   // website = honeypot, musí zůstat prázdné
```

## 8. Bezpečnost & limity

- **Honeypot** `website` — skryté pole, boti ho vyplní → tiše zahodit (hotovo na obou stranách)
- **CORS** — `ALLOWED_ORIGIN` omezit na doménu webu, ať endpoint nejde volat odjinud z prohlížeče
- **Rate limit** — volitelně Cloudflare WAF rule (např. max 10 req/min/IP) v dashboardu, bez kódu
- **GDPR** — data se nikde neukládají, jen se přepošlou (Telegram + e-mail); do zásad OÚ uvést Cloudflare, Telegram a Resend jako zpracovatele

## 9. Budoucí rozšíření (až bude potřeba)

1. **Potvrzovací e-mail hostovi** — druhý `resend` call na `d.email`
2. **Google Kalendář** — Worker může přes Calendar API rovnou blokovat termíny
3. **Tlačítka v Telegramu** („✅ Potvrdit / ❌ Obsazeno") — bot webhook, odpověď hostovi SMS/e-mailem
4. **Admin přehled** — ukládat rezervace do Cloudflare KV a číst přes jednoduchou admin stránku

---
*Podklad připravil Jakub Richter · září 2026 · repo `jakub332/bbcrew-web-zadani`*
