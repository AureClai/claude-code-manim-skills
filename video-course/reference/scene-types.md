# Types de Scènes

Référence détaillée de tous les types de scènes disponibles dans `/video-course`.

## Vue d'Ensemble

| Type | Usage | Complexité | Génération |
|------|-------|------------|------------|
| `title_card` | Titres, transitions textuelles | Simple | Manim direct |
| `animation` | Animations mathématiques complètes | Complexe | Via `/manim` |
| `diagram` | Schémas, arbres, flowcharts | Moyenne | Manim direct |
| `summary` | Points clés, récapitulatifs | Simple | Manim direct |
| `equation` | Formules mathématiques | Moyenne | Manim (Text) |
| `comparison` | Comparaisons côte à côte | Moyenne | Manim direct |
| `code_demo` | Démonstration de code | Moyenne | Manim direct |

---

## title_card

Écran titre pour introductions, transitions entre chapitres, ou crédits.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `title` | string | Oui | - | Texte principal |
| `subtitle` | string | Non | null | Texte secondaire |
| `author` | string | Non | null | Nom de l'auteur |
| `date` | string | Non | null | Date affichée |
| `animation` | string | Non | "fade_in" | Animation d'entrée |
| `background` | string | Non | style.background | Couleur de fond |
| `position` | string | Non | "center" | Position: "center", "top", "bottom" |

### Animations disponibles

- `fade_in` : Fondu simple
- `write` : Écriture progressive
- `grow` : Apparition avec zoom
- `typewriter` : Effet machine à écrire

### Exemple

```yaml
- id: "chapter_2"
  name: "Chapitre 2"
  type: "title_card"
  duration: "4s"
  content:
    title: "Chapitre 2"
    subtitle: "Les Applications"
    animation: "write"
    background: "GREY_E"
  transition_out: "fade"
```

### Code Manim généré

```python
class Chapter2(Scene):
    def construct(self):
        title = Text("Chapitre 2", font_size=72, color=WHITE)
        subtitle = Text("Les Applications", font_size=48, color=GREY_B)
        subtitle.next_to(title, DOWN, buff=0.5)

        self.play(Write(title), run_time=1.5)
        self.play(FadeIn(subtitle), run_time=1)
        self.wait(1.5)
```

---

## animation

Animation Manim complète, générée via la skill `/manim`.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `concept` | string | Oui | - | Description en langage naturel |
| `three_d` | boolean | Non | false | Forcer scène 3D |
| `elements` | array | Non | [] | Éléments visuels suggérés |
| `phases` | array | Non | [] | Phases de l'animation |
| `style_overrides` | object | Non | {} | Surcharges de style |

### Structure des phases

```yaml
phases:
  - name: "setup"
    description: "Ce qui se passe dans cette phase"
    duration: "5s"  # Estimation
  - name: "main"
    description: "Animation principale"
    duration: "10s"
```

### Exemple

```yaml
- id: "derivative_visualization"
  name: "Visualisation de la dérivée"
  type: "animation"
  content:
    concept: |
      Montrer graphiquement le passage de la sécante à la tangente.
      - Afficher f(x) = x²
      - Tracer une sécante entre x=1 et x=3
      - Animer le point x=3 qui se rapproche de x=1
      - Montrer la tangente finale à x=1
      - Afficher la pente = 2

    elements:
      - type: "axes"
        x_range: [-1, 4]
        y_range: [-1, 10]
      - type: "function"
        expression: "x**2"
      - type: "secant_line"
        points: [[1, 1], [3, 9]]
        animated: true
      - type: "label"
        text: "Pente = 2"
        position: "top_right"

    phases:
      - name: "setup"
        description: "Axes et courbe"
        duration: "3s"
      - name: "secant"
        description: "Afficher la sécante"
        duration: "2s"
      - name: "limit"
        description: "Animation limite"
        duration: "8s"
      - name: "result"
        description: "Tangente finale"
        duration: "3s"

  transition_out: "fade"
```

---

## diagram

Schémas structurés : arbres, flowcharts, graphes.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `layout` | string | Oui | - | "tree", "flowchart", "radial", "grid" |
| `nodes` | array | Oui | - | Liste des nœuds |
| `edges` | array | Non | [] | Connexions entre nœuds |
| `animation` | string | Non | "build" | "none", "build", "fade_in" |
| `direction` | string | Non | "down" | "down", "up", "left", "right" |

### Structure d'un nœud

```yaml
nodes:
  - id: "node_1"          # Identifiant unique
    label: "Texte"        # Texte affiché
    parent: "parent_id"   # Parent pour layout tree (optionnel)
    color: "primary"      # Couleur (réf ou valeur)
    shape: "rectangle"    # "rectangle", "circle", "diamond"
    size: 1.0             # Échelle relative
```

### Structure d'une arête

```yaml
edges:
  - from: "node_1"
    to: "node_2"
    label: "relation"     # Texte sur l'arête (optionnel)
    style: "arrow"        # "arrow", "line", "dashed"
    color: "secondary"
```

### Exemple - Arbre

```yaml
- id: "math_tree"
  name: "Branches des mathématiques"
  type: "diagram"
  duration: "10s"
  content:
    layout: "tree"
    direction: "down"

    nodes:
      - id: "math"
        label: "Mathématiques"
        color: "primary"
      - id: "algebra"
        label: "Algèbre"
        parent: "math"
      - id: "analysis"
        label: "Analyse"
        parent: "math"
      - id: "geometry"
        label: "Géométrie"
        parent: "math"

    animation: "build"
```

