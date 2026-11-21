# Données brutes (bronze) — datasets

Instantanés statiques des données brutes utilisées par le dépôt
[Présentations-Seances](https://github.com/Mentorat-2026-FY/Presentations-Seances)
(cours « Entrepôt de données — Je valide ça, service-conseil »).

## Pourquoi ce dépôt existe

Plusieurs sources publiques (StatCan, FMI, FAO, BLS…) bloquent ou limitent les
requêtes provenant des adresses IP partagées des runners GitHub Actions. Pour
que la CI reste déterministe et reproductible, les données brutes sont
versionnées ici et la CI les récupère par `actions/checkout` plutôt que de les
télécharger en ligne. Aucun secret : tout est ouvert et documenté.

## Structure

Le contenu reproduit l'arborescence `data/` du dépôt principal, que la CI
branche directement sur le chemin `data/` :

- `raw/` — fichiers bruts téléchargés par `scripts/fetch_raw_data.py`
  (manifeste `raw/manifest_sha256.json` : URL, date, empreinte SHA-256) ;
- `geospatial/` — instantané WMS GetCapabilities StatCan utilisé par la
  séance 4.

## Fichier compressé

`raw/statcan/18100004.csv` (154 Mo) dépasse la limite GitHub de 100 Mo par
fichier : il est stocké compressé sous `raw/statcan/18100004.csv.gz` (~15 Mo,
`gzip` déterministe). La CI le décompresse avant le pipeline ; en local,
`gzip -dk raw/statcan/18100004.csv.gz` produit le fichier attendu.

## Mise à jour

Les instantanés sont régénérés localement (réseau résidentiel) :

1. `python scripts/fetch_raw_data.py` dans le dépôt principal ;
2. recopie de `data/raw/` et `data/geospatial/` ici, avec compression du
   gros CSV StatCan ;
3. mise à jour du manifeste SHA-256 (`raw/manifest_sha256.json`), qui sert
   de contrôle d'intégrité dans les tests.

Empreinte de référence : `raw/statcan/18100004.csv` →
`73a9a1528412c7fa2ab2d9100ec61f97fafe886379cf8192c0b13cd58533fc75`.
