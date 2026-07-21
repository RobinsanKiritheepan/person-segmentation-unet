# notebooks/ — les 3 versions à exécuter

Les notebooks **nus**, prêts à tourner. Pour les versions expliquées ligne par ligne,
voir [`../cours`](../cours/).

| Notebook | Modèle | Dataset | Où le lancer |
|---|---|---|---|
| `01_tp_unet_from_scratch.ipynb` | `TinyUNet` écrit à la main (118 K params) | Penn-Fudan, 170 img (téléchargé auto) | local |
| `02_colab_dataset_kaggle.ipynb` | idem | Supervisely, 2 667 img (`kagglehub`) | **Colab (T4)** |
| `03_resnet_unet_transfer.ipynb` | `ResNet18-UNet` pré-entraîné (14,3 M params) | Supervisely | local ou Colab |

## Ordre conseillé

**01 → 03.** Le premier construit tout à la main (c'est là qu'on comprend), le troisième
montre ce que le transfer learning change vraiment.

## Avant de lancer

Les données arrivent toutes seules — rien à télécharger manuellement. Le dossier
[`../data`](../data/) se remplit au premier run.

**Les chemins sont relatifs à ce dossier** : `../data/PennFudanPed`, `../photos_test`.
Lance donc les notebooks depuis `notebooks/` (ce que fait Jupyter par défaut).

## Réglages à connaître

| Constante | Défaut | Quand la changer |
|---|---|---|
| `EPOCHS` | 5 (v1) / 30 (v3) | baisse-la si l'entraînement est trop long |
| `BATCH_SIZE` | 8 | baisse à 4 si tu manques de mémoire GPU |
| `IMG_SIZE` | 128 (v1) / 256 (v3) | plus grand = plus fin mais bien plus lent |

## Deux pièges de plateforme

- **Windows** : `num_workers` doit rester à `0` dans les `DataLoader` — le
  multiprocessing plante sous Jupyter/Windows.
- **AMD / ROCm** : `BatchNorm2d` peut échouer (`miopenStatusUnknownError`) si aucun
  compilateur C++ n'est installé — ROCm compile ses kernels à la volée. Solution : pointer
  `CPLUS_INCLUDE_PATH` vers des headers C++, ou forcer `DEVICE = torch.device("cpu")`.

## Mission 7 — tester sur ta propre photo

Dépose une image dans `../photos_test/` puis exécute la cellule correspondante. Le dossier
est exclu du dépôt (photos personnelles).
