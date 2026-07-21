# data/

**Ce dossier est volontairement vide sur le dépôt** (exclu par `.gitignore`).

Les datasets pèsent plusieurs Go et se téléchargent **automatiquement** au premier
lancement des notebooks. Les versionner rendrait le clone inutilisable.

## Ce qui atterrit ici

```
data/
└── PennFudanPed/
    ├── PNGImages/    les photos
    └── PedMasks/     les masques correspondants (même nom de fichier)
```

| Dataset | Taille | Qui le télécharge | Comment |
|---|---|---|---|
| **Penn-Fudan Pedestrian** (170 images) | ~50 Mo | notebook `01` | `urllib` + décompression, vers `data/PennFudanPed/` |
| **Supervisely Person** (2 667 images) | ~4,3 Go | notebooks `02` et `03` | `kagglehub`, vers le cache `~/.cache/kagglehub/` |

## Rien à faire de ton côté

Les notebooks sont **idempotents** : ils vérifient si le dossier existe déjà et ne
retéléchargent pas. Lance simplement la cellule de téléchargement.

> ⚠️ **Piège connu** : l'archive officielle de Penn-Fudan crée parfois un dossier
> **imbriqué** (`PennFudanPed/PennFudanPed/`). Le notebook `03` utilise `rglob` pour
> trouver `PNGImages/` et `PedMasks/` où qu'ils soient — mais si le `01` te renvoie
> « 0 couple image/masque », c'est là qu'il faut regarder.

## Utiliser tes propres données

Il suffit de respecter la structure : un dossier d'images, un dossier de masques, et
**le même nom de fichier** de part et d'autre. Les masques doivent être binarisables
(tout pixel non nul = personne).
