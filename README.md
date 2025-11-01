# Jira Mall  
[![Made with HTML](https://img.shields.io/badge/Made%20with-HTML-orange.svg)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![No dependencies](https://img.shields.io/badge/Dependencies-None-brightgreen.svg)](#)
[![Runs locally](https://img.shields.io/badge/Runs-Locally-blue.svg)](#)

---

##  Bakgrund
Jag såg av en slump att det gick att skapa ifyllda fält i Jira genom att lägga till parametrar direkt i en **URL**.  
När jag klickade på en sådan länk öppnades ett nytt ärende – redan delvis ifyllt.  
Det visade sig inte vara något officiellt API, utan snarare en intern Jira-funktion som Jira själv använder bakom kulisserna.  

Och jag tänkte: *tänk om man kunde ha en enkel webbsida som fyller i en felrapport åt en – direkt i Jira?*  
Det här är resultatet.

---

## Redigera mallar

Alla mallar och inställningar finns **överst i samma HTML-fil**, i YAML-blocket:  

```yaml
<script type="text/yaml" id="templatesYaml">
templates:
  "Felrapport CCX-3":
    project: "CCX-3"
    issuetype: "Bug"
    summary: "Bug: <text>"
    description: |
      *Felbeskrivning*
      <text>

      *Felupprepning*
      <text>

defaults:
  baseUrl: "https://mydomain.com"
  endpoint: "init"
  pid:
    "CCX-3": 10302
  order: ["issuetype","summary"]
  issuetypes:
    Bug: 1
    Epic: 2
  dynamics:
    environment:
      lab:   ["LabA","LabB","LabC"]
      node:  ["Node1","Node2","Node3"]
      build: ["Build-Alpha","Build-Beta","Build-Gamma"]
</script>
```

💡 Du redigerar YAML-mallen **direkt i koden**.  
Skälet: en SPA (Single Page Application) får normalt inte läsa in externa filer lokalt p.g.a. webbläsarens säkerhetsregler (CORS).  
Därför ligger YAML:en inline – allt är självbärande och fungerar var som helst utan server eller nätverksåtkomst.

---

## Fält

- **templates** – varje mall visas i dropdown *Mall*.  
- **defaults** – systemvärden (t.ex. baseUrl, endpoint, pid-mappning, ordning på fält och dropdown-listor).  
- **dynamics** – definierar fält som byggs upp av flera dropdowns (ex. `environment` → `lab`, `node`, `build`).  
  De valda alternativen skrivs ihop till ett textfält som skickas till Jira.

Alla fältnamn i GUI:t matchar YAML-nycklarna exakt (`project`, `description`, `environment` osv).

---

## Textformat
Endast **fetstil** (`*text*`) stöds.  
Radbrytningar (`\n`) bevaras som i YAML:en.

---

## Användning
1. Spara filen som `jira-template.html`.  
2. Kör lokalt:  
   ```bash
   python3 -m http.server 8080
   # öppna http://localhost:8080/jira-template.html
   ```
3. Välj mall, fyll i, och klicka **Skapa** → Jira öppnas med förifyllda fält.  
   Eller klicka **Kopiera URL** för att dela länken.

---

## Säkerhet
- Ingen extern JavaScript eller nätverkstrafik – allt körs lokalt.  
- `window.open(..., 'noopener,noreferrer')` hindrar referer-läckor.  
- YAML-parsern stöder endast säkra strukturer (inga skript eller anrop).  
- Varning visas om URL överstiger 1800 tecken.

---

## Exempel på dynamiskt fält

```yaml
dynamics:
  environment:
    lab:   ["LabA","LabB","LabC"]
    node:  ["Node1","Node2","Node3"]
    build: ["Build-Alpha","Build-Beta","Build-Gamma"]
```

Detta skapar tre dropdowns – **Lab**, **Node** och **Build** – som tillsammans bygger upp fältet `environment` i Jira.  
Om du skriver i fältet manuellt, ersätts dropdown-värdet vid nästa uppdatering.

---

## Repo
Projektet körs helt lokalt och kan läggas var som helst:

```bash
git clone https://github.com/stromg/jira-template.git
cd jira-template
python3 -m http.server 8080
```

---

Vibe-coded with ❤️  by **Göran Ström**

