# Site Auto-M · Garage Carrosserie Multimarques, Fribourg

Site vitrine statique (HTML, CSS, JavaScript), sans framework ni dépendance, prévu pour **GitHub Pages**.
Le formulaire de devis est envoyé par le service **Formspree** (aucun serveur à gérer).

## Structure

```
index.html                  Page principale (accueil, prestations, garage, devis, horaires)
informations-legales.html   Mentions légales + politique de confidentialité (modèle)
css/style.css               Styles (mobile-first)
js/main.js                  Menu mobile, statut ouvert/fermé, formulaire
robots.txt, sitemap.xml     Référencement (adresse du site à compléter)
favicon.svg                 Icône provisoire
assets/fonts/               Polices WOFF2 + licences OFL
assets/img/provisoire/      Logo et photos PROVISOIRES (droits à obtenir)
```

Une version PHP (hébergement suisse avec Apache) est archivée à côté de ce dossier, dans
`../Archive - version PHP (hebergement suisse)/`, si vous changez d'hébergeur plus tard.

## Aperçu en local

```
python3 -m http.server 8000
```

Puis ouvrir http://localhost:8000. Tant que Formspree n'est pas configuré, le formulaire s'affiche désactivé
avec le message « appelez-nous » : c'est le comportement attendu.

## Publier sur GitHub Pages (glisser-déposer, sans ligne de commande)

1. Sur github.com : **New repository**, nom par exemple `auto-m`, visibilité **Public**
   (obligatoire pour GitHub Pages avec un compte gratuit), puis **Create repository**.
2. Sur la page du dépôt vide, cliquer sur le lien **uploading an existing file**.
3. Ouvrir ce dossier dans le Finder, tout sélectionner (**Cmd + A**) et glisser la sélection dans la page GitHub.
   Glisser **le contenu** du dossier, pas le dossier lui-même : `index.html` doit se retrouver à la racine du dépôt.
4. Attendre que les 18 fichiers soient listés, puis cliquer sur **Commit changes**.
5. Dans le dépôt : **Settings → Pages → Build and deployment → Source : Deploy from a branch**,
   branche `main`, dossier `/ (root)`, puis **Save**.
6. Après une à deux minutes, le site est en ligne à `https://NOM-UTILISATEUR.github.io/auto-m/`
   (l'adresse exacte s'affiche en haut de la page **Settings → Pages**). Cocher **Enforce HTTPS**.

Pour modifier le site plus tard : ouvrir le fichier sur GitHub et cliquer sur le crayon, ou refaire
**Add file → Upload files** avec le fichier modifié (même nom, même dossier : il est remplacé).

Tous les chemins du site sont relatifs : il fonctionne aussi bien à l'adresse `…github.io/auto-m/`
qu'avec un domaine personnalisé.

### Domaine personnalisé (facultatif, ex. `auto-m-fribourg.ch`)

1. Acheter le domaine chez un registraire (Infomaniak, Hostpoint…).
2. Dans **Settings → Pages → Custom domain**, saisir le domaine (GitHub crée un fichier `CNAME` dans le dépôt).
3. Chez le registraire, créer les enregistrements DNS indiqués dans la documentation GitHub Pages
   (« Managing a custom domain for your GitHub Pages site »).
4. Une fois le domaine actif, cocher **Enforce HTTPS**.

### Après la mise en ligne

Remplacer `DOMAINE-A-DEFINIR.ch` dans `robots.txt` et `sitemap.xml` par l'adresse réelle du site,
et ajouter dans `<head>` de `index.html` : `<link rel="canonical" href="ADRESSE/">` et une balise `og:image` absolue.

## Activer le formulaire (Formspree)

1. Créer un compte sur https://formspree.io avec **l'adresse e-mail du garage** et la confirmer.
2. Créer un formulaire (« New form »). Formspree affiche une adresse du type `https://formspree.io/f/abcdwxyz`.
3. Dans `index.html`, remplacer `VOTRE_ID_FORMSPREE` dans l'attribut `action` du formulaire par l'identifiant
   (la partie après `/f/`).
4. Recommandé : dans les réglages du projet Formspree, activer **Restrict to Domain** avec l'adresse du site
   (`NOM-UTILISATEUR.github.io` ou votre domaine).
5. Publier, puis envoyer une demande de test depuis le site en ligne.

Bon à savoir :

- Offre gratuite : 50 demandes par mois, historique conservé 30 jours dans le compte Formspree
  (au-delà de 50, les demandes sont mises de côté sans e-mail). Vérifier l'offre en vigueur sur formspree.io/plans.
- Le champ `email` du formulaire sert d'adresse de réponse : « Répondre » dans l'e-mail reçu écrit directement au client.
- Anti-spam : champ piège `_gotcha` (ignoré par Formspree s'il est rempli) et délai minimal de 3 secondes avant envoi.
- Les données passent par les serveurs de Formspree aux États-Unis : c'est indiqué dans la politique de confidentialité.

## Sécurité

GitHub Pages ne permet pas d'ajouter des en-têtes HTTP. La politique de sécurité (CSP) est donc déclarée dans une
balise `<meta http-equiv="Content-Security-Policy">` en haut de chaque page. Elle n'autorise que les fichiers du site
et l'envoi vers `formspree.io`.

Le petit script inline du `<head>` de `index.html` est autorisé par son empreinte (hash sha256) dans cette CSP.
Si vous modifiez ce script, recalculez l'empreinte, sinon le navigateur le bloquera :

```
printf '%s' "document.documentElement.classList.add('js');" | openssl dgst -sha256 -binary | base64
```

## Éléments provisoires

- Le mode révision est actif : attribut `data-review` sur `<html>` dans les deux pages HTML.
  Il affiche les étiquettes jaunes « À confirmer » et les blocs `[À COMPLÉTER]`.
  **Le retirer avant la mise en ligne**, une fois les contenus validés.
- Pour tout retrouver : `grep -rn "PROVISOIRE\|À COMPLÉTER\|À AJOUTER\|DOMAINE-A-DEFINIR\|VOTRE_ID\|review-tag" .`

## Remplacer une image

Déposer le nouveau fichier (idéalement en WebP, environ 1600 px de large pour l'accueil, 1000 px pour les autres),
mettre à jour `src`, `width`, `height` et `alt` dans `index.html`. Ensuite, supprimer l'étiquette
`review-tag--media` et déplacer le fichier hors du dossier `provisoire/`.

## Horaires

Les horaires se modifient à un seul endroit pour l'affichage : les attributs `data-hours` du tableau dans
`index.html`. Le statut « Ouvert / Fermé » en est calculé. Mettre aussi à jour le texte visible du tableau et le
bloc JSON-LD (`openingHoursSpecification`) dans `<head>`.
