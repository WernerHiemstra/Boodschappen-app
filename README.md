# Boodschappen

Boodschappenlijsten en recepten in één app. Lijsten, recepten met foto’s, ingrediënten toevoegen aan je lijst.

**Starten:** open `index.html` in je browser (lokaal of na deploy).

## Realtime delen (meerdere gebruikers)

Zonder configuratie slaat de app alleen lokaal op (per browser). Om wijzigingen **realtime** met anderen te delen (iedereen met de link kan meedoen, geen inlog):

1. **Firebase-project aanmaken**
   - Ga naar [Firebase Console](https://console.firebase.google.com/) → Create a project (of kies bestaand).
   - In het project: **Build** → **Realtime Database** → **Create Database** (bijv. Europa, testmodus mag voor ontwikkeling).

2. **Config in de app zetten**
   - In Firebase: Project settings (tandwiel) → **Your apps** → **</>** (Web) → registreer app, kopieer de `firebaseConfig`-object.
   - Open `index.html` en zoek naar `firebaseConfig` (bij de Sync-module). Vervang de placeholder-waarden door jouw config:
     - `apiKey`, `authDomain`, `databaseURL`, `projectId`, `storageBucket`, `appId`.

3. **Database-regels (geen inlog)**
   - Realtime Database → **Rules**. Voor "iedereen met de link mag lezen/schrijven" (geen auth):
   ```json
   {
     "rules": {
       "shares": {
         ".read": true,
         ".write": true
       }
     }
   }
   ```
   - **Publish**. Let op: hiermee kan iedereen die de URL kent data in dit project lezen en schrijven. Geschikt voor huisgenoten of vertrouwde groepen; voor openbaar gebruik later regels aanscherpen.

4. **Delen**
   - Na het openen van de app staat in de URL een deel-link (bijv. `#share=abc123...`). De knop **Deel link** in de header kopieert de volledige URL; stuur die naar anderen. Iedereen met die link ziet dezelfde lijsten en recepten, wijzigingen verschijnen realtime.

Zonder Firebase-config blijft de app gewoon lokaal werken (geen foutmelding).

## Ontwikkelen

- Geen build-stap: gewoon `index.html` bewerken.
- Voor experimenten: maak een branch (`git checkout -b experiment/naam`) en werk daar verder.
