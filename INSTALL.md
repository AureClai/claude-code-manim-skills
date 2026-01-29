# Installation des Skills Manim

## Prérequis

### 1. Manim
```bash
pip install manim
```

### 2. FFmpeg
**Windows :**
```bash
winget install --id Gyan.FFmpeg -e
```

**macOS :**
```bash
brew install ffmpeg
```

**Linux :**
```bash
sudo apt install ffmpeg
```

## Installation des Skills

Copier les dossiers `manim` et `video-course` dans :

```
~/.claude/skills/
```

### Windows
```
C:\Users\<VOTRE_USER>\.claude\skills\manim\
C:\Users\<VOTRE_USER>\.claude\skills\video-course\
```

### macOS / Linux
```
~/.claude/skills/manim/
~/.claude/skills/video-course/
```

## Structure attendue

```
~/.claude/skills/
├── manim/
│   ├── SKILL.md
│   ├── reference/
│   ├── examples/
│   └── templates/
└── video-course/
    ├── SKILL.md
    ├── README.md
    ├── reference/
    ├── examples/
    └── templates/
```

## Utilisation

Après installation, redémarrer Claude Code puis :

```
/manim Animation d'une fonction sinus --render
/video-course Vidéo sur les intégrales --render --quality h
```

## Skills incluses

### /manim
Génère des animations Manim à partir de descriptions en langage naturel.

Options :
- `--render` : Lancer le rendu après génération
- `--quality l|m|h` : Qualité (low/medium/high)
- `--3d` : Forcer une scène 3D

### /video-course
Crée des vidéos éducatives longues en combinant plusieurs animations Manim.

Workflow :
1. Conception interactive de la structure
2. Génération du script YAML (validation requise)
3. Rendu de chaque scène
4. Assemblage avec FFmpeg
