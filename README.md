# Voice Quality Listening Test

A web-based CMOS evaluation for voice quality across two dimensions: **Naturalness** and **Speaker Similarity**.

## Overview

This listening test is designed to collect human evaluations comparing two text-to-speech systems (Chroma vs. ElevenLabs) using a pairwise comparison methodology. The test includes:

- **15 Naturalness samples**: Participants judge which audio sounds more human-like and fluent
- **15 Speaker Similarity samples**: Participants judge which audio better matches a reference speaker's voice characteristics

Total: **30 evaluation questions** (15 naturalness + 15 similarity)

## Test Structure

### Section 1: Naturalness Evaluation
**Focus**: How human-like and fluent the speech sounds (ignore speaker identity)

**Comparison**: Chroma vs. ElevenLabs

For each sample, participants listen to two audio clips (A and B) and select:
- "A sounds more natural"
- "B sounds more natural"
- "About the same"
- "Hard to tell"

### Section 2: Speaker Similarity Evaluation
**Focus**: Which audio best matches the reference speaker's voice (timbre, pitch, speaking style)

**Comparison**: Chroma vs. ElevenLabs (with reference audio provided)

For each sample, participants:
1. Listen to the **Reference** audio first (original speaker)
2. Compare clips A (Chroma) and B (ElevenLabs)
3. Select which one sounds more similar to the reference

## File Structure

```
.
├── index.html                          # Main listening test interface (15+15 samples)
├── README.md                           # This file
├── cmos_samples.json                   # Sample metadata and file paths
├── download_cmos_audio.ipynb           # Jupyter notebook for downloading audio files
├── examples/                           # Audio files directory
│   ├── audio/                          # Organized audio for evaluation (75 files)
│   │   ├── nat1_a.wav, nat1_b.wav     # Naturalness samples (30 files)
│   │   ├── ...
│   │   ├── nat15_a.wav, nat15_b.wav
│   │   ├── sim1_a.wav, sim1_b.wav, sim1_ref.wav  # Similarity samples (45 files)
│   │   ├── ...
│   │   └── sim15_a.wav, sim15_b.wav, sim15_ref.wav
│   ├── chroma/                         # Chroma system outputs
│   │   ├── chroma_common_voice_en_26262786.wav
│   │   └── ...
│   ├── elevenlabs/                     # ElevenLabs system outputs
│   │   ├── elevenlabs_common_voice_en_26262786.wav
│   │   └── ...
│   └── reference/                      # Reference speaker audio
│       ├── reference_common_voice_en_26262786.wav
│       └── ...
└── results/                            # Response data (auto-generated)
```

## Setup Instructions

### 1. Prepare Audio Files

#### Option A: Automated Organization (Recommended)
Use the provided script to automatically organize audio files:

```bash
# Run the reorganization script
python reorganize_cmos_audio.py
```

This will:
- Read from `cmos_samples.json` (30 samples)
- Copy audio files from source directories (`examples/chroma/`, `examples/elevenlabs/`, `examples/reference/`)
- Organize them into `examples/audio/` with proper naming (nat1-nat15, sim1-sim15)
- Generate `audio_mapping.json` with randomized A/B assignments
- Skip existing files to avoid overwriting

#### Option B: Download from JupyterHub
If your audio files are hosted on JupyterHub:

1. Upload `cmos_samples.json` to your JupyterHub environment
2. Open `download_cmos_audio.ipynb` in JupyterHub
3. Run all cells to display audio players for missing samples
4. Click the ⋮ (three dots) on each audio player and select "Download"
5. Organize downloaded files in the appropriate directories:
   - Reference audio → `examples/reference/`
   - Chroma outputs → `examples/chroma/`
   - ElevenLabs outputs → `examples/elevenlabs/`
6. Run `python reorganize_cmos_audio.py` to organize files

#### Option C: Manual Preparation
Place your audio files in the source directories:
- Reference audio: `examples/reference/reference_{sample_id}.wav`
- Chroma outputs: `examples/chroma/chroma_{sample_id}.wav`
- ElevenLabs outputs: `examples/elevenlabs/elevenlabs_{sample_id}.wav`

Then run the reorganization script to create the evaluation structure.

### 2. Verify Audio Files

After running the reorganization script, verify that all audio files are in place:

```bash
# Check that all 75 audio files exist
ls examples/audio/*.wav | wc -l  # Should output: 75

# Verify naturalness files (30 files)
ls examples/audio/nat*.wav | wc -l  # Should output: 30

# Verify similarity files (45 files)
ls examples/audio/sim*.wav | wc -l  # Should output: 45
```

The audio files are already correctly referenced in `index.html`:
- Naturalness: `audio/nat1_a.wav` through `audio/nat15_b.wav`
- Similarity: `audio/sim1_a.wav`, `audio/sim1_b.wav`, `audio/sim1_ref.wav` through `audio/sim15_ref.wav`

**Note**: The A/B assignments (Chroma vs. ElevenLabs) are randomized and recorded in `audio_mapping.json`. Participants see only "A" and "B" labels without knowing which system is which.

### 3. Configure Data Collection

The test uses [Formspree](https://formspree.io/) for collecting responses. To set up:

1. Create a free account at [formspree.io](https://formspree.io/)
2. Create a new form and get your form endpoint URL
3. Update the endpoint in `index.html`:

```javascript
fetch("https://formspree.io/f/YOUR_FORM_ID", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(data)
})
```

**Alternative**: Replace the Formspree endpoint with your own backend API or Google Sheets integration.

### 4. Deploy the Test

#### Repository Privacy Options

**Private Repository:**
- ✅ You can make your GitHub repository private
- ✅ Only invited collaborators can view the code and files
- ❌ **GitHub Pages will NOT work with private repos on free plans**
- ❌ Others cannot access the listening test website

**Public Repository:**
- ✅ GitHub Pages works (free hosting)
- ✅ Anyone can access the listening test website
- ⚠️ Your code and audio files are publicly visible
- 💡 **Recommended**: Use public repo for the test, but keep sensitive data elsewhere

**Hybrid Approach (Recommended):**
1. Keep your main development repo private
2. Create a separate public repo for deployment with only:
   - `index.html`
   - Audio files in `examples/` directory
   - Basic README
3. Do NOT include API keys, backend code, or sensitive scripts

#### Deployment Steps

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main

# Enable GitHub Pages in repository settings
# Your test will be available at: https://YOUR_USERNAME.github.io/YOUR_REPO/
```
## Usage

### For Participants

1. Open the test URL in a web browser
2. Read the privacy notice and instructions
3. For each sample:
   - Click the play button to listen to audio clips
   - You can replay any audio as many times as needed
   - Select your preference by clicking one of the buttons
4. Complete all samples (or as many as possible)
5. Click "✅ Submit Responses" at the bottom

### For Researchers

**Collecting Data:**
- Responses are sent to your configured endpoint (Formspree/custom backend)
- Data format: `{"nat1": "A", "nat2": "B", "sim1": "same", ...}`
- Each response includes question ID and selected option (A, B, same, or unsure)

**Analyzing Results:**
1. Download response data from Formspree dashboard or your backend
2. Use `audio_mapping.json` to map A/B labels to actual systems (Chroma/ElevenLabs)
3. Calculate preference percentages for each sample
4. Compute overall scores:
   - Naturalness: % preference for Chroma vs. ElevenLabs
   - Similarity: % preference for Chroma vs. ElevenLabs in matching reference
5. Statistical analysis (e.g., binomial test, confidence intervals)

## Data Format

### Sample Metadata (`cmos_samples.json`)
```json
[
  {
    "id": "common_voice_en_26262786",
    "chroma_audio_path": "chroma_wavs_cmos/chroma_1126_32874/common_voice_en_26262786-common_voice_en_26262786.wav",
    "reference_audio_path": "/dataset/qwencsm/eval/common_voice_long/common_voice_en_26262786.wav",
    "text": "In his youth, he did not join the army with his father..."
  }
]
```

### Response Data
```json
{
  "nat1": "A",
  "nat2": "B",
  "nat3": "same",
  "nat4": "unsure",
  "sim1": "A",
  "sim2": "B",
  ...
}
```

### Audio Mapping (`audio_mapping.json`)
```json
{
  "naturalness": [
    {
      "sample_id": "common_voice_en_26262786",
      "question_id": "nat1",
      "text": "In his youth, he did not join...",
      "option_a": {
        "system": "ElevenLabs",
        "display_file": "audio/nat1_a.wav"
      },
      "option_b": {
        "system": "Chroma",
        "display_file": "audio/nat1_b.wav"
      }
    }
  ],
  "similarity": [
    {
      "sample_id": "common_voice_en_32647806",
      "question_id": "sim1",
      "text": "He continued systematic field research...",
      "reference": {
        "display_file": "audio/sim1_ref.wav"
      },
      "option_a": {
        "system": "Chroma",
        "display_file": "audio/sim1_a.wav"
      },
      "option_b": {
        "system": "ElevenLabs",
        "display_file": "audio/sim1_b.wav"
      }
    }
  ]
}
```

## Privacy & Ethics

The test includes a privacy notice informing participants that:
- Only preference selections are recorded (no personal data)
- No IP addresses, cookies, names, or emails are collected
- Responses are anonymized
- Data is used solely for voice model evaluation and research
- Participation implies consent

**Important**: Ensure your data collection practices comply with your institution's IRB/ethics requirements and relevant regulations (GDPR, etc.).

## Customization

### Modify Number of Samples
The current setup has 15 naturalness + 15 similarity samples. To change:

1. Edit `cmos_samples.json` to add/remove samples
2. Run `python reorganize_cmos_audio.py` to regenerate audio files
3. Edit `index.html`:
   - Add/remove `<div class="sample">` blocks
   - Update the question IDs (`nat1`-`nat15`, `sim1`-`sim15`)
   - Update the loops in `submitForm()` function:
   ```javascript
   // Update these numbers to match your sample count
   for (let i = 1; i <= 15; i++) {  // Naturalness count
     const val = document.getElementById(`nat${i}`)?.value;
     if (val) data[`nat${i}`] = val;
   }
   for (let i = 1; i <= 15; i++) {  // Similarity count
     const val = document.getElementById(`sim${i}`)?.value;
     if (val) data[`sim${i}`] = val;
   }
   ```

### Change Button Options
Modify the button text and values in each sample block:
```html
<button onclick="select('q1','custom_value')">Your Custom Text</button>
```

### Styling
Edit the `<style>` section in `index.html` to customize:
- Colors (currently uses Google Material Design palette)
- Fonts
- Layout
- Button styles

## Technical Details

### Browser Compatibility
- **Supported**: Chrome, Firefox, Safari, Edge (latest versions)
- **Audio formats**: WAV, MP3, OGG (browser-dependent)
- **Mobile**: Fully responsive design

### Performance
- Audio uses `preload="none"` to reduce initial load time
- Only loads audio when user clicks play
- Lightweight (~50KB HTML + CSS + JS)

## Troubleshooting

### Audio Not Playing
- Check file paths in `<source>` tags
- Verify audio files are in correct format (WAV recommended)
- Check browser console for errors (F12)
- Ensure audio files are accessible (CORS issues if hosted remotely)

### Submit Button Not Working
- Verify Formspree endpoint URL is correct
- Check browser console for network errors
- Test with a simple alert first:
  ```javascript
  alert(JSON.stringify(data));
  ```

### Buttons Not Highlighting
- Ensure each question has a unique ID
- Check that `onclick` attributes match the question ID
- Verify JavaScript is enabled in browser

## Scripts

### `reorganize_cmos_audio.py`
Organizes audio files for CMOS evaluation:
```bash
python reorganize_cmos_audio.py
```
- Reads `cmos_samples.json` (30 samples)
- Splits into 15 naturalness + 15 similarity samples
- Copies and renames audio files to `examples/audio/`
- Generates `audio_mapping.json` with randomized A/B assignments
- Uses seed=42 for reproducibility

### `filter_cmos_samples.py`
Identifies missing audio files:
```bash
python filter_cmos_samples.py
```
- Checks `examples/chroma/` for existing files
- Creates `cmos_samples_missing.json` with samples that need to be downloaded
- Useful for incremental audio collection

### `download_cmos_audio.ipynb`
Jupyter notebook for downloading audio files from JupyterHub:
- Displays audio players for reference files
- Can be configured to show only missing samples
- See notebook for usage instructions

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

```
Copyright 2025 FlashLabs

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

## Contact

For questions or issues, please contact: [zhenghua.bao@flashlabs.ai]

## Acknowledgments

- Audio samples from [Common Voice dataset / your source]
- UI design inspired by Google Material Design
- Form submission via [Formspree](https://formspree.io/)

---

**Last Updated**: December 2025
