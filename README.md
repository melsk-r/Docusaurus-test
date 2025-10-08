# Docusaurus-test

## Voorwaarden voor een evt. invoering van Docusaurus

1. Alle GitHub Pages sites moeten dezelfde vormgeving hebben.
   a. Stel we besluiten om zowel het gebruik van Jekyll als Docusaurus toe te staan dan moet een bezoeker van de GitHub Pages sites qua vormgeving niet zien met welke static site generator de site gegenereerd is.
      De vormgeving van de Docusaurus site moet dan aangepast worden aan de vormgeving van de Jekyll sites of andersom. Wel mag de functionalteit natuurlijk afwijken.
   b. Stel we besluiten dat alle sites omgebouwd moeten worden naar Docusaurus dan mag een gehele nieuwe vormgeving geïntroduceerd worden maar moeten alle sites ook in een betrekkelijk korte periode over op Docusaurus.

   Optie 2 heeft de voorkeur aangezien die ons niet verplicht om de CSS voor Jekyll dan wel voor Docusaurus aan te passen en ook omdat ik verwacht dat het erg lastig wordt bepaalde vormgevingen hetzelfde te krijgen.
   Om een voorbeeld te noemen, kijk naar hoe Swagger en Redocly zijn geïntegreerd in Jekyll en kijk hoe dat bij Docusaurus het geval is. Bij Docusaurus ontbreekt overigens (op dit moment) alle integratie van Redocly.

2. Het gebruik van Docusaurus moet redelijk eenvoudig uit te leggen zijn en het gebruik moet zo makkelijk mogelijk gemaakt kunnen worden.
   Het gebruik van Jekyll is indertijd met een presentatie uitgelegd op basis waarvan het diverse personen inderdaad gelukt is een site in elkaar te zetten. Zoiets moet, als we Docusaurus gaan gebruiken, ook nu weer het geval zijn.
   We moeten niet willen dat collega's zich eerst diep in Docusaurus moeten verdiepen voordat ze er een site mee kunnen genereren. Liefst creëren we een template op basis waarvan redelijk eenvoudig een site in elkaar te zetten is.
   Daarbij moet bepaalde functionaliteit eenvoudig aan of uitgezet kunnen worden.

Op dit moment heb ik nog niet het gevoel dat aan voorwaarde 2 kan worden voldaan. De werking van Docusaurus is nog niet echt intuïtief en ondoorgrondelijk. Ik kan het in ieder geval nog niemand uitleggen.

## Spelen met Docusaurus

### Eigen probeersels

#### Docusaurus basics
De door mij doorlopen stappen om docusaurus aan de praat te krijgen:

1. Ik ben gestart bij een lege repository;
2. Die heb ik m.b.v. GitHub desktop lokaal gehaald;
3. Op die lokale repo heb ik in een Cmd Windows het commando: 'npx create-docusaurus@latest my-website classic' gedraaid;
4. Vervolgens heb ik daar ook 'npm run build' gedraaid wat resulteerde in een 'build' folder in de 'my-website' folder;
5. Deze folder heb ik verplaatst naar de root van de repo en daar hernoemd naar 'docs';
6. In het bestand 'my-website/docusaurus.config.js' heb ik de property 'baseUrl' gewijzigd van '/' in '/Docusaurus-test/';
7. Daarna alles naar de remote repo geschreven;
8. Daar heb ik tenslotte GitHub Pages geconfigureerd zodat deze build vanaf de 'main' branch en de 'docs' folder;
9. Na builden kan de docusaurus site bekeken worden op de url die door GitHub Pages wordt aangegeven.

#### Docusaurus builden en deployen met een Action script
**LET OP!**: _In deze paragraaf wil ik beschrijven hoe je het builden en deployen op de remote repository kunt laten uitvoeren m.b.v. een Actionscript. Onderstaand verhaal werkt echter alleen nog maar voor de gemma-zaken repository. Er moet dus nog uitgezocht worden hoe het ook op andere repositories geïmplementeerd moet worden._

De Docusaurus site voor GEMMA Zaken wordt gebuild en gedeployed m.b.v. een Action script. Om er voor te zorgen dat er een Action script gebruikt kan worden voor het deployen van de GitHub Pages site moet je in _'Settings/Pages'_ bij _'Build and deployment - Source'_ kiezen voor _'**GitHub Actions**'_.
Plaats daarna de file `deploy.yml` in de folder `\.github\workflows` met de volgende inhoud:
```
1. on:
2.   push:
3.     branches:
4.       - master
5.       - documentatie-update
6.   workflow_dispatch:
7.
8. jobs:
9.   deploy:
10.    permissions:
11.      pages: write # to deploy to Pages
12.      id-token: write # to verify the deployment originates from an appropriate source
13.    environment:
14.      name: github-pages
15.      url: ${{ steps.deployment.outputs.page_url }}
16.    runs-on: ubuntu-latest
17.    steps:
18.      - name: Checkout
19.        uses: actions/checkout@v4
20.      - name: Set up Node.js
21.        uses: actions/setup-node@v4
22.        with:
23.          node-version: 22.x
24.          cache: npm
25.      - name: Install dependencies
26.        run: npm install --frozen-lockfile --non-interactive
27.      - name: Spectral YAML lint
28.        run: npm run spectral
29.      - name: Build
30.        run: npm run build
31.      - name: Setup Pages
32.        uses: actions/configure-pages@v4
33.      - name: Upload artifact
34.        uses: actions/upload-pages-artifact@v3
35.        with:
36.          path: build
37.      - name: Deploy to GitHub Pages
38.        id: deployment
39.        uses: actions/deploy-pages@v4
```
Regelnummers horen natuurlijk niet in het bestand thuis.

