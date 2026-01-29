# Claude Code Manim Skills

Skills pour [Claude Code](https://claude.ai/claude-code) permettant de générer des animations mathématiques avec [Manim](https://www.manim.community/) (la bibliothèque utilisée par 3Blue1Brown).

## Skills incluses

### `/manim` - Générateur d'animations

Génère des animations Manim à partir de descriptions en langage naturel.

```
/manim Visualisation de la dérivée comme pente de la tangente --render --quality h
```

**Options :**
- `--render` : Lance le rendu après génération du code
- `--quality l|m|h` : Qualité de rendu (low 480p / medium 720p / high 1080p)
- `--3d` : Force une scène 3D

### `/video-course` - Créateur de vidéos éducatives

Crée des vidéos longues en combinant plusieurs animations Manim avec un workflow interactif.

```
/video-course Introduction aux intégrales pour lycéens --render --quality h
```

**Workflow :**
1. **Conception** - Discussion interactive pour définir la structure
2. **Script YAML** - Génération et validation du script par l'utilisateur
3. **Rendu** - Génération de chaque scène (avec gestion des erreurs)
4. **Assemblage** - Fusion des vidéos avec FFmpeg + transitions

## Installation

### Prérequis

```bash
# Manim
pip install manim

# FFmpeg
winget install ffmpeg      # Windows
brew install ffmpeg        # macOS
sudo apt install ffmpeg    # Linux
```

### Installation des skills

```bash
# Cloner le repo
git clone https://github.com/AureClai/claude-code-manim-skills.git
cd claude-code-manim-skills

# Copier dans le dossier skills de Claude Code
# Windows
copy /Y manim %USERPROFILE%\.claude\skills\manim
copy /Y video-course %USERPROFILE%\.claude\skills\video-course

# macOS / Linux
cp -r manim ~/.claude/skills/
cp -r video-course ~/.claude/skills/
```

Redémarrer Claude Code pour charger les skills.

## Exemples

### Animation simple

```
/manim Cercle qui se transforme en carré avec les formules d'aire
```

### Animation 3D

```
/manim Surface z = sin(x) * cos(y) en 3D avec rotation de caméra --3d --render
```

### Vidéo complète

```
/video-course Les lois de Kepler expliquées visuellement --render --quality h
```

## Structure des skills

```
manim/
├── SKILL.md                 # Prompt principal
├── reference/
│   ├── manim-api.md         # Référence API Manim
│   ├── animation-patterns.md # Patterns d'animation
│   └── 3d-reference.md      # Référence 3D
├── examples/
│   ├── calculus.md          # Exemples calcul
│   ├── linear-algebra.md    # Exemples algèbre linéaire
│   ├── geometry.md          # Exemples géométrie
│   └── 3d-scenes.md         # Exemples 3D
└── templates/
    └── scene-template.md    # Template de scène

video-course/
├── SKILL.md                 # Prompt principal
├── README.md                # Documentation
├── reference/
│   ├── yaml-schema.md       # Schéma YAML complet
│   ├── scene-types.md       # Types de scènes
│   └── ffmpeg-commands.md   # Commandes FFmpeg
└── examples/
    └── derivees-introduction.yaml  # Exemple de projet
```

## Licence

MIT License - voir [LICENSE](LICENSE)

## Crédits

- [Manim Community](https://www.manim.community/) - Bibliothèque d'animation
- [3Blue1Brown](https://www.3blue1brown.com/) - Inspiration pour le style visuel
- [Claude Code](https://claude.ai/claude-code) - CLI Anthropic
