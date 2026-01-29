# Référence FFmpeg pour Video Course

Commandes ffmpeg utilisées pour l'assemblage des vidéos.

## Vérifier l'installation

```bash
ffmpeg -version
```

## Concaténation Simple

### Méthode concat demuxer (recommandée)

Créer un fichier `concat.txt` :
```
file 'scene_01.mp4'
file 'scene_02.mp4'
file 'scene_03.mp4'
```

Commande :
```bash
ffmpeg -f concat -safe 0 -i concat.txt -c copy output.mp4
```

### Méthode filter (si codecs différents)

```bash
ffmpeg -i scene_01.mp4 -i scene_02.mp4 -i scene_03.mp4 \
  -filter_complex "[0:v][0:a][1:v][1:a][2:v][2:a]concat=n=3:v=1:a=1[outv][outa]" \
  -map "[outv]" -map "[outa]" output.mp4
```

## Transitions

### Crossfade entre deux vidéos

```bash
# Durée vidéo 1 = 10s, transition de 0.5s commence à 9.5s
ffmpeg -i scene_01.mp4 -i scene_02.mp4 -filter_complex \
  "[0:v][1:v]xfade=transition=fade:duration=0.5:offset=9.5[outv]" \
  -map "[outv]" output.mp4
```

### Types de transitions xfade

| Transition | Description |
|------------|-------------|
| `fade` | Fondu enchaîné classique |
| `wipeleft` | Balayage vers la gauche |
| `wiperight` | Balayage vers la droite |
| `wipeup` | Balayage vers le haut |
| `wipedown` | Balayage vers le bas |
| `dissolve` | Dissolution pixelisée |
| `pixelize` | Effet pixelisation |
| `radial` | Transition radiale |
| `circleopen` | Cercle qui s'ouvre |
| `circleclose` | Cercle qui se ferme |

### Chaîne de transitions (3+ vidéos)

```bash
# Pour 3 vidéos avec fade de 0.5s
# Durées: v1=10s, v2=8s, v3=6s
ffmpeg -i v1.mp4 -i v2.mp4 -i v3.mp4 -filter_complex \
  "[0:v][1:v]xfade=transition=fade:duration=0.5:offset=9.5[v01]; \
   [v01][2:v]xfade=transition=fade:duration=0.5:offset=16.5[outv]" \
  -map "[outv]" output.mp4
```

### Script Python pour générer la commande

```python
def generate_xfade_command(videos, transition="fade", duration=0.5):
    """
    Génère la commande ffmpeg pour enchaîner des vidéos avec transitions.

    Args:
        videos: Liste de tuples (filename, duration_seconds)
        transition: Type de transition
        duration: Durée de la transition

    Returns:
        Commande ffmpeg complète
    """
    if len(videos) < 2:
        return f"ffmpeg -i {videos[0][0]} -c copy output.mp4"

    inputs = " ".join(f"-i {v[0]}" for v, _ in videos)

    filters = []
    cumulative_offset = 0

    for i in range(len(videos) - 1):
        v1_duration = videos[i][1]
        offset = cumulative_offset + v1_duration - duration

        if i == 0:
            in1, in2 = "[0:v]", "[1:v]"
        else:
            in1, in2 = f"[v{i-1}{i}]", f"[{i+1}:v]"

        if i == len(videos) - 2:
            out = "[outv]"
        else:
            out = f"[v{i}{i+1}]"

        filters.append(
            f"{in1}{in2}xfade=transition={transition}:duration={duration}:offset={offset}{out}"
        )

        cumulative_offset = offset

    filter_complex = "; ".join(filters)

    return f'ffmpeg {inputs} -filter_complex "{filter_complex}" -map "[outv]" output.mp4'
```

## Audio

### Ajouter musique de fond

```bash
ffmpeg -i video.mp4 -i music.mp3 \
  -c:v copy \
  -c:a aac \
  -map 0:v:0 \
  -map 1:a:0 \
  -shortest \
  output.mp4
```

### Avec contrôle du volume

