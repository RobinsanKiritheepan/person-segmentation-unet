# 📖 cours/ — les notebooks annotés

Les trois notebooks de [`../notebooks`](../notebooks/), repris **à l'identique** mais
annotés en profondeur : un bloc de cours avant chaque cellule, et **chaque ligne de code
commentée**.

C'est ici qu'il faut venir pour **comprendre**. Les notebooks d'origine restent nus, pour
exécuter.

## Contenu

| Fichier | Ce qu'on y explique |
|---|---|
| `01_tp_unet_from_scratch_annote.ipynb` | **le cours principal** : lecture d'images, `Dataset` paresseux, BILINEAR vs NEAREST, `nn.Module`, skip connections, Soft Dice, la boucle d'entraînement, le seuil optimal, le fond flou |
| `02_colab_dataset_kaggle_annote.ipynb` | `kagglehub`, l'appariement **par nom de fichier**, le garde Colab |
| `03_resnet_unet_transfer_annote.ipynb` | le **transfer learning**, la normalisation ImageNet, l'augmentation géométrique vs photométrique, AdamW + cosine LR |

Les versions 02 et 03 ne détaillent que ce qui est **nouveau** et renvoient à la 01 pour
tout ce qui est commun — pas de redite.

## Les notions clés, par ordre d'importance

1. **Les skip connections** — le pooling détruit le détail spatial ; les skips le
   transportent de l'encoder au decoder. Sans elles, les masques sont flous.
2. **BILINEAR pour l'image, NEAREST pour le masque** — redimensionner, c'est *inventer*
   des pixels. Une moyenne entre 0 et 1 donne 0,5 : ni fond, ni personne.
3. **Soft Dice** — le seuillage n'est pas dérivable, donc la loss travaille sur les
   probabilités continues. Le seuillage n'intervient qu'à l'évaluation.
4. **Le seuil se règle sur la validation** — jamais sur le test, sinon c'est régler un
   bouton en regardant la réponse.
5. **Transfer learning** — un encoder pré-entraîné sait déjà voir ; sur un petit dataset,
   c'est le levier n°1.

## 🔴 Les 7 bugs rencontrés pendant le projet

Détaillés dans le bilan du notebook `01`. Aucun n'était du deep learning :

| Bug | Leçon |
|---|---|
| `np.array` sans `()` | `f` est la fonction, `f()` son résultat |
| `raise` laissé après le TODO | Python lit ligne par ligne |
| `glob(".png")` sans `*` | le joker est obligatoire |
| archive au dossier imbriqué | `rglob` cherche partout |
| `num_workers=2` sous Windows | `0` obligatoire sous Jupyter/Windows |
| mauvais kernel Python | vérifier *quel* Python exécute le notebook |
| MIOpen sans compilateur C++ | ROCm compile à la volée : il lui faut des headers C++ |

**80 % du métier, c'est de la plomberie.**