* Het toevoegen van de branchnamen in de regels 4 en 5 zorgt er voor dat de workflow opgestart kan worden voor zowel de master als de documentatie-update branch. Daarvoor moet dit bestand wel in beide branches in `\.github\workflows` worden geplaatst. Hebben je branches andere namen gebruik die dan en natuurlijk mag er ook alleen de 'master' (of in andere repo's de 'main') branch staan. Het lijkt er trouwens op dat die branch er minimaal moet staan.
* De string  `workflow_dispatch:` in regel 6 zorgt er voor dat de workflow handmatig opgestart kan worden.

#### Versions

Indien m.b.v. `npm run docusaurus docs:version [versienummer]` een versie van de website is gemaakt en de versie blijkt achteraf niet correct te zijn dan kun je natuurlijk altijd opnieuw dat commando uitvoeren met het juiste versienummer. Mocht dat lastig zijn kun je echter het versienummer ook wijzigen door de volgende aanpassingen aan te brengen:
* Wijzig het versienummer in het bestand ´[Repository-naam]\my-website\versions.json´;
* Wijzig het versienummer in de foldernaam ´[Repository-naam]\my-website\versioned_docs\version-[versienummer]´;
* Wijzig het versienummer in de bestandsnaam ´[Repository-naam]\my-website\versioned_sidebars\version-[versienummer]-sidebars.json´.

### Experimenteren met de ZGW Docusaurus site
De folderstructuur van de ZGW Docusaurus GitHub repo ziet er, als we de folders 
* .devcontainer
* .docusaurus
* .git
* .github
* .vscode
* archive
* build
* node_modules
* tools

buiten beschouwing laten, als volgt uit:
```
GEMMA-Zaken
1.  ├── api-specs
2.      ├── v1
3.          ├── autorisaties
4.  ├── api-specificatie
5.      ├── _deprecated
6.      ├── ac
7.      ├── brc
8.      ├── contactmomenten-api
9.      ├── DESIGN
10.     ├── drc
11.     ├── klanten-api
12.     ├── nrc
13.     ├── verzoeken-api
14.     ├── zrc
15.     ├── ztc
16. ├── docs
17.     ├── unversioned
18.         ├── adrs
19.         ├── community
20.         ├── gids
21.     ├── v1
22.         ├── autorisaties
23. ├── sidebars
24. ├── src
25.     ├── components
26.         ├── HomepageFeature
27.     ├── css
28.     ├── pages
29. ├── static
30.     ├── img
31. ├── v1_versioned_docs
32.     ├── version-1.5.1
33.         ├── autorisaties
34. ├── v1_versioned_sidebars
```

Laten we de folders even een voor 1 aflopen.

**api-specs**<br/>
Deze folder lijkt geen rol te spelen bij het genereren van de site. Wijzigingen aangebracht in de OAS specificatie `api-specs/v1/autorisaties/openapi.yaml` leiden nl. niet tot wijzigingen in de site. Dat is ook niet het geval na een handmatige rebuild van de site.

**api-specificatie**<br/>
Hetzelfde lijkt te gelden voor deze folder.

**docs**<br/>
In de onderliggende folders van deze folder vinden we een aantal markdown bestanden maar in de folder `docs/v1/autorisaties` ook een aantal MDX bestanden (een formaat dat Markdown-tekst combineert met JSX-componenten oftewel JavaScript XML componenten wat een syntaxisuitbreiding is voor JavaScript die het mogelijk maakt om HTML-achtige code te schrijven binnen JavaScript-bestanden) alsmede het bestand `sidebar.ts`.

In de subfolder `docs/unversioned` staan bestanden die onafhankelijk zijn van een van de API versies. Om te beginnen 3 markdown bestanden waar in de footer van de site naar verwezen wordt. Voor het markdown bestand in de subfolder `docs/unversioned/adrs` geldt hetzelfde.
Naar de bestanden `index.md` in de subfolders `docs/unversioned/community` en `docs/unversioned/gids` wordt vanuit de top navigatie items 'Gids' en 'Community' gelinkt. De andere bestanden in deze folders kunnen daarna benadert worden via de side navigatie.

Hoe de MDX bestanden vervaardigd zijn is nog de vraag. Vermoedelijk en ook hopelijk zijn deze gegenereerd. Hoe moet nog uitgezocht worden.
Het bestand `sidebar.ts` lijkt geen rol te spelen.

**sidebars**<br/>

**src**<br/>

**static**<br/>

**v1_versioned_docs**<br/>

**v1_versioned_sidebars**<br/>

