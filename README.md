# Docusaurus-test

## Docusaurus basics
De door mij doorlopen stappen om docusaurus aan de praat te krijgen:

1. Ik ben gestart bij een lege repository;
2. Die heb ik m.b.v. GitHub desktop lokaal gehaald;
3. Op die lokale repo heb ik in een Cmd Windows het commando: 'npx create-docusaurus@latest my-website classic' gedraaid;
4. Vervolgens heb ik daar ook 'npm run build' gedraaid wat resulteerde in een 'build' folder in de 'my-website' folder;
5. Deze folder heb ik verplaatst naar de root van de repo en daar hernoemd naar 'docs';
6. In het bestand 'my-website/docusaurus.config.js' heb ik de property 'baseUrl' gewijzigd van '/' in '/Docusaurus-test/';
7. In het 'my-website/.gitignore' bestand heb ik de ignore op de '/build' folder uitgeschakeld;
8. Daarna alles naar de remote repo geschreven;
9. Daar heb ik tenslotte GitHub Pages geconfigureerd zodat deze build vanaf de 'main' branch en de 'docs' folder;
10. Na builden kan de docusuarus site bekeken worden op de url die door GitHub Pages wordt aangegeven;

## Docusaurus builden en deployen met een Action script
De Docusaurus site voor GEMMA Zaken wordt gebuild en gedeployed m.b.v. een Action script. Om er voor te zorgen dat er een Action script gebruikt kan worden voor het deployen van de GitHub Pages site moet je in _'Settings/Pages'_ bij _'Build and deployment - Source'_ kiezen voor _'**GitHub Actions**'_.
Plaats daarna de file `deploy.yml` in de folder `\.github\workflows` met de volgende inhoud:
```
1. on:
2.   push:
3.     branches:
4.       - main
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

* Het toevoegen van de branchnamen in de regels 4 en 5 zorgt er voor dat de workflow opgestart kan worden voor zowel de master als de documentatie-update branch. Daarvoor moet dit bestand wel in beide branches in `\.github\workflows` worden geplaatst. Hebben je branches andere namen gebruik die dan en natuurlijk mag er ook alleen de 'main' branch staan. Het lijkt er trouwens op dat die branch er minimaal moet staan.
* De string  `workflow_dispatch:` in regel 6 zorgt er voor dat de workflow handmatig opgestart kan worden.


