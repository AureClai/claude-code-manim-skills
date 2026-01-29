# Schéma YAML - Video Course

Spécification complète du format de script pour `/video-course`.

## Structure Racine

```yaml
metadata:      # Informations sur le projet (requis)
style:         # Style visuel global (requis)
scenes:        # Liste des scènes (requis, min 1)
audio:         # Configuration audio (optionnel)
output:        # Configuration de sortie (requis)
```

## Metadata

```yaml
metadata:
  title: string           # Titre de la vidéo (requis)
  description: string     # Description courte (optionnel)
  author: string          # Auteur (optionnel)
  language: string        # Code langue: "fr", "en", etc. (défaut: "fr")
  target_audience: string # Public cible (optionnel)
  tags: [string]          # Tags pour organisation (optionnel)
  version: string         # Version du script (optionnel)
  created_at: date        # Date de création (auto)
  estimated_duration: string  # Durée estimée: "5min", "10min" (optionnel)
```

## Style

```yaml
style:
  quality: string         # "l" (480p), "m" (720p), "h" (1080p) (défaut: "m")
  fps: integer            # Images par seconde (défaut: 60)

  color_palette:
    primary: string       # Couleur principale (défaut: "BLUE")
    secondary: string     # Couleur secondaire (défaut: "YELLOW")
    accent: string        # Couleur d'accent (défaut: "RED")
    background: string    # Fond (défaut: "BLACK")
    text: string          # Texte (défaut: "WHITE")
    positive: string      # Valeurs positives (défaut: "GREEN")
    negative: string      # Valeurs négatives (défaut: "RED")

  typography:
    title_size: integer   # Taille titres (défaut: 72)
    subtitle_size: integer # Taille sous-titres (défaut: 48)
    text_size: integer    # Taille texte normal (défaut: 36)
    code_size: integer    # Taille code (défaut: 24)

  transitions:
    default: string       # Transition par défaut: "fade", "cut", "none"
    duration: float       # Durée en secondes (défaut: 0.5)
```

### Couleurs Disponibles (Manim)

```
WHITE, BLACK, GREY, GREY_A, GREY_B, GREY_C, GREY_D, GREY_E
RED, RED_A, RED_B, RED_C, RED_D, RED_E
GREEN, GREEN_A, GREEN_B, GREEN_C, GREEN_D, GREEN_E
BLUE, BLUE_A, BLUE_B, BLUE_C, BLUE_D, BLUE_E
YELLOW, YELLOW_A, YELLOW_B, YELLOW_C, YELLOW_D, YELLOW_E
GOLD, GOLD_A, GOLD_B, GOLD_C, GOLD_D, GOLD_E
ORANGE, PINK, PURPLE, TEAL, MAROON
```

## Scenes

Liste ordonnée des scènes à générer.

```yaml
scenes:
  - id: string            # Identifiant unique (requis)
    name: string          # Nom affiché (requis)
    description: string   # Description pour génération (optionnel)
    type: string          # Type de scène (requis)
    duration: string      # Durée cible: "30s", "1min" (optionnel)
    content: object       # Contenu spécifique au type (requis)
    transition_in: string # Transition d'entrée (optionnel)
    transition_out: string # Transition de sortie (défaut: style.transitions.default)
    skip: boolean         # Ignorer cette scène (défaut: false)
```

### Type: title_card

Écran titre statique ou avec animation simple.

```yaml
- id: "intro"
  name: "Introduction"
  type: "title_card"
  duration: "5s"
  content:
    title: "Les Dérivées"           # Titre principal (requis)
    subtitle: "Calcul différentiel" # Sous-titre (optionnel)
    author: "Prof. Martin"          # Auteur affiché (optionnel)
    animation: "fade_in"            # "fade_in", "write", "grow" (défaut: "fade_in")
  transition_out: "fade"
```

### Type: animation

Animation Manim complète générée via `/manim`.

```yaml
- id: "tangent_demo"
  name: "Visualisation de la tangente"
  type: "animation"
  content:
    concept: |
      Montrer une courbe f(x) = x² avec une tangente mobile.
      La tangente glisse le long de la courbe pendant que
      la pente s'affiche en temps réel.

    # Paramètres optionnels pour /manim
    three_d: false        # Forcer scène 3D

    # Éléments visuels à inclure (guide pour la génération)
    elements:
      - type: "axes"
        x_range: [-3, 3]
        y_range: [-1, 9]
      - type: "function"
        expression: "x**2"
        color: "primary"   # Référence à style.color_palette
      - type: "tangent_line"
        animated: true
      - type: "slope_display"
        position: "top_right"

    # Phases de l'animation (optionnel, guide la structure)
    phases:
      - name: "setup"
        description: "Afficher les axes et la courbe"
        duration: "3s"
      - name: "demonstration"
        description: "Animer la tangente de x=-2 à x=2"
        duration: "6s"
      - name: "highlight"
        description: "Mettre en évidence la pente à x=1"
        duration: "3s"

  transition_out: "fade"
```

### Type: diagram

Schéma ou diagramme statique/simple.

```yaml
- id: "concept_map"
  name: "Carte conceptuelle"
  type: "diagram"
  duration: "8s"
  content:
    layout: "tree"        # "tree", "flowchart", "radial", "grid"

    nodes:
      - id: "root"
        label: "Dérivée"
        color: "primary"
      - id: "geom"
        label: "Interprétation\ngéométrique"
        parent: "root"
      - id: "phys"
        label: "Interprétation\nphysique"
        parent: "root"
      - id: "tangent"
        label: "Pente de\nla tangente"
        parent: "geom"
      - id: "velocity"
        label: "Vitesse\ninstantanée"
        parent: "phys"

    edges:
      - from: "root"
        to: "geom"
        label: ""
      - from: "root"
        to: "phys"
        label: ""

    animation: "build"    # "none", "build", "fade_in"

  transition_out: "fade"
```

