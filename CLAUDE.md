# Partogramme — ce qu'il faut savoir

Outil de suivi du travail obstétrical (le partogramme) : on suit une patiente, sa
grossesse et les examens pendant le travail. Public : étudiants sage-femme.

## État actuel

- Une seule page : `index.html`. Pour le moment elle gère seulement les patientes
  (ajout, modification en cliquant sur une ligne, liste).
- Le reste de la base (grossesses, examens…) est prévu mais pas encore fait dans la page.
- Au 2026-09-30 : le site en ligne https://sps-g43-parto.professeurpetitchat.com/
  renvoie 404 (problème de mise en ligne à régler).

## Base de données Supabase

Projet : `veuzjdbmtvaynqxxandp` (l'URL et la clé publishable sont déjà dans `index.html`).

Tables (toutes avec RLS activé, sauf `enfant` — voir « Sécurité » ci-dessous) :

- `Patiente` : `id_patiente`, `nom`, `nom_marital`, `prenom`, `gs_rh`, `date_naissance`
  - `gs_rh` est un type enum `groupe_sanguin` : A+, A-, O+, O-, AB+, AB-, inconnu
- `grossesse` : dates et durées du travail, liaisons vers la patiente et 4 praticiens
- `praticien` : nom, prénom, `specialite` (enum : sage-femme, Anesthésiste, Obstétricien, Pédiatre)
- `orientation` : nom de l'orientation et fichier du pictogramme associé
- `examen` : un examen pendant le travail (date/heure, dilatation, hauteur de la présentation,
  orientation, remarque) — c'est le cœur du partogramme
- `enfant` : naissance(s) d'une grossesse (jumeaux possibles : plusieurs enfants par grossesse)

Relations : une `Patiente` a des `grossesse`s ; chaque `grossesse` a des `examen`s
et des `enfant`s ; chaque `examen` pointe vers une `orientation` ; les praticiens
de la `grossesse` viennent de `praticien`.

## Sécurité (à régler)

La table `enfant` n'a pas de Row Level Security (RLS) : n'importe qui avec la clé
publishable peut la lire et la modifier. Le reste de la base est protégé. Pour
activer la protection : `ALTER TABLE "public"."enfant" ENABLE ROW LEVEL SECURITY;`
(mais attention : sans politique d'accès, la table deviendra inaccessible depuis
le site — il faudra ajouter des politiques).
