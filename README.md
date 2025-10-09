# Analyse Docusaurus implementatie voor ZGW en uitrol naar andere repositories

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

3. De structuur van de Docusaurus site moet voldoen aan de in '[Uniformering GitHub Pages](https://github.com/VNG-Realisatie/API-Kennisbank/blob/master/Uniformering/Index.md)' beschreven structuur.
   Zo nodig kan natuurlijk besloten worden die structuur aan te passen aan de nieuwe fuhctionaliteit.

4. De top navigatie moet centraal geconfigureerd en beheerd kunnen worden.
   De ZGW implementeert nu een top navigatie die alleen betrekking heeft op de ZGW API standaarden. De ZGW API standaarden maken echter deel uit van een familie van VNG Realisatie standaarden en de topnavigatie vormt een portaal van waaruit je de GitHub Pages sites van al die standaarden kunt bereiken.

Op dit moment wordt n.m.m. nog niet aan voorwaarde 2, 3 en 4 voldaan. 

2. De werking van Docusaurus is voor zover ik nu kan beoordelen niet echt intuïtief, complex en zelfs enigzins ondoorgrondelijk. Ik kan het in ieder geval nog niet goed en eenvoudig uitleggen;
3. De structuur zoals nu opgezet voor ZGW voldoet niet aan wat is vastgelegd in '[Uniformering GitHub Pages](https://github.com/VNG-Realisatie/API-Kennisbank/blob/master/Uniformering/Index.md)'.
   Voor de ZGW site is nu gekozen voor een top navigatie menu die alleen betrekking heeft op de ZGW API's. In de Jekyll versie maakt de ZGW site echter onderdeel uit van het grotere geheel van de VNG Realisatie Standaarden portaal. Natuurlijk kunnen we dat in Docusaurus ook realiseren maar dat heeft 3 nadelen:
4. De configuratie van het top navigatie menu gebeurd niet centraal, tenminste voor zover ik nu kan overzien.
   Bij Jekyll is dat wel het geval. Daar wordt door de GitHub repositories die Jekyll implementeren gebruik gemaakt van Jekyll themes waarin de top navigatie is geconfigureerd.
   Dat de top navigatie niet centraal geconfigureerd kan worden heeft de volgende nadelen:
   -  Het topnavigatiemenu moet in elke GitHub repo die Docusaurus implementeert apart geconfigureerd worden wat foutgevoelig is. Bij die configuratie kunnen fouten gemaakt worden waardoor de topnavigatie bij de ene Docusaurus site afwijkt van die van de andere.
   -  Als er wijzigingen in de topnavigatie moeten worden aangebracht moeten deze worden doorgevoerd in alle GitHub repositories waarmee Docusaurus wordt geïmplementeerd.
  

## Verkenningen van Docusaurus

### Experimenten vanaf scratch

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

### Experimenteren met de ZGW-AP's Docusaurus site
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
├── api-specs
├── api-specificatie
├── docs
    ├── unversioned
        ├── adrs
        ├── community
        ├── gids
    ├── v1
        ├── autorisaties
├── sidebars
├── src
    ├── components
        ├── HomepageFeature
    ├── css
    ├── pages
├── static
    ├── img
├── v1_versioned_docs
    ├── version-1.5.1
        ├── autorisaties
├── v1_versioned_sidebars
```

Laten we de folders even een voor 1 aflopen.

**api-specs**<br/>
Deze folder en diens subfolders (hier niet weergegeven) lijken geen rol te spelen bij het genereren van de site. Wijzigingen aangebracht in de OAS specificatie `api-specs/v1/autorisaties/openapi.yaml` leiden nl. niet tot wijzigingen in de site. Dat is ook niet het geval na een handmatige rebuild van de site.

In het Docusaurus configuratiebestand `docusaurus.config.ts` staat echter:

```yaml
1.     [
2.       "docusaurus-plugin-openapi-docs",
3.       {
4.         id: "openapi",
5.         docsPluginId: "v1",
6.         config: {
7.           autorisaties: {
8.             specPath: "api_specs/v1/autorisaties/openapi.yaml",
9.             outputDir: "docs/v1/autorisaties",
10.            sidebarOptions: {
11.              groupPathsBy: "tag",
12.              categoryLinkSource: "tag",
13.            },
14.          } satisfies OpenApiPlugin.Options,
15.        },
16.      } satisfies Plugin.PluginOptions,
17.    ],
18.  ],
```
Ik vermoed dat deze code iets te maken heeft met het vertalen van de `openapi.yaml' naar de MDX bestanden in de `docs/v1/autorisatie` folder. Regel 8 verwijst dan naar
Toch worden de MDX bestanden niet geüpdate als ik een nieuwe build maak.

