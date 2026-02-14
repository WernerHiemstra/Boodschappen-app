# Boodschappen-app — Requirements Document

## Overzicht
Een single-page webapplicatie voor het beheren van boodschappenlijsten en recepten met realtime synchronisatie tussen apparaten. De app is mobile-first, werkt offline, en synchroniseert automatisch via Firebase Realtime Database.

---

## 1. Boodschappenlijsten

### 1.1 Meerdere lijsten
- Gebruiker kan meerdere boodschappenlijsten aanmaken
- Elke lijst heeft een naam, unieke ID, en creatie-timestamp
- Lijsten kunnen hernoemd worden
- Lijsten kunnen verwijderd worden (met waarschuwing als er items in staan)
- Actieve lijst wordt onthouden bij herladen
- Lijstselectie via dropdown in header (klikbare lijstnaam)

### 1.2 Items toevoegen
- Invoerveld onderaan met auto-complete suggesties
- Smart parser die input analyseert:
  - `"melk x2"` of `"melk 2x"` → hoeveelheid 2
  - `"500g gehakt"` of `"500 g gehakt"` → 500 gram
  - `"2 kg aardappelen"` → 2 kilogram
  - `"3 appels"` → 3 stuks
  - `"tomaten (roma)"` → notitie "roma"
  - `"melk x2 (houdbaar)"` → hoeveelheid 2, notitie "houdbaar"
- Ondersteunde eenheden: stuks, gram, kg, ml, liter, eetlepel, theelepel, snuf, bos, blik, pak, zak
- Items krijgen automatisch een categorie toegewezen op basis van CategoryMap
- Enter-toets voegt item toe
- Invoerveld wordt geleegd na toevoegen

### 1.3 Suggesties
- Suggesties verschijnen tijdens typen (vanaf 1 karakter)
- Suggesties zijn afkomstig van:
  - Eerder toegevoegde items (item history)
  - Standaardproducten uit CategoryMap (100+ producten)
- Suggesties gesorteerd op frequentie (meest gebruikte eerst)
- Tonen productnaam, categorie-emoji en categorienaam
- Navigatie met pijltjestoetsen (↑↓)
- Enter-toets selecteert gemarkeerde suggestie
- Escape-toets sluit suggesties

### 1.4 Items beheren
- Items tonen naam, hoeveelheid + eenheid, notitie (indien aanwezig)
- Items hebben een categorie-badge (klikbaar)
- Items kunnen afgevinkt worden (toggle completed state)
- Afgevinkte items:
  - Worden getoond in aparte "Afgevinkt" sectie onderaan
  - Sectie is inklapbaar/uitklapbaar
  - Tonen aantal afgevinkte items
  - Blijven bewaard tot verwijderd
- Items hebben een three-dots menu met acties:
  - "Hoeveelheid aanpassen" → modal met input
  - "Verwijderen" → verwijdert item met undo-toast

### 1.5 Categorieën
- Standaard categorieën met emoji's:
  - 🥬 Groente & Fruit
  - 🥩 Vlees & Vis
  - 🥛 Zuivel & Eieren
  - 🍞 Brood & Bakkerij
  - 🥫 Conserven & Sauzen
  - 🍝 Pasta, Rijst & Peulvruchten
  - 🧂 Kruiden & Specerijen
  - 🍫 Snacks & Snoep
  - 🧴 Verzorging & Huishouden
  - 🍷 Dranken
  - 🧊 Overig
- Categorie-badge klikken opent categorie-keuzemodal
- Gekozen categorie wordt opgeslagen als override voor die productnaam
- Override geldt voor toekomstige items met dezelfde naam op alle lijsten

### 1.6 Groeperen op categorie
- Toggle-knop in header: groepeer items per categorie
- Gegroepeerde weergave toont categorie-headers met emoji's
- Categorieën zonder items worden niet getoond
- Categorie-volgorde is aanpasbaar (drag-and-drop modal)
- Custom volgorde wordt per lijst opgeslagen
- Knop "Categorievolgorde aanpassen" verschijnt alleen bij groepering

---

## 2. Recepten