### Exemple - Flowchart

```yaml
- id: "algorithm"
  name: "Algorithme de tri"
  type: "diagram"
  content:
    layout: "flowchart"
    direction: "down"

    nodes:
      - id: "start"
        label: "Début"
        shape: "circle"
      - id: "compare"
        label: "A > B ?"
        shape: "diamond"
      - id: "swap"
        label: "Échanger"
        shape: "rectangle"
      - id: "next"
        label: "Élément suivant"
        shape: "rectangle"
      - id: "end"
        label: "Fin"
        shape: "circle"

    edges:
      - from: "start"
        to: "compare"
      - from: "compare"
        to: "swap"
        label: "Oui"
      - from: "compare"
        to: "next"
        label: "Non"
      - from: "swap"
        to: "next"
      - from: "next"
        to: "end"

    animation: "build"
```

---

## summary

Liste de points clés avec animation séquentielle.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `title` | string | Non | null | Titre de la section |
| `points` | array | Oui | - | Liste des points |
| `animation` | string | Non | "sequential" | "sequential", "simultaneous", "none" |
| `delay_between` | float | Non | 1.0 | Délai entre points (sec) |
| `icon_style` | string | Non | "bullet" | Style d'icône par défaut |

### Structure d'un point

```yaml
points:
  - text: "Texte du point"
    color: "primary"         # Couleur (optionnel)
    icon: "bullet"           # "bullet", "number", "check", "arrow", "none"
    highlight: false         # Mettre en évidence
```

### Exemple

```yaml
- id: "takeaways"
  name: "Points clés"
  type: "summary"
  duration: "12s"
  content:
    title: "À retenir"

    points:
      - text: "Premier point important"
        icon: "number"
        color: "primary"
      - text: "Deuxième point clé"
        icon: "number"
        color: "secondary"
      - text: "Conclusion finale"
        icon: "check"
        color: "accent"
        highlight: true

    animation: "sequential"
    delay_between: 1.5

  transition_out: "fade_to_black"
```

---

## equation

Mise en valeur d'équations et formules.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `equation` | string | Oui* | - | Équation en texte simple |
| `parts` | array | Oui* | - | Équation décomposée |
| `highlight_sequence` | array | Non | [] | Séquence de mise en évidence |
| `transform_to` | string | Non | null | Transformation vers autre forme |

*Un des deux requis

### Format texte simple

Pour équations simples, utiliser des caractères Unicode :
```yaml
equation: "f'(x) = 2x"
```

### Format décomposé (recommandé)

Plus de contrôle sur les couleurs et animations :
```yaml
parts:
  - text: "f(x)"
    color: "primary"
  - text: " = "
    color: "text"
  - text: "x²"
    color: "secondary"
```

### Exemple avec transformation

```yaml
- id: "factoring"
  name: "Factorisation"
  type: "equation"
  duration: "8s"
  content:
    parts:
      - text: "x² + 2x + 1"
        color: "primary"

    transform_to:
      parts:
        - text: "(x + 1)²"
          color: "secondary"

    highlight_sequence:
      - indices: [0]
        label: "Expression initiale"
        duration: 2
      - indices: [0]
        label: "Reconnaître l'identité remarquable"
        duration: 2
```

---

## comparison

Comparaisons visuelles côte à côte.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `layout` | string | Non | "side_by_side" | "side_by_side", "top_bottom" |
| `left` / `top` | object | Oui | - | Premier élément |
| `right` / `bottom` | object | Oui | - | Second élément |
| `highlight_differences` | boolean | Non | false | Mettre en évidence les différences |
| `sync_animations` | boolean | Non | true | Synchroniser les animations |

### Structure d'un côté

```yaml
left:
  title: "Titre"
  type: "animation"      # "animation", "diagram", "text", "image"
  content: "..."         # Selon le type
```

### Exemple

```yaml
- id: "discrete_vs_continuous"
  name: "Discret vs Continu"
  type: "comparison"
  duration: "15s"
  content:
    layout: "side_by_side"

    left:
      title: "Somme discrète"
      type: "animation"
      content: |
        Barres d'histogramme sous la courbe,
        montrant une approximation de Riemann

    right:
      title: "Intégrale"
      type: "animation"
      content: |
        Aire continue sous la même courbe,
        remplie progressivement

    highlight_differences: true
    sync_animations: true

  transition_out: "fade"
```

---

## code_demo

Démonstration de code avec coloration syntaxique.

### Paramètres

| Paramètre | Type | Requis | Défaut | Description |
|-----------|------|--------|--------|-------------|
| `language` | string | Non | "python" | Langage de programmation |
| `code` | string | Oui | - | Code à afficher |
| `highlight_lines` | array | Non | [] | Lignes à mettre en évidence |
| `animation` | string | Non | "write" | "write", "fade_in", "typewriter" |
| `show_output` | boolean | Non | false | Afficher la sortie |
| `output` | string | Non | null | Sortie à afficher |

### Exemple

```yaml
- id: "python_derivative"
  name: "Calcul en Python"
  type: "code_demo"
  duration: "10s"
  content:
    language: "python"
    code: |
      def derivative(f, x, h=0.0001):
          return (f(x + h) - f(x)) / h

      f = lambda x: x**2
      print(derivative(f, 3))

    highlight_lines: [2, 5]

    show_output: true
    output: "6.000099999999479"

    animation: "write"
```