**api-specificatie**<br/>
Deze folder en diens subfolders (hier niet weergegeven) lijken geen rol te spelen bij het genereren van de site. Wijzigingen aangebracht in de OAS specificatie `api-specificatie/ac/openapi.yaml` leiden nl. niet tot wijzigingen in de site. Dat is ook niet het geval na een handmatige rebuild van de site.

**docs**<br/>
In de onderliggende folders van deze folder vinden we een aantal markdown bestanden maar in de folder `docs/v1/autorisaties` ook een aantal MDX bestanden (een formaat dat Markdown-tekst combineert met JSX-componenten oftewel JavaScript XML componenten wat een syntaxisuitbreiding is voor JavaScript die het mogelijk maakt om HTML-achtige code te schrijven binnen JavaScript-bestanden) alsmede het bestand `sidebar.ts`.

In de subfolder `docs/unversioned` staan bestanden die onafhankelijk zijn van een van de API versies. Om te beginnen 3 markdown bestanden waar in de footer van de site naar verwezen wordt. Voor het markdown bestand in de subfolder `docs/unversioned/adrs` geldt hetzelfde.
Naar de bestanden `index.md` in de subfolders `docs/unversioned/community` en `docs/unversioned/gids` wordt vanuit de top navigatie items 'Gids' en 'Community' gelinkt. De andere bestanden in deze folders kunnen daarna benadert worden via de side navigatie.

De bestanden in de folder de folder `docs/v1/autorisaties` zijn gerelateerd aan API versie 1.6.0. 
Hoe de MDX bestanden vervaardigd zijn is nog de vraag. Vermoedelijk en ook hopelijk zijn deze gegenereerd. Hoe moet nog uitgezocht worden.
Op het eveneens in deze folder aanwezige bestand `sidebar.ts` kom ik hieronder terug.

**sidebars**<br/>
Het bestand `v1.ts` configureert de sidebar die verschijnt als wordt gekozen voor versie 1.6.0 van de API specificaties. Overigens wordt in dit bestand niet alleen de side bar geconfigureert maar ook de body van de pagina voor de categorie niveau's van de side bar. Verder wordt in dit betand het bestand `docs/v1/autorisaties/sidebar.ts` geïmporteerd.

Ik heb getest of ik die import kon uitschakelen maar dat is om de e.o.a. reden nog niet gelukt. Waarom niet is me niet duidelijk. Het is i.i.g. niet eenvoudig het vervangen van

``` yaml
          type: "category",
          label: "Autorisaties API",
          link: {
            type: "generated-index",
            title: "Autorisaties API",
            description:
              "This is a sample server Petstore server. You can find out more about Swagger at http://swagger.io or on irc.freenode.net, #swagger. For this sample, you can use the api key special-key to test the authorization filters.",
            slug: "/category/autorisaties-api",
          },
          items: require("../docs/v1/autorisaties/sidebar.ts"),
```
door
``` yaml
          type: "doc",
          id: "index",
```

Ook het bestand `unversioned.ts' staat in deze folder. Het configureert de sidebar voor de pagina's die in de folder `docs/unversioned' staan. Dus voor de situaties als in de top navigatie gekozen is voor 'Gids' en 'Community' of in de footer voor 'Over de ZGW API standaard', 'Besluitenlogboek', 'Doe mee', 'Contact' en 'Praktijkvoorbeelden'. Het koppelen van dit sidebar bestand aan alles in die folder gebeurd in `docusaurus.config.ts`. Daar staat nl.