### 2.1 Recepten maken/beheren
- Gebruiker kan recepten aanmaken, bewerken en verwijderen
- Elk recept heeft:
  - Naam (verplicht)
  - Aantal porties (standaard 4)
  - Lijst van ingrediënten met naam, hoeveelheid en eenheid
  - Foto-URL (optioneel, Unsplash-URL's)
  - Unieke ID en creatie-timestamp
- Recepten-overzicht toont alle recepten als cards met foto, naam en aantal porties/ingrediënten
- Klikken op recept opent receptweergave
- "+" knop in header (alleen op overzichtspagina) maakt nieuw recept
- Modal voor recept maken/bewerken:
  - Invoer: receptnaam, aantal porties, foto-URL
  - Ingrediënten toevoegen met naam, hoeveelheid, eenheid
  - Ingrediënten verwijderen via "×" knop
  - "Opslaan" valideer dat receptnaam niet leeg is
  - "Verwijder" knop (alleen in edit-modus) met bevestiging

### 2.2 Standaard recepten
- Bij eerste gebruik worden 8 standaard recepten geladen:
  1. Pasta Carbonara (4 porties, 6 ingrediënten)
  2. Tomatensoep (4 porties, 8 ingrediënten)
  3. Kip Teriyaki met Rijst (4 porties, 8 ingrediënten)
  4. Griekse Salade (4 porties, 8 ingrediënten)
  5. Pannenkoeken (4 porties, 5 ingrediënten)
  6. Chili con Carne (6 porties, 10 ingrediënten)
  7. Caesarsalade met Kip (2 porties, 7 ingrediënten)
  8. Stamppot Boerenkool (4 porties, 6 ingrediënten)
- Elk standaard recept heeft een vaste Unsplash-foto-URL
- Standaard recepten kunnen bewerkt/verwijderd worden zoals custom recepten

### 2.3 Receptweergave
- Toont receptfoto (groot), naam, aantal porties
- Lijst van ingrediënten met hoeveelheden
- Foto kan aangepast worden via klik → modal met URL-invoer
- Porties aanpassen via +/- knoppen:
  - Herberekent alle ingrediënthoeveelheden proportioneel
  - Desiredservings wordt tijdelijk bewaard (niet gesynchroniseerd)
- "Alles selecteren/deselecteren" toggle voor ingrediënten
- Ingrediënten individueel aan/uit vinken
- Geselecteerde ingrediënten bewaren (tijdelijk, niet gesynchroniseerd)
- "Toevoegen aan [Lijstnaam]" knop:
  - Voegt geselecteerde ingrediënten toe aan actieve boodschappenlijst
  - Ingrediënten krijgen categorie van CategoryMap
  - Toast met aantal toegevoegde ingrediënten + "Bekijk lijst" actie
  - Bekijk lijst → switcht naar boodschappen-tab
- Terug-knop (chevron links) → terug naar receptenoverzicht
- Three-dots menu → "Recept bewerken"

### 2.4 Weekmenu
- Recepten kunnen gemarkeerd worden als:
  - Niet in weekplan (standaard)
  - "planned" (gepland voor deze week)
  - "eaten" (deze week gegeten)
- Weekplan-toggle per recept (bord-met-bestek icoon):
  - Eerste klik → planned (blauw)
  - Tweede klik → eaten (groen)
  - Derde klik → verwijderd uit weekplan
- Weekplan filter in receptenoverzicht:
  - "Alle recepten" (standaard)
  - "Weekmenu (X gepland, Y gegeten)" (alleen als er weekplan-items zijn)
- Filter bewaart tijdelijke voorkeur (niet gesynchroniseerd, reset bij tab-switch)
- "Weekplan resetten" knop in header (alleen bij overzicht + weekplan-items):
  - Verwijdert alle weekplan-markeringen met bevestiging

---

## 3. Firebase Realtime Sync

### 3.1 Share-link systeem
- URL bevat share-ID in hash: `#share=ml9eyqmjmqqbbnml9eyqmj011so5`
- Share-ID wordt automatisch gegenereerd bij eerste bezoek (2× genId())
- Share-ID wordt gebruikt als Firebase Realtime Database path: `shares/{shareId}`
- Zelfde share-link = zelfde data op alle apparaten
- "Deel link kopiëren" knop in header:
  - Kopieert volledige URL naar clipboard
  - Toast bevestiging

### 3.2 Gesynchroniseerde data
- SHARED_KEYS (gesynchroniseerd tussen apparaten):
  - `lists` — alle boodschappenlijsten met items
  - `recipes` — alle recepten
  - `weekplan` — weekmenu-status per recept
  - `itemHistory` — frequentie van toegevoegde items (voor suggesties)
  - `categoryOverrides` — custom categorie-keuzes per productnaam
- NIET gesynchroniseerd (lokaal per apparaat):
  - `activeListId` — welke lijst actief is
  - `activeRecipeId` — welk recept open is
  - `showAllRecipes` — receptenoverzicht vs. recept-detail
  - `activeTab` — boodschappen vs. recepten tab
  - `groupByCategory` — groepering aan/uit
  - `_completedOpen` — afgevinkte sectie ingeklapt/uitgeklapt
  - Recept-view tijdelijke state (porties, geselecteerde ingrediënten, weekfilter)

### 3.3 Synchronisatie-gedrag
- Firebase initialisatie bij app-start
- Initiële `pull()` van Firebase-data bij startup (async)
- Realtime `listen()` voor live updates van andere apparaten
- Elke `State.update()` → automatisch `push()` naar Firebase (300ms debounce)
- Connection status indicator (oranje "Offline" badge bij geen verbinding)
- Bij permission-denied: console.warn, app blijft werken in local-only mode
- Array/object normalisatie na Firebase round-trip (RTDB slaat arrays op als objecten)

---

## 4. Data Persistence

### 4.1 localStorage
- Key: `boodschappen_v1`
- Volledige state wordt opgeslagen bij elke wijziging
- localStorage wordt geladen bij app-start (fallback voor offline/geen Firebase)
- localStorage wordt overschreven door Firebase-data na initiële pull + merge

### 4.2 State management
- Centraal State-object met immutable updates via `State.update(fn)`
- Update flow: `fn(state)` → `Store.save(state)` → `Sync.push(state)` (indien enabled)
- State integrity checks bij init en na Firebase merge:
  - `lists` moet array zijn met minimaal 1 lijst
  - `itemHistory`, `categoryOverrides`, `weekplan` moeten objecten zijn
  - `recipes` moet array zijn
  - Elk `list.items` moet array zijn
  - Firebase array→object conversie wordt teruggedraaid via `Object.values()`

---

## 5. UI/UX Features

### 5.1 Navigatie
- Tab bar onderaan met twee tabs:
  - 🛒 Boodschappen
  - 🍳 Recepten
- Tab-switch zet `activeTab`, reset recept-state, rendert nieuwe content
- Header met:
  - Titel (klikbaar bij boodschappen-tab → lijstkeuze)
  - Acties (context-afhankelijk)
- Command Palette (Cmd+K / Ctrl+K):
  - Zoeken in lijsten en recepten
  - Enter → selecteer item (switch lijst of open recept)
  - Escape → sluit palette

### 5.2 Modals
- Lijst bewerken: hernoem, verwijder
- Lijst aanmaken
- Recept maken/bewerken
- Recept verwijderen (bevestiging)
- Receptfoto aanpassen
- Hoeveelheid aanpassen (item)
- Categorie kiezen (item)
- Categorie-volgorde aanpassen (drag-and-drop lijst)
- Weekplan reset (bevestiging)
- Backdrop click → sluit modal
- Escape-toets → sluit modal
- Auto-focus op eerste inputveld

### 5.3 Toast notificaties
- Verschijnen onderaan (boven tab bar)
- Auto-dismiss na 4 seconden
- Optionele actie-knop (bijv. "Ongedaan maken")
- Voorbeelden:
  - "Link gekopieerd"
  - "[Item] verwijderd" + Ongedaan maken
  - "[N] ingrediënten toegevoegd aan [Lijst]" + Bekijk lijst
  - "Recept bijgewerkt"
  - "[Item] → [Categorie]"

### 5.4 Responsive design
- Mobile-first layout
- Safe area support (iOS notch, Android navigation)
- Sticky header met blur-effect
- Sticky add-bar onderaan
- Scrollable main content area
- Touch-friendly hit targets (min 44px)
- Smooth transitions en animaties

### 5.5 Empty states
- Lege boodschappenlijst: emoji, titel, beschrijving met voorbeeld
- Geen recepten: emoji, titel, uitleg over "+" knop
- Geen zoekresultaten: passende melding

---

## 6. CategoryMap — Standaardproducten

### 6.1 Productdatabase
- 100+ veelvoorkomende boodschappen met standaard categorieën
- Voorbeelden per categorie:
  - **Groente & Fruit**: aardappel, appel, banaan, tomaat, ui, knoflook, paprika, komkommer, wortel, sla, citroen, avocado, broccoli, bloemkool, courgette, aubergine, spinazie, champignons, prei, selderij, rode kool, witte kool, andijvie, rucola, peterselie, basilicum, koriander, munt, tijm, rozemarijn
  - **Vlees & Vis**: gehakt, kipfilet, biefstuk, spek, worst, zalm, tonijn, kabeljauw, garnalen
  - **Zuivel & Eieren**: melk, yoghurt, kaas, boter, eieren, room, kwark, karnemelk, slagroom, roomkaas, mozzarella, feta, parmezaan
  - **Brood & Bakkerij**: brood, volkoren, witbrood, croissant, baguette, pistolet
  - **Conserven & Sauzen**: tomatenpuree, tomatensaus, pesto, mayonaise, ketchup, mosterd, sojasaus, honing, jam, pindakaas, notenpasta
  - **Pasta, Rijst & Peulvruchten**: pasta, spaghetti, penne, rijst, linzen, kikkererwten, bruine bonen, witte bonen
  - **Kruiden & Specerijen**: zout, peper, paprikapoeder, komijnpoeder, currypoeder, kaneel, nootmuskaat, oregano, basilicum
  - **Snacks & Snoep**: chips, chocolade, koekjes, noten, rozijnen
  - **Verzorging & Huishouden**: tandpasta, shampoo, zeep, wasmiddel, afwasmiddel, toiletpapier, keukenpapier
  - **Dranken**: water, sap, wijn, bier, koffie, thee, frisdrank
- Gebruikt voor auto-complete suggesties en automatische categorie-toekenning

### 6.2 Categorievolgorde
- Standaard volgorde (gebruikt tenzij lijst custom volgorde heeft):
  1. Groente & Fruit
  2. Vlees & Vis
  3. Zuivel & Eieren
  4. Brood & Bakkerij
  5. Conserven & Sauzen
  6. Pasta, Rijst & Peulvruchten
  7. Kruiden & Specerijen
  8. Snacks & Snoep
  9. Verzorging & Huishouden
  10. Dranken
  11. Overig
- Per lijst aanpasbaar via "Categorievolgorde aanpassen" modal
- Custom volgorde wordt opgeslagen in `list.categoryOrder`

---

## 7. Technische Requirements

### 7.1 Platform
- Single-page webapplicatie (HTML + CSS + JavaScript)
- Geen build process, geen externe dependencies (behalve Firebase SDK)
- Werkt in alle moderne browsers (Chrome, Firefox, Safari, Edge)
- Progressive Web App features (optioneel):
  - Installeerbaar op mobiel
  - Apple mobile web app capable

### 7.2 Dependencies
- Firebase JavaScript SDK v10.12.0:
  - `firebase-app-compat.js`
  - `firebase-database-compat.js`
- Geen andere externe libraries

### 7.3 Firebase configuratie
- Firebase Realtime Database
- Aanbevolen rules voor productie:
  ```json
  {
    "rules": {
      "shares": {
        "$shareId": {
          ".read": true,
          ".write": true
        }
      }
    }
  }
  ```
- Database locatie: België (europe-west1) of dichtstbijzijnde regio

### 7.4 Browser storage
- localStorage voor offline persistence
- Geen cookies
- Geen IndexedDB
- Session state in memory (niet persistent)

---

## 8. Edge Cases & Error Handling

### 8.1 Firebase errors
- Permission denied → console.warn + werk in local-only mode
- Network offline → offline indicator + gebruik localStorage
- Write failed → console.warn, data blijft lokaal beschikbaar
- Pull/listen errors → error callback vangt af

### 8.2 Data corruption
- `state.lists` geen array → normaliseer via `Object.values()`
- `state.recipes` geen array → normaliseer via `Object.values()`
- `list.items` geen array of undefined → normaliseer of fallback naar `[]`
- `getActiveList()` retourneert null → UI toont "Boodschappen" fallback
- Leeg recipename bij opslaan → validatie blokkeert opslaan
- Negatieve porties → min-knop disabled bij 1 portie

### 8.3 User input
- Lege invoer bij item toevoegen → wordt genegeerd
- Parser herkent geen eenheid → default naar "stuks"
- Ongeldige hoeveelheid → fallback naar 1
- Escape-toets in modals → sluit modal
- Enter-toets in inputs → submit actie (toevoegen, opslaan)

### 8.4 Sync conflicts
- Laatste write wint (Firebase behavior)
- Geen merge-conflict resolutie
- mergeRemote() overschrijft lokale shared keys blind
- Lokale UI-state blijft behouden (activeTab, etc.)

---

## 9. State Initialization Flow

Bij app-start:
1. Laad state uit localStorage OF gebruik defaults
2. Valideer state integrity:
   - Zorg dat lists, recipes arrays zijn
   - Zorg dat itemHistory, categoryOverrides, weekplan objecten zijn
   - Laad 8 standaard recepten als recipes leeg is
   - Zet activeTab op 'boodschappen'
   - Reset activeRecipeId naar null (altijd lijst-view na reload)
3. Als Firebase enabled:
   - Initiële `Sync.pull()` en merge remote data
   - Start `Sync.listen()` voor realtime updates
4. Render UI: `UI.renderAll()`
5. Focus op add-input

---

## 10. Non-Functional Requirements

### 10.1 Performance
- App laadt in < 2 seconden op 3G
- UI-updates instant (< 100ms)
- Firebase debounce: 300ms voor writes
- Smooth 60fps animaties

### 10.2 Accessibility
- ARIA labels op alle interactieve elementen
- Tab-navigation werkend
- Enter/Escape keyboard shortcuts
- aria-selected voor tabs
- role="tab", role="listbox" etc. waar van toepassing

### 10.3 Design
- iOS-inspired design system
- SF Pro Display font stack (fallback naar system fonts)
- Kleurenpalet:
  - Accent: #007aff (iOS blauw)
  - Groen: #34c759
  - Rood: #ff3b30
  - Oranje: #ff9500
  - Grijs tints voor text/backgrounds
- Border radius: 8-20px
- Shadows: subtiel, 3 levels (sm, md, lg)
- Spacing: 4px grid (xs=4, sm=8, md=16, lg=24, xl=32)

---

## 11. Out of Scope (NIET implementeren)

- User accounts / authenticatie
- Receptinstructies (alleen ingrediënten)
- Boodschappen sorteren op winkelroute
- Prijzen / budget tracking
- Barcode scanner
- Delen naar specifieke personen (share-link is voor iedereen met URL)
- Dark mode toggle
- Meerdere talen (alleen Nederlands)
- Push notificaties
- Export naar PDF/print
- Foto's uploaden (alleen URL's)
- Recipe search/filter op ingrediënten
- Nutritional info
- Shopping history/analytics

---

## Implementatie Tips

### Structuur
- Modulaire opbouw met IIFE's (Immediately Invoked Function Expressions)
- Modules: Sync, Store, State, CategoryMap, Recipe, Parser, UI, Modal, Toast, Palette, Suggestions
- Alle modules in één HTML-bestand (single-file app)
- CSS bovenaan in `<style>` tag
- JavaScript onderaan in `<script>` tag

### State management
- State is centraal, immutable via `State.update(fn)`
- UI rendered op basis van state, nooit andersom
- Geen direct DOM-mutations buiten render-functies

### Event handling
- Event delegation waar mogelijk (bijv. item-clicks op mainContent)
- Avoid dubbele event listeners (innerHTML wipes previous listeners)
- Click handlers in render-functies attached na innerHTML

### Firebase
- Error handling op alle Firebase calls
- Fallback naar localStorage bij Firebase failures
- Array/object normalisatie in mergeRemote
- Defensive checks overal (null, undefined, type checks)

### Parser
- Regex patterns voor quantity + unit extractie
- Parentheses voor notes: `(roma)` → note
- Multipliers: `x2`, `2x`
- Spaties tussen numbers en units: `500 g`, `500g` beide OK
