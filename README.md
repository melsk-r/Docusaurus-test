# Docusaurus-test

De door mij doorlopen stappen om docusaurus aan de praat te krijgen:

1. Ik ben gestart bij een lege repository;
2. Die heb ik m.b.v. GitHub desktop lokaal gehaald;
3. Op die lokale repo heb ik in een Cmd Windows het commando: 'npx create-docusaurus@latest my-website classic' gedraaid;
4. Vervolgens heb ik daar ook 'npm run build' gedraaid wat resulteerde in een 'build' folder in de 'my-website' folder;
5. Deze folder heb ik verplaatst naar de root van de repo en daar hernoemd naar 'docs';
6. In het bestand 'my-website/docusaurus.config.js' heb ik de property 'baseUrl' gewijzigd van '/' in '/Docusaurus-test/';
7. Daarna alles naar de remote repo geschreven;
8. Daar heb ik tenslotte GitHub Pages geconfigureerd zodat deze build vanaf de 'main' brannch en de 'docs' folder;
9. Na builden kan de docusuarus site bekeken worden op de url die door GitHub Pages wordt aangegeven;