### Type: summary

Écran récapitulatif avec points clés.

```yaml
- id: "recap"
  name: "Points clés"
  type: "summary"
  duration: "15s"
  content:
    title: "À retenir"    # Titre de la section (optionnel)

    points:
      - text: "La dérivée mesure le taux de variation instantané"
        color: "primary"
        icon: "bullet"    # "bullet", "number", "check", "arrow"
      - text: "Géométriquement : pente de la tangente"
        color: "secondary"
      - text: "Physiquement : vitesse instantanée"
        color: "accent"

    animation: "sequential"  # "sequential", "simultaneous", "none"
    delay_between: 1.0       # Délai entre points (secondes)

  transition_out: "fade_to_black"
```

### Type: equation

Mise en évidence d'une équation ou formule.

```yaml
- id: "formula"
  name: "Formule de la dérivée"
  type: "equation"
  duration: "10s"
  content:
    # Note: Éviter LaTeX si problèmes, utiliser Unicode ou Text
    equation: "f'(x) = lim[h→0] (f(x+h) - f(x)) / h"

    # OU format décomposé (plus fiable)
    parts:
      - text: "f'(x)"
        color: "primary"
      - text: " = "
        color: "text"
      - text: "lim"
        color: "secondary"
        subscript: "h→0"
      - text: " (f(x+h) - f(x)) / h"
        color: "text"

    highlight_sequence:   # Animation de mise en évidence
      - parts: [0]
        description: "La dérivée de f"
      - parts: [2]
        description: "Quand h tend vers 0"
      - parts: [4]
        description: "Le taux de variation"

  transition_out: "fade"
```

### Type: comparison

Comparaison côte à côte.

```yaml
- id: "compare"
  name: "Avant/Après"
  type: "comparison"
  duration: "12s"
  content:
    layout: "side_by_side"  # "side_by_side", "top_bottom", "overlay"

    left:
      title: "Taux moyen"
      content: "Animation de la sécante"
      # ou
      image: "path/to/image.png"

    right:
      title: "Taux instantané"
      content: "Animation de la tangente"

    highlight_differences: true

  transition_out: "fade"
```

## Audio

Configuration audio optionnelle.

```yaml
audio:
  background_music:
    file: "path/to/music.mp3"   # Chemin vers fichier audio
    volume: 0.3                  # Volume 0.0 à 1.0
    fade_in: 2.0                 # Durée fade in (secondes)
    fade_out: 3.0                # Durée fade out (secondes)
    loop: true                   # Boucler si vidéo plus longue

  # Narration TTS (futur)
  narration:
    enabled: false
    voice: "fr-FR-Standard-A"
    script_file: "narration.txt"
```

## Output

Configuration de la sortie.

```yaml
output:
  filename: "derivees_expliquees"  # Nom sans extension (requis)
  format: "mp4"                    # "mp4", "webm", "mov" (défaut: "mp4")
  resolution: "1080p"              # "480p", "720p", "1080p", "4k"

  # Options avancées
  codec:
    video: "libx264"               # Codec vidéo
    audio: "aac"                   # Codec audio
    preset: "medium"               # Preset ffmpeg: "fast", "medium", "slow"

  # Fichiers à conserver
  keep_scenes: true                # Garder les .mp4 individuels
  keep_sources: true               # Garder les .py Manim

  # Destination
  output_dir: "output"             # Dossier de sortie
```

## Exemple Complet

```yaml
metadata:
  title: "Introduction aux Dérivées"
  description: "Comprendre le concept de dérivée de manière visuelle"
  language: "fr"
  target_audience: "Lycéens, étudiants en mathématiques"
  estimated_duration: "5min"

style:
  quality: "h"
  color_palette:
    primary: "BLUE"
    secondary: "YELLOW"
    accent: "RED"
  transitions:
    default: "fade"
    duration: 0.5

scenes:
  - id: "title"
    name: "Titre"
    type: "title_card"
    duration: "4s"
    content:
      title: "Les Dérivées"
      subtitle: "Visualiser le changement instantané"
    transition_out: "fade"

  - id: "question"
    name: "La question"
    type: "animation"
    content:
      concept: |
        Afficher la question "Comment mesurer un changement instantané ?"
        avec une animation de voiture dont la vitesse varie.
        Montrer le compteur de vitesse.

  - id: "secant_to_tangent"
    name: "De la sécante à la tangente"
    type: "animation"
    content:
      concept: |
        Sur une courbe f(x)=x², montrer une sécante entre deux points.
        Animer le rapprochement des deux points jusqu'à ce que
        la sécante devienne une tangente.
        Afficher la formule du taux de variation qui devient la dérivée.
      phases:
        - name: "secante"
          description: "Montrer la sécante entre x=1 et x=3"
        - name: "rapprochement"
          description: "Animer x=3 vers x=1"
        - name: "tangente"
          description: "Afficher la tangente limite"

  - id: "summary"
    name: "Résumé"
    type: "summary"
    duration: "10s"
    content:
      title: "À retenir"
      points:
        - text: "La dérivée = limite du taux de variation"
        - text: "Géométrie : pente de la tangente"
        - text: "Physique : vitesse instantanée"
    transition_out: "fade_to_black"

output:
  filename: "intro_derivees"
  resolution: "1080p"
  keep_scenes: true
```
