# Compta OHADA — comptabilité SYSCOHADA en ligne (fork de Frappe Books)

Application de comptabilité **100 % navigateur**, hébergeable gratuitement
(site statique). Dérivée de [Frappe Books](https://github.com/frappe/books)
(licence **AGPL-3.0**).

## Ce qui a été fait

| Domaine | Détail |
|---|---|
| **Plan comptable SYSCOHADA révisé** | `fixtures/verified/ohada.json` : 768 comptes (classes 1 à 8). Généré par `scripts/ohada/build_coa.py` depuis `scripts/ohada/coa_data.txt` (éditez ce fichier puis relancez le script). |
| **Pays OHADA** | 17 États membres : plan SYSCOHADA proposé par défaut, exercice = année civile, locales françaises, devise RDC (CDF) ajoutée (`utils/ohada.ts`). |
| **FCFA** | Affichage sans décimales pour XOF / XAF / KMF / GNF. |
| **TVA** | À la création de la société : « TVA collectée x % » (compte 4431) et « TVA déductible x % » (compte 4452), taux normal du pays + exonéré. |
| **Comptes par défaut** | Clients 4111, Fournisseurs 4011, Ventes 7011 / Services 706, remises 7019, pertes sur créances 6511, arrondis 6588, stock 311, FNP 4081, banque 521, caisse 571. |
| **Version web** | Base SQLite exécutée dans le navigateur (sql.js/WebAssembly, Web Worker), stockée dans IndexedDB ; interface en français par défaut (`web/`). |
| **Sauvegarde** | Bouton « Télécharger la sauvegarde » (fichier `.db`) ; import via « Fichier existant ». |

## État de vérification (important)

L'environnement de développement n'avait pas accès à npm : **le code web n'a
jamais été compilé ni exécuté**. Seuls ont été vérifiés : la structure du plan
comptable (script de validation, aucun doublon, comptes par défaut présents) et
la syntaxe TypeScript des fichiers modifiés. Prévoyez une première session de
mise au point (voir « Premier lancement »).

## Premier lancement

```bash
npm install --legacy-peer-deps --ignore-scripts
npm run dev        # http://localhost:5173
npm run build      # site statique dans web/dist
```

Points les plus susceptibles de demander un ajustement : bundling de `knex`
dans le worker (alias/polyfills dans `web/vite.config.ts`), types `sql.js`,
et les chemins d'import du client knex (`web/src/worker/knexClient.ts`).

## Hébergement gratuit

- **GitHub Pages** : poussez le dépôt (public) sur GitHub, activez
  *Settings → Pages → Source : GitHub Actions* ; `.github/workflows/deploy.yml` s'en charge.
- **Cloudflare Pages** : build `npm install --legacy-peer-deps --ignore-scripts && npm run build`, dossier `web/dist`.

## Limites actuelles

- Les données restent **dans le navigateur de l'utilisateur** (pas de compte, pas de multi-utilisateur,
  pas de synchronisation). Faites des sauvegardes régulières. Un seul onglet à la fois.
- Impression / PDF : via la boîte d'impression du navigateur.
- Comptabilité par inventaire intermittent (6031 variations de stocks) non automatisée : l'inventaire
  permanent de Frappe Books est utilisé (COGS = 6011).
- Classe 9 (engagements hors bilan, analytique) non incluse.
- Taux de TVA : indicatifs, à vérifier auprès de l'administration fiscale du pays.
- Les états Bilan / Compte de résultat standards sont regroupés par nature de compte ;
  la présentation officielle SYSCOHADA (tableaux avec références AD, AE… , TAFIRE/flux de trésorerie,
  notes annexes) reste à développer.

## Pistes pour un vrai mode « en ligne » multi-utilisateur

Synchronisation/stockage serveur avec une offre gratuite (Supabase, Turso ou Cloudflare D1) et
authentification ; le point d'extension est la couche `web/src/ipc.ts` + `web/src/worker/`.

## Licence

AGPL-3.0 : si vous mettez ce logiciel en ligne, vous devez fournir le code source
(avec vos modifications) aux utilisateurs — gardez le dépôt public et ajoutez un lien « Code source » dans l'application.