```yaml
1.    presets: [
2.      [
3.        "@docusaurus/preset-classic",
4.        {
5.          docs: {
6.            // id: "default", // ommitted, because it's the default docs instance
7.            path: "docs/unversioned",
8.            routeBasePath: "/",
9.            sidebarPath: require.resolve("./sidebars/unversioned.ts"),
10.           remarkPlugins: [
11.             ...
12.           ],
13.         },
14.         ...
15.         },
16.       } satisfies Preset.Options,
17.     ],
18.   ],
```
M.n. de regels 7 en 9 zijn hierbij van belang. De functie `require.resolve` in regel 9 doet overigens niets meer dan het absolute path naar `sidebars/unversioned.ts` teruggeven. M.b.t. het bestand `v1.ts` gebeurd eigenlijk hetzelfde in `docusaurus.config.ts`.

```yaml
1.   plugins: [
2.      [
3.        "@docusaurus/plugin-content-docs",
4.        {
5.          id: "v1",
6.          path: "docs/v1",
7.          routeBasePath: "v1/",
8.          sidebarPath: "./sidebars/v1.ts",
9.          ...
10.       } satisfies Plugin.PluginOptions,
11.     ],
```

**src**<br/>
De startpagina van het Classic Docusaurus theme, het enige dat op dit moment beschikbaar is, bestaat uit 3 delen:
* Een headerframe met daarin o.a. de top navigatie en de button om naar de verschillende versies van de ZGW-API specificaties te gaan;
* Een footerframe bestaande uit de brede grijze balk onderaan met daarin de 3 kolommen 'Standaard', 'Project' en 'Context';
* Het tussen deze beide frames gelegen bodyframe met daarin inhoudelijke componenten.

Het headerframe en bodyframe is alleen op deze pagina te zien. Het Footerframe blijft altijd zichtbaar.

De vulling van het bodyframe wordt in de folder 'src' geregeld. 
* Het bestand `src/components/HonepageFeatures/index.js` bevat Javascript code waarmee het Feature deel van het bodyframe wordt gevuld. De teksten, de titels en de bijbehorende images van de features worden hier gedefinieerd;
* Het bestand `src/pages/index.js` combineert het resultaat van het voorgaande bestand met de andere in de bodyframe voorkomende componenten. Daarbij wordt o.a gebruik gemaakt van enkele in het bestand `docusaurus.config.ts` geconfigureerde properties.

Door aan het laatste bestand de functie `ZGWtopnavigatie'

```javascript
function ZGWtopnavigatie() {
  const { siteConfig } = useDocusaurusContext();
  return (
      <div>
			<div>
			  <Link className="button button--secondary" to="/">
				ZGW API's
			  </Link>&nbsp;&nbsp;&nbsp;
			  <Link className="button button--secondary" to="/gids/tools">
				Gids
			  </Link>&nbsp;&nbsp;&nbsp;
			  <Link className="button button--secondary" to="/v1/next">
				API Suite
			  </Link>&nbsp;&nbsp;&nbsp;
			  <Link className="button button--secondary" to="/community">
				Community
			  </Link>
			</div>
      </div>
  );
}
```

toe te voegen en de functie Home() uit te breiden met het element 'ZGWtopnavigatie'

```javascript
export default function Home() {
  const { siteConfig } = useDocusaurusContext();
  return (
    <Layout
      title={`Home | ${siteConfig.title}`}
      description="Description will go into a meta tag in <head />"
    >
      <ZGWtopnavigatie />
      <HomepageHeader />
      <main>
        <HomepageFeatures />
      </main>
    </Layout>
  );
}
```

Lukt het om een extra topnavigatie niveau toe te voegen waarmee de standaard topnavigatie gebruikt zou kunnen worden voor de VNG-Realisatie Standaarden portaal functie. Helaas is deze alleen op deze pagina beschikbaar.

**static**<br/>

**v1_versioned_docs**<br/>

**v1_versioned_sidebars**<br/>

