# Mise en ligne de crakit-action (geste de Louann, une fois)

Le README de crakit-cli promet déjà `ldc2956/crakit-action@v1`. Ce dossier tient la promesse.
Une Action GitHub doit être dans un dépôt PUBLIC pour être utilisable par d'autres : c'est l'exception à la règle « dépôts privés », voulue, limitée à ce dossier (aucun secret dedans, MIT).

1. Déplacer `self-test.yml.a-deplacer` vers `.github/workflows/self-test.yml` (le pont ne peut pas écrire dans `.github`).
2. Dans ce dossier :
   ```
   git init
   git add -A
   git commit -m "crakit-action 1.0.0"
   gh repo create ldc2956/crakit-action --public --source=. --push
   git tag v1.0.0 && git tag v1 && git push --tags
   ```
3. Vérifier que le workflow `self-test` passe (onglet Actions). Il fabrique un mini projet avec lodash 4.17.20 et lance l'action dessus.
4. Facultatif : Marketplace → « Publish this Action » depuis la page du dépôt.

Quand la version 1.1 sortira : `git tag -f v1 && git push -f --tags`, c'est ce qui fait avancer `@v1`.
