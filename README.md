# Lumină inteligentă: aplicație compatibilă Stream v5

Această revizie păstrează aspectul, camerele și autentificarea aplicației existente. Modifică avertizările sondei și permite comenzi consecutive fără blocarea tuturor butoanelor până la confirmarea serverului.

## Instalare

Înlocuiește pe găzduirea/GitHub Pages existentă aceste fișiere:

- `index.html`
- `style.css`
- `app.js`
- `sw.js`
- `icon.svg`
- `manifest.webmanifest`

Nu publica `config.h` al ESP-ului. Setările deja salvate în același browser/origin folosesc aceeași cheie locală și nu sunt șterse de acest update. Sesiunea Firebase continuă să folosească `browserLocalPersistence`; navigarea privată, ștergerea datelor browserului sau deconectarea explicită pot necesita autentificare din nou.

După înlocuire, închide și redeschide aplicația/reîncarcă pagina. Dacă instalarea PWA afișează încă vechiul mesaj „Bandă oprită” la eroare de sondă fără protecție activă, verifică actualizarea fișierelor și service worker-ul. Nu șterge inutil datele site-ului, deoarece asta elimină configurația și sesiunea locală.

## Comenzi și afișare

- **Comenzi:** `releu`, `led_on`, `led_brightness` 1–5; fiecare acțiune trimite doar câmpul schimbat.
- **Latență UI:** nu mai există blocarea globală a butoanelor până la ACK de la server. Selectarea nivelului deja activ nu mai face o scriere.
- **Stare dorită:** modificarea imediată a comutatorului din Firebase local reprezintă cererea utilizatorului, nu confirmarea că ESP-ul a acționat.
- **Stare efectivă:** `led_efectiv` permite afișarea „ÎN AȘTEPTARE” dacă s-a cerut ON dar ieșirea nu este încă ON; „BLOCAT” este rezervat unei protecții active.
- **Senzor invalid:** apare avertizarea că banda nu este oprită automat dacă firmware-ul raportează lipsa blocării; ultima temperatură nu este prezentată drept citire curentă.
- **Supraîncălzire:** păstrează prioritatea avertizării de 70/50 °C, inclusiv dacă sonda devine indisponibilă după declanșare.
- **Firmware vechi:** dacă acesta raportează blocare la eroare de senzor, aplicația încă poate arăta corect că banda este oprită de acel firmware.

Aplicația asculta deja Firebase prin `onValue`; înlocuirea polling-ului cu stream se face în firmware. Indicatorul online și vechimea temperaturii necesită în continuare timestamp-urile mici publicate de ESP.

## Administrator și client

Configurația, linkul pentru client, camerele și rolurile existente sunt păstrate. Clientul deschide linkul primit și se conectează cu email/parolă; nu trebuie să introducă Firebase config.

Fișierul `database.rules.example.json` este doar un exemplu moștenit, nu o regulă instalată automat. Nu înlocui regulile funcționale ale proiectului fără verificarea UID-urilor și permisiunilor. În acest exemplu toți utilizatorii autentificați pot citi nodul `luci` și scrie comenzile lui; nu oferă izolare între mai mulți clienți în același proiect.

Ascunderea meniului administrativ în interfață nu este control de acces. Marcajul local de dispozitiv administrator din versiunea veche este păstrat pentru compatibilitate; autorizarea reală trebuie impusă în Firebase Rules.

## Limitări și siguranță

Noua politică a firmware-ului nu oprește banda la sondă lipsă/invalidă. Fără o citire validă nu poate detecta supraîncălzirea; folosește o protecție termică hardware pentru o instalație care poate prezenta risc.

Testele de UI s-au făcut cu Firebase simulat, la 1.280 px și 375 px. Nu reprezintă o confirmare a autentificării reale, a regulilor proiectului, a latenței radio sau a stabilității pe 24 h.
