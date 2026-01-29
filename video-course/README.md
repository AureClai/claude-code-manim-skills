# Video Course Skill

Skill pour créer des vidéos éducatives longues combinant plusieurs animations Manim.

## Prérequis

### Manim
```bash
pip install manim
```

### FFmpeg (REQUIS pour l'assemblage)

**Windows :**
1. Télécharger depuis https://ffmpeg.org/download.html
2. Ou via Chocolatey : `choco install ffmpeg`
3. Ou via Winget : `winget install ffmpeg`

**Vérification :**
```bash
ffmpeg -version
```

## Utilisation

```
/video-course Créer une vidéo sur les intégrales pour lycéens
```

### Options

| Option | Description |
|--------|-------------|
| `--render` | Lancer le rendu après validation du script |
| `--quality l/m/h` | Qualité de rendu (défaut: m) |

## Workflow

1. **Conception** - Discussion pour définir la structure
2. **Script YAML** - Génération et validation du script
3. **Génération** - Création de chaque scène via /manim
4. **Assemblage** - Fusion avec FFmpeg + transitions

## Structure du projet généré

```
mon_projet/
├── project.yaml        # Script validé
├── scenes/
│   ├── 01_intro.py
│   ├── 01_intro.mp4
│   └── ...
└── output/
    └── video_finale.mp4
```

## Documentation

- [Schéma YAML](reference/yaml-schema.md)
- [Types de scènes](reference/scene-types.md)
- [Commandes FFmpeg](reference/ffmpeg-commands.md)
- [Exemples](examples/)
