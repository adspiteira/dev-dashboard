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

## Réordonnancement des catégories (28 juin 2026)
- **Besoin** : pouvoir remonter/descendre une catégorie facilement (ex. mettre « Mes roadmaps » en tête).
- **Choix d'interaction** : **flèches ▲▼ sur l'en-tête de catégorie, visibles en mode édition seulement** (comparé en images à : flèches + bouton « ↥ tout en haut », et glisser-déposer). Retenu pour la **robustesse** (zéro dépendance, clavier + tactile, dans le style anguleux) sur une action **rare**. DnD écarté (sur-dimensionné/fragile pour un tri rare) ; le « ↥ » écarté (peu de catégories → 2-3 clics suffisent).
- **Mécanique** : l'ordre des catégories = **ordre d'apparition dans le tableau plat `links`**. `moveSection(sec,dir)` échange deux blocs voisins en **préservant l'ordre interne** des cartes (`cats.flatMap(c => links.filter(...))`). L'ordre est **persisté tel quel** par la synchro existante (`serialize()` garde l'ordre du tableau) — aucun nouveau stockage.
- **Détails soignés** : flèches `.secbtn` biseautées (`clip-path`), discrètes (`--muted`) au repos, accent `--sec` au survol/focus ; **focus clavier en `box-shadow` inset** (pas d'`outline`, sinon coupé par le `clip-path`) ; flèche désactivée (`disabled`, opacity .3) aux bords ; placées après le trait `::after` (flex:1) via `order:1` → alignées à droite ; **refocus de la flèche après re-render** pour enchaîner au clavier.
- **Vérifié sur images** : édition desktop + mobile (flèches au bord droit, bornes désactivées, pas de débordement), état normal (flèches absentes, vue propre) ; logique testée hors-ligne (réordonnancement, ordre interne, bornes no-op, aucune carte perdue).
- ⚪ **Effet de bord assumé** : le liseré de couleur suit la **position** (`PALETTE[ci]`), pas le nom — réordonner **décale les couleurs** (Roadmaps en tête → liseré cyan au lieu de magenta). Cohérent (chaque section reste distincte). Si gênant un jour : dériver `--sec` d'un **hash du nom** de catégorie (mais ça change les couleurs actuelles).
- ✅ **Cibles tactiles** : flèches **44 px sur mobile** (28 px desktop), gap élargi à 10 px pour éviter les fausses touches — le 28 px a été **jugé trop petit au doigt sur vrai iPhone**, corrigé dans le média `max-width:560px`. *Émulation Playwright iPhone confirmée **fidèle au vrai écran** par l'utilisateur → proxy de test fiable avant déploiement.*

## Plancher qualité — état vérifié (28 juin 2026)
- **responsive mobile** : VU — corrigé le 28/06 (cartes empilées, plus de débordement).
- **focus clavier** : VU dans le code — piège `clip-path` **traité** (cartes en `box-shadow` inset ; boutons non biseautés en `outline`).
- **reduced-motion** : OK — aucune animation CSS, transitions coupées sous `prefers-reduced-motion`.
- **contraste** : mesuré — texte atténué `#6e7e9e` sur carte ≈ **4,7:1** (passe 4,5, mais juste).

## Dette design — SOLDÉE (28 juin 2026)
- ✅ **P2 titre plafonné** : `-webkit-line-clamp:2` sur `.name` (grille à hauteurs régulières).
- ✅ **P3 calage icône** : `.card` en `align-items:flex-start` (icône alignée au titre).
- ✅ **P3 respiration** : `.desc` `margin-top` 3 → 5 px.
- ✅ **P3 sous-titre mobile** : header en `display:block` sous 560 px → titre/sous-titre/toolbar s'empilent et s'enroulent proprement.
- ✅ **P1 badge suppression « coupé »** (mode édition) : `.del` était en `top/right:-7px` (hors carte) → le `clip-path` de `.card` **rognait** la partie qui dépasse. Refait en **rond rouge** (`border-radius:50%`) rentré à `top:7px/right:8px`, dans la zone non rognée + halo de focus clavier. Bonus : colle enfin au texte d'aide « le **rond rouge** pour supprimer ».
- ✅ **P2 badge ↔ titre long** (régression du fix ci-dessus) : le rond rentré tombait sur la ligne du titre → sur un nom long (« Roadmap CongéLink ») il **chevauchait** le texte. Corrigé : `body.editing .meta{padding-right:30px}` réserve la place du badge **en mode édition seulement** → le titre se coupe/espace avant le rond.
- ✅ **Esthétique du bouton de suppression** : le rond rouge plein « faisait tache » (action rare/destructive qui criait en permanence) ET un **rond jurait** avec le système tout anguleux. Refait après comparaison de 3 pistes en images : **au repos un simple `×` gris** (couleur `--muted`, discret), **au survol/focus une petite puce biseautée rouge** (`clip-path` comme les icônes, contour via `box-shadow` inset → propre malgré le clip-path + focus clavier visible). Le rouge n'apparaît qu'au moment d'agir. Piste retenue **B « anguleux »** (× monochrome discret mais trouvable) — A (fantôme) et C (onglet) écartées.

### Passe design-plus (diagnose + fix · 28 juin 2026, capture avant/après)
- ✅ **Placeholder « (duplicated) »** : sous-titre StackBlitz `Vite (duplicated)` (texte de travail laissé) → **`Bac à sable Vite`**. Corrigé dans les **deux sources** : `DEFAULTS` inline (`index.html`) **et** `favorites.json`.
- ✅ **Cohérence texte ↔ UI du bouton supprimer** : l'aide d'édition disait encore « clique … **le rond rouge** pour la supprimer » — **vestige** de l'ancienne version ronde, jamais mis à jour quand le badge est passé au **`×` anguleux** (direction B ci-dessus). Le texte est maintenant **« le × rouge »** → mot ↔ élément alignés. *Leçon : quand on change la FORME d'un contrôle, grep le texte d'aide qui le décrit.*
- ✅ **Icônes de marque génériques** : Firebase affichait le **« G » de Google**, Neon un **globe** — voir « Icônes » ci-dessous.

## Pistes écartées (badge suppression)
- **A — fantôme** (anneau rouge quasi effacé au repos) : trop discret, risque de ne pas voir qu'on peut supprimer.
- **C — onglet biseauté rouge sombre** : stylé mais reste un peu rouge au repos (moins calme que B).
- **Rond (border-radius:50%)** sous toutes ses formes : **corps étranger** dans un système 100 % anguleux (cartes/icônes biseautées). Ne pas y revenir.

## Dette design — OUVERTE
- ⚪ **Dépendance favicon Google** (faible) : hors les 2 overlays Firebase/Neon, les icônes restent servies par Google (1 appel/carte, casse hors-ligne). Voir « Icônes — stratégie ».
- ⚪ **Sous-titres incohérents** : « Skills » et « Telandros » (roadmap) ont `desc` vide → fallback domaine nu « github.com » alors que les voisins montrent le chemin complet. Cosmétique — remplir `desc` ou harmoniser le fallback.

## Icônes — stratégie (décision 28 juin 2026)
- **Par défaut** : favicon-par-domaine via `google.com/s2/favicons?domain=<hostname>` (un service externe, simple). Limite : pour certains **sous-domaines**, le service renvoie une icône **générique** — le « G » Google pour `console.firebase.google.com`, un **globe** pour `console.neon.tech`. Le repli `onerror` ne sauve pas (le favicon générique se charge en 200, ce n'est pas une erreur).
- **Override par domaine** (`ICON_OVERRIDE` dans `index.html`) : map `hostname → URL d'icône`, avec repli sur le service.
  - **Firebase** → **flamme SVG inline** (data-URI, `fill:#FFA000`) : robuste et **hors-ligne**, car même `firebase.google.com` renvoie le « G ».
  - **Neon** → favicon de la **racine de marque** `neon.tech` (logo Neon réel) au lieu du sous-domaine console.
- **Dette ouverte (faible)** : tout sauf les 2 overrides dépend encore du service Google (1 appel par carte, casse hors-ligne). Si on veut couper la dépendance, basculer les icônes restantes en SVG inline / assets locaux.

## Note technique (piège à connaître)
**`clip-path` rogne aussi les ENFANTS qui débordent**, pas seulement le focus. Un badge d'angle / tooltip en position absolue avec offset négatif sur une carte biseautée sera **tranché** — il faut le garder *dedans*, ou retirer le `clip-path` du parent.

## Note capture (piège headless à connaître)
Le sous-titre « coupé » vu à `--window-size=390` était un **artefact** : headless Chrome a une largeur de fenêtre **minimale ~500 px**, il rend à ~500 et **recadre** à 390 → faux débordement. Vérifier le mobile à **~520 px** (proxy breakpoint) ou en émulation d'appareil, jamais en `--window-size=390` brut.
