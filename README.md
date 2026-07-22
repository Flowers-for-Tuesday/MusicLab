# MusicLab 🎵

A powerful music visualization extension for [Manim](https://www.manim.community/), enabling you to create stunning animated music theory visualizations, sheet music, and interactive piano keyboards directly in your Manim scenes.

## 📋 Overview

ManimMusic solves the challenge of creating professional music visualizations in Manim by providing ready-to-use components for:

- **Circle of Fifths** visualization with interactive key rotation
- **Sheet music rendering** using LilyPond integration
- **Piano keyboard** visualization with multi-octave support
- **Audio generation** from music21 scores
- **Music notation elements** via SVG support

Perfect for music education videos, theory explanations, and interactive music demonstrations!

## 🛠️ Tech Stack

### Core Dependencies
- **manim** (0.19.1) - Mathematical animation engine
- **music21** (9.9.1) - Music analysis and notation toolkit
- **numpy** (2.4.1) - Numerical computing
- **matplotlib** (3.10.8) - Plotting library
- **svgelements** (1.9.6) - SVG parsing
- **lxml** (5.3.0) - XML processing
- **auxiliar** (0.0.5) - Utility functions

### Built-in Tools
- **LilyPond 2.24.4** - Professional music notation engraving
- **FluidSynth 2.4.6** - MIDI to audio synthesis

## 🚀 Quick Start

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Flowers-for-Tuesday/ManimMusic.git
   cd ManimMusic
   ```

2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Verify installation:**
   ```bash
   # Test Circle of Fifths demo
   manim -pql CircleOfFifthsDemo.py CircleOfFifthsDemo

   # Test Music Score demo
   manim -pql MusicTexDemo.py MusicTexDemo

   # Test Piano Keyboard demo
   manim -pql PianoKeyboardDemo.py PianoKeyboardDemo
   ```

> **Note:** The first run may take longer as LilyPond processes the sheet music. Subsequent runs use cached SVG files for faster rendering.

## 📁 Project Structure

```
ManimMusic/
├── Mobjects/              # Core visualization components
│   ├── CircleOfFifths.py  # Circle of fifths visualization
│   ├── MusicTex.py        # Sheet music rendering (LilyPond)
│   ├── PianoKeyboard.py  # Piano keyboard components
│   ├── MusicAudio.py      # Audio generation (FluidSynth)
│   └── MusicSvg.py        # SVG music notation elements
├── utils/                 # Helper utilities
│   └── pianoscore.py     # Score creation helpers
├── demos/                 # Example videos
│   ├── CircleOfFifthsDemo.mp4
│   ├── MusicTexDemo.mp4
│   └── PianoKeyboardDemo.mp4
├── lilypond-2.24.4/       # LilyPond binary (Windows)
├── fluidsynth-2.4.6/     # FluidSynth binary (Windows)
└── MusicSvg/              # SVG music symbols library
```

## 🎯 Core Components

### 1. CircleOfFifths

Visualize the circle of fifths with interactive key rotation and optional score display.

**Features:**
- Support for major/minor keys
- Rotate to any key signature
- Optional score display for each key
- Chord visualization with `show_chord()`
- Arrow marking between keys

**Example:**
```python
from manim import *
from Mobjects import CircleOfFifths

class MyScene(Scene):
    def construct(self):
        # Basic circle of fifths
        circle = CircleOfFifths("Major1").set_color(BLACK)
        self.play(Write(circle))
        
        # Rotate to G major
        self.play(circle.animate.rotate_to_key("G"))
        
        # With score display
        circle_with_scores = CircleOfFifths("Major1", show_scores=True)
        self.play(Transform(circle, circle_with_scores))
```

**Available Types:**
- `"Major1"`, `"Major2"` - Major key variants
- `"Minor1"`, `"Minor2"` - Minor key variants

### 2. MusicTex

Render professional sheet music from music21 scores using LilyPond.

**Features:**
- Automatic MusicXML → LilyPond → SVG conversion
- Caching for improved performance
- Customizable display options (clef, time signature, key signature, etc.)
- Support for multiple parts (Treble, Bass, Alto)

**Example:**
```python
from manim import *
from Mobjects import MusicTex
from utils import piano_score, add_notes

class MyScene(Scene):
    def construct(self):
        # Create a score
        score = piano_score("C", "4/4", parts=["Treble", "Bass"])
        add_notes(score, "Treble", [("C4", 1.0), ("E4", 1.0), ("G4", 1.0), ("C5", 1.0)])
        add_notes(score, "Bass", [("C3", 2.0), ("G3", 2.0)])
        
        # Render as SVG
        music = MusicTex(score)
        self.play(Write(music))
```

**Customization Options:**
- `barline_on` - Show/hide bar lines
- `clef_on` - Show/hide clef
- `timesignature_on` - Show/hide time signature
- `keysignature_on` - Show/hide key signature
- `staffsymbol_on` - Show/hide staff lines
- `metronomemark_on` - Show/hide tempo mark
- `line_width` - Staff line thickness

### 3. PianoKeyboard & MultiOctavePianoKeyboard

Create interactive piano keyboard visualizations.

**Features:**
- Single octave (`PianoKeyboard`) or multi-octave (`MultiOctavePianoKeyboard`)
- Key marking/highlighting
- Note name labels
- Customizable colors and sizes

**Example:**
```python
from manim import *
from Mobjects import MultiOctavePianoKeyboard

class MyScene(Scene):
    def construct(self):
        # Create 3-octave keyboard with labels
        piano = MultiOctavePianoKeyboard(
            octaves=3, 
            start_octave=3,
            show_labels=True
        ).scale(0.8)
        
        self.play(Write(piano))
        
        # Highlight specific notes
        self.play(piano.animate.markNotes(["C4", "E4", "G4"]))
        self.wait(1)
```

**Methods:**
- `markKey(key)` / `markKeys(keys)` - Highlight keys by index
- `markNotes(notes)` - Highlight by note name (e.g., "C4")
- `unmarkKey(key)` / `resetMarks()` - Clear highlights
- `alignToKey(key, mob)` - Align other objects to keys

### 4. MusicAudio & NoteAudio

Generate audio files from music21 scores or individual notes.

**Features:**
- Convert music21 scores to WAV files
- Support for single notes and chords
- Automatic caching
- Uses FluidSynth with sound fonts

**Example:**
```python
from Mobjects import NoteAudio, MusicAudio
from utils import piano_score, add_notes

# Generate audio for a single note
note_audio = NoteAudio("C4", duration=1.0)
print(f"Audio saved to: {note_audio.wav_path}")

# Generate audio for a chord
chord_audio = NoteAudio(["C4", "E4", "G4"], duration=2.0)

# Generate audio for a full score
score = piano_score("C", "4/4", parts=["Treble"])
add_notes(score, "Treble", [("C4", 1.0), ("E4", 1.0), ("G4", 1.0)])
score_audio = MusicAudio(score, output_name="my_song")
```

### 5. MusicSvg

Load pre-made SVG music notation symbols.

**Example:**
```python
from Mobjects import MusicSvg

# Load a music symbol SVG
clef = MusicSvg("clefs_percussion")
rest = MusicSvg("rests_1")
```

## 🎬 Demo Videos

### Circle of Fifths Demo
![Circle of Fifths Demo](demos/CircleOfFifthsDemo.gif)

Demonstrates the circle of fifths visualization with:
- Basic circle display
- Score display mode
- Interactive key rotation (C → G → D → A)

### Music Score Demo
![Music Score Demo](demos/MusicTexDemo.gif)

Shows sheet music rendering capabilities:
- Creating piano scores with multiple parts
- Rendering different key signatures (C major → D major)
- Smooth transitions between scores

### Piano Keyboard Demo
![Piano Keyboard Demo](demos/PianoKeyboardDemo.gif)

Features the piano keyboard component:
- Multi-octave keyboard visualization
- Octave highlighting
- Individual key indication
- Note label display

## 💡 Usage Tips

1. **Performance:** MusicTex caches SVG files based on score content. Reusing the same score will be much faster on subsequent runs.

2. **Score Creation:** Use the `piano_score()` and `add_notes()` utilities from `utils.pianoscore` for easier score creation:
   ```python
   from utils import piano_score, add_notes
   
   score = piano_score("G", "3/4", parts=["Treble"])
   add_notes(score, "Treble", [
       ("G4", 1.0),
       [("B4", 0.5), ("D5", 0.5)],  # Beamed notes
       ("G5", 1.0)
   ])
   ```

3. **Customization:** Most components accept standard Manim styling parameters (color, scale, position, etc.)

4. **Audio Integration:** Combine `MusicAudio` with Manim's `Audio` class to add sound to your animations:
   ```python
   from manim import Audio
   from Mobjects import MusicAudio
   
   audio = MusicAudio(score)
   self.add_sound(audio.wav_path)
   ```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is open source. Please check individual component licenses (LilyPond, FluidSynth, etc.) for their respective terms.

## 🙏 Acknowledgments

- [Manim Community](https://www.manim.community/) - The animation framework
- [music21](https://web.mit.edu/music21/) - Music analysis toolkit
- [LilyPond](https://lilypond.org/) - Music engraving
- [FluidSynth](https://www.fluidsynth.org/) - MIDI synthesis


## 📺 Watch More

For more demonstrations and music theory lessons created with ManimMusic, visit my Bilibili channel: [Flowers for Tuesday](https://space.bilibili.com/393925947/lists?sid=5697355&spm_id_from=333.788.0.0). I regularly update the channel with new visualizations and insights into music theory!

---

**Made for every music lover**
