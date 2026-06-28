# Journal design — dev-dashboard

Launchpad de favoris perso (GitHub Pages). Direction : **cyberpunk, bleu électrique, punchy mais lisible**.

## Direction retenue
- **Concept** : console / HUD de pilotage — « SYSTEM ONLINE », on clique un outil pour l'ouvrir.
- **Palette** : `#05070e` (fond quasi-noir) · `#29d3ff` (cyan signal) · `#ff2e97` (magenta) · `#3b6cff` (bleu) · `#dce8ff` (encre) · `#6e7e9e` (texte atténué).
- **Typo** : display = **Chakra Petch** / body = système / mono = **JetBrains Mono** — via Google Fonts (`display=swap`) **avec fallback système** dans la stack (rapide + caractère, FOUT maîtrisé).
- **Signature** : cartes **biseautées** (`clip-path`) + **liseré néon par catégorie** (inset gauche) + eyebrows `// CATÉGORIE` + crochets de visée + glow au survol ; barre néon cyan→magenta en tête, fond grille + halos radiaux.
- **Layout** : catégories empilées, chacune en **grille de cartes** (`minmax(238px,1fr)`), 1 colonne sous 560px.

## Pistes écartées (ne pas y revenir)
- **Vert acidulé sur fond noir** — cliché IA n°2, écarté pour le bleu électrique cyan/magenta.

## Captures clés
- En ligne (source de re-capture) : https://adspiteira.github.io/dev-dashboard/
- Captures de session (éphémères) : desktop + mobile, avant/après le correctif cartes du 28/06.

## Plancher qualité — état vérifié (28 juin 2026)
- **responsive mobile** : VU — corrigé le 28/06 (cartes empilées, plus de débordement).
- **focus clavier** : VU dans le code — piège `clip-path` **traité** (cartes en `box-shadow` inset ; boutons non biseautés en `outline`).
- **reduced-motion** : OK — aucune animation CSS, transitions coupées sous `prefers-reduced-motion`.
- **contraste** : mesuré — texte atténué `#6e7e9e` sur carte ≈ **4,7:1** (passe 4,5, mais juste).

## À refaire / dette design
- **P2 — titre des cartes non plafonné** : `.name` retourne à la ligne librement → un nom long (ex. « awwwards inspiration design ») grandit la carte et **désaligne les hauteurs** d'une rangée. Fix : `-webkit-line-clamp:2` (ou nowrap+ellipsis) sur `.name`.
- **P3** : si le nom passe sur 2 lignes, `.card` en `align-items:center` décale l'icône → envisager `align-items:flex-start` une fois le titre plafonné.
- **P3** : `margin-top:3px` entre nom et légende un peu serré (4–5px respirerait mieux).
- **P3 (pré-existant)** : sous-titre « SYSTEM ONLINE… » un peu serré au bord sur mobile.