```bash
ffmpeg -i video.mp4 -i music.mp3 \
  -c:v copy \
  -filter_complex "[1:a]volume=0.3[bg]" \
  -map 0:v:0 \
  -map "[bg]" \
  -shortest \
  output.mp4
```

### Fade in/out sur l'audio

```bash
# Fade in 2s au début, fade out 3s à la fin (vidéo de 60s)
ffmpeg -i video.mp4 -i music.mp3 \
  -c:v copy \
  -filter_complex "[1:a]volume=0.3,afade=t=in:st=0:d=2,afade=t=out:st=57:d=3[bg]" \
  -map 0:v:0 \
  -map "[bg]" \
  -shortest \
  output.mp4
```

### Boucler l'audio si trop court

```bash
ffmpeg -i video.mp4 -stream_loop -1 -i music.mp3 \
  -c:v copy \
  -c:a aac \
  -map 0:v:0 \
  -map 1:a:0 \
  -shortest \
  output.mp4
```

## Obtenir la Durée d'une Vidéo

```bash
ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 video.mp4
```

### En Python

```python
import subprocess
import json

def get_video_duration(filepath):
    """Retourne la durée en secondes."""
    cmd = [
        'ffprobe', '-v', 'error',
        '-show_entries', 'format=duration',
        '-of', 'json',
        filepath
    ]
    result = subprocess.run(cmd, capture_output=True, text=True)
    data = json.loads(result.stdout)
    return float(data['format']['duration'])
```

## Création de Vidéo depuis Image (Title Cards)

### Image statique pendant X secondes

```bash
ffmpeg -loop 1 -i title.png -c:v libx264 -t 5 -pix_fmt yuv420p title.mp4
```

### Avec fade in/out

```bash
ffmpeg -loop 1 -i title.png -c:v libx264 -t 5 -pix_fmt yuv420p \
  -vf "fade=t=in:st=0:d=0.5,fade=t=out:st=4.5:d=0.5" \
  title.mp4
```

## Redimensionnement et Uniformisation

### Forcer une résolution

```bash
ffmpeg -i input.mp4 -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2" output.mp4
```

### Uniformiser le framerate

```bash
ffmpeg -i input.mp4 -r 60 output.mp4
```

## Commande Complète Typique

Assemblage d'un projet complet :

```bash
#!/bin/bash

# 1. Obtenir les durées
dur1=$(ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 scenes/01_intro.mp4)
dur2=$(ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 scenes/02_demo.mp4)

# 2. Calculer les offsets (avec transition de 0.5s)
offset1=$(echo "$dur1 - 0.5" | bc)
offset2=$(echo "$offset1 + $dur2 - 0.5" | bc)

# 3. Assembler avec transitions
ffmpeg \
  -i scenes/01_intro.mp4 \
  -i scenes/02_demo.mp4 \
  -i scenes/03_conclusion.mp4 \
  -filter_complex \
    "[0:v][1:v]xfade=transition=fade:duration=0.5:offset=$offset1[v01]; \
     [v01][2:v]xfade=transition=fade:duration=0.5:offset=$offset2[outv]" \
  -map "[outv]" \
  temp_video.mp4

# 4. Ajouter l'audio
ffmpeg \
  -i temp_video.mp4 \
  -i assets/audio/background.mp3 \
  -c:v copy \
  -filter_complex "[1:a]volume=0.25,afade=t=in:d=2,afade=t=out:st=$(echo "$offset2 + $dur3 - 3" | bc):d=3[audio]" \
  -map 0:v \
  -map "[audio]" \
  -shortest \
  output/video_finale.mp4

# 5. Nettoyer
rm temp_video.mp4
```

## Dépannage

### Erreur "Non-monotonous DTS"

```bash
ffmpeg -i input.mp4 -c copy -video_track_timescale 90000 output.mp4
```

### Vidéos avec codecs différents

Ré-encoder avant concat :
```bash
ffmpeg -i scene.mp4 -c:v libx264 -preset fast -crf 22 -c:a aac scene_normalized.mp4
```

### Vérifier la compatibilité

```bash
ffprobe -v error -select_streams v:0 -show_entries stream=codec_name,width,height,r_frame_rate -of csv=p=0 video.mp4
```
