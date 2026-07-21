# 🧍 Person Segmentation — U-Net from scratch → transfer learning

> Recoder le **fond flou de Zoom** : un réseau qui prédit, pour **chaque pixel** d'une
> photo, s'il appartient à une **personne** ou au **fond**.

Projet de deep learning en **PyTorch**, mené en trois versions : du U-Net écrit à la main
jusqu'à une version optimisée à encoder pré-entraîné.

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-2.9-EE4C2C?logo=pytorch&logoColor=white)
![Licence](https://img.shields.io/badge/licence-MIT-green)

---

## 🎯 Le problème

La **segmentation binaire d'images** : à partir d'une photo RGB, produire un **masque**
de la même taille où chaque pixel vaut 1 (personne) ou 0 (fond).

```
photo RGB (3, H, W)  ──►  U-Net  ──►  masque (1, H, W)
```

C'est la brique derrière le fond flou de Zoom, les stickers Instagram, le détourage
automatique de Photoshop ou remove.bg.

---

## 📊 Résultats

| Version | Modèle | Résolution | Dataset | Dice (test) | IoU (test) |
|---|---|---|---|---|---|
| **v1** | `TinyUNet` from scratch (118 K params) | 128 px | Penn-Fudan (170 img) | **0,64** | **0,49** |
| **v2** | idem, sur dataset élargi | 128 px | Supervisely (2 667 img) | *à exécuter* | *à exécuter* |
| **v3** | `ResNet18-UNet` pré-entraîné (14,3 M params) | 256 px | Supervisely (2 667 img) | *à exécuter* | *à exécuter* |

> **Honnêteté sur les chiffres** : seule la ligne **v1** est un résultat mesuré
> (5 epochs, GPU AMD Radeon 880M via ROCm). Les v2 et v3 ont été **validées
> techniquement** (exécution de bout en bout, tests unitaires passés) mais leur
> entraînement complet reste à lancer — les cases sont là pour que tu y reportes tes
> propres mesures.
>
> Le **Dice** et l'**IoU** valent 1 pour une prédiction parfaite, 0 pour une prédiction
> totalement fausse. IoU est mathématiquement toujours inférieur au Dice.

---

## 🗂️ Architecture du dépôt

```
person-segmentation-unet/
│
├── notebooks/                        🧪 les 3 versions du projet
│   ├── 01_tp_unet_from_scratch.ipynb    U-Net écrit à la main, Penn-Fudan
│   ├── 02_colab_dataset_kaggle.ipynb    même pipeline, dataset Kaggle, pensé Colab
│   └── 03_resnet_unet_transfer.ipynb    version performance : encoder pré-entraîné
│
├── cours/                            📖 les mêmes, ANNOTÉS ligne par ligne
│   ├── 01_tp_unet_from_scratch_annote.ipynb
│   ├── 02_colab_dataset_kaggle_annote.ipynb
│   └── 03_resnet_unet_transfer_annote.ipynb
│
├── slides/                           🎬 support de présentation (7 diapos)
├── sujet/                            📄 l'énoncé d'origine
├── data/                             📦 datasets (ignorés par git, téléchargés auto)
└── requirements.txt
```

👉 **Pour comprendre** : ouvre [`cours/`](cours/) — chaque ligne de code y est commentée.
👉 **Pour exécuter** : ouvre [`notebooks/`](notebooks/).

---

## 🚀 Démarrage rapide

```bash
git clone https://github.com/RobinsanKiritheepan/person-segmentation-unet.git
cd person-segmentation-unet
pip install -r requirements.txt
jupyter lab notebooks/01_tp_unet_from_scratch.ipynb
```

**Aucun téléchargement manuel** : le notebook v1 récupère Penn-Fudan (~50 Mo) tout seul
au premier lancement, et la v2/v3 utilise `kagglehub`.

> ⚠️ **Sur Google Colab** : le notebook `02` est prévu pour ça (`Run All` après avoir
> choisi un runtime **GPU T4**).

---

## 🧠 Les 3 versions, et ce qu'elles apprennent

### v1 — Le U-Net à la main

L'architecture complète écrite brique par brique : encoder qui compresse, bottleneck,
decoder qui reconstruit, et surtout les **skip connections** qui transportent le détail
spatial perdu au pooling.

La loss combine **BCE** (précision pixel par pixel) et **Soft Dice** (recouvrement des
zones). Le « soft » est essentiel : le seuillage n'est pas dérivable, donc la loss
travaille sur les **probabilités continues** — le seuillage n'arrive qu'à l'évaluation.

### v2 — Passage à l'échelle

Même pipeline, dataset 15× plus grand (2 667 images via `kagglehub`), pensé pour tourner
sur Colab. L'appariement image ↔ masque se fait **par nom de fichier** plutôt que par tri
parallèle — plus robuste quand les extensions diffèrent.

### v3 — Transfer learning

Le vrai levier de performance. L'encoder devient un **ResNet18 pré-entraîné sur ImageNet**
(1,2 M d'images) : le réseau arrive en sachant déjà détecter contours et textures, et
n'apprend plus qu'à les assembler en masque.

S'y ajoutent : résolution 256, augmentation renforcée (crop à échelle variable + color
jitter), normalisation ImageNet, **AdamW** + **cosine LR**, et conservation du meilleur
checkpoint.

---

## 🛠️ Stack

`PyTorch` · `torchvision` · `NumPy` · `Pillow` · `matplotlib` · `OpenCV` · `kagglehub`

Testé sur **AMD Radeon 880M via ROCm** (`torch 2.9.1+rocm7.2.1`) et sur **Google Colab
(T4)**. Le code détecte le matériel automatiquement et retombe sur CPU si besoin.

---

## 📚 Datasets

| Dataset | Taille | Usage | Source |
|---|---|---|---|
| **Penn-Fudan Pedestrian** | 170 images, ~50 Mo | v1 | [UPenn](https://www.cis.upenn.edu/~jshi/ped_html/) |
| **Supervisely Person (filtré)** | 2 667 images, ~4,3 Go | v2, v3 | [Kaggle](https://www.kaggle.com/datasets/tapakah68/supervisely-filtered-segmentation-person-dataset) |

Les deux se téléchargent automatiquement. `data/` est volontairement exclu du dépôt.

---

## 📝 Licence

MIT — voir [LICENSE](LICENSE).

Les datasets restent soumis à leurs licences respectives.
