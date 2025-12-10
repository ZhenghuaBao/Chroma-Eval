# Voice Quality Listening Test

A web-based CMOS evaluation for voice quality across two dimensions: **Naturalness** and **Speaker Similarity**.

## Overview

This listening test is designed to collect human evaluations comparing two speech-to-speech systems (Chroma vs. Baseline) using a pairwise comparison methodology. The test includes:

- **15 Naturalness samples**: Participants judge which audio sounds more human-like and fluent
- **15 Speaker Similarity samples**: Participants judge which audio better matches a reference speaker's voice characteristics

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
├── index.html                          # Main listening test interface
├── README.md                           # This file
├── cmos_samples.json                   # Sample metadata and file paths
├── download_cmos_audio.ipynb           # Jupyter notebook for downloading audio files
├── parse_cmos_data.py                  # Script to parse and structure sample data
├── examples/                           # Audio files directory
│   ├── reference_audio/                # Reference speaker audio (for similarity test)
│   │   ├── common_voice_en_26262786.wav
│   │   ├── common_voice_en_103675.wav
│   │   └── ...
│   ├── chroma_audio/                   # Chroma system outputs
│   │   ├── sample_01.wav
│   │   ├── sample_02.wav
│   │   └── ...
│   └── elevenlabs_audio/               # ElevenLabs system outputs
│       ├── sample_01.wav
│       ├── sample_02.wav
│       └── ...
└── results/                            # Response data (auto-generated)
```

## Setup Instructions

### 1. Prepare Audio Files

#### Option A: Download from JupyterHub
If your audio files are hosted on JupyterHub:

1. Upload `cmos_samples.json` to your JupyterHub environment
2. Open `download_cmos_audio.ipynb` in JupyterHub
3. Run all cells to display audio players
4. Click the ⋮ (three dots) on each audio player and select "Download"
5. Organize downloaded files in the `examples/` directory:
   - Reference audio → `examples/reference_audio/`
   - Chroma outputs → `examples/chroma_audio/`
   - ElevenLabs outputs → `examples/elevenlabs_audio/`

#### Option B: Manual Preparation
Place your audio files in the appropriate directories:
- Reference audio: `examples/reference_audio/`
- Chroma outputs: `examples/chroma_audio/`
- ElevenLabs outputs: `examples/elevenlabs_audio/`

### 2. Update Audio File Paths

Edit `index.html` and update the `<source>` tags with your actual file paths:

```html
<!-- Example for Naturalness: Chroma vs. ElevenLabs -->
<div class="audio-group">
  <strong>A (Chroma):</strong> 
  <audio controls preload="none">
    <source src="examples/chroma_audio/sample_01.wav" type="audio/wav">
  </audio>
</div>
<div class="audio-group">
  <strong>B (ElevenLabs):</strong> 
  <audio controls preload="none">
    <source src="examples/elevenlabs_audio/sample_01.wav" type="audio/wav">
  </audio>
</div>

<!-- Example for Similarity: Reference + Chroma vs. ElevenLabs -->
<div class="audio-group">
  <strong>Reference:</strong> 
  <audio controls preload="none">
    <source src="examples/reference_audio/common_voice_en_26262786.wav" type="audio/wav">
  </audio>
</div>
<div class="audio-group">
  <strong>A (Chroma):</strong> 
  <audio controls preload="none">
    <source src="examples/chroma_audio/sample_01.wav" type="audio/wav">
  </audio>
</div>
<div class="audio-group">
  <strong>B (ElevenLabs):</strong> 
  <audio controls preload="none">
    <source src="examples/elevenlabs_audio/sample_01.wav" type="audio/wav">
  </audio>
</div>
```

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
- Data format: `{"q1": "A", "q2": "B", "q3": "same", ...}`

**Analyzing Results:**
- Download response data from Formspree dashboard or your backend
- Calculate preference percentages for each sample
- Compute overall scores (e.g., % preference for System A vs. B)

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
  "q1": "A",
  "q2": "B",
  "q3": "same",
  "q4": "unsure",
  ...
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
Edit `index.html` and:
1. Add/remove `<div class="sample">` blocks
2. Update the question IDs (`q1`, `q2`, etc.)
3. Update the loop in `submitForm()` function:
   ```javascript
   for (let i = 1; i <= YOUR_SAMPLE_COUNT; i++) {
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

### `parse_cmos_data.py`
Parses raw text data into structured JSON format:
```bash
python parse_cmos_data.py
```

### `download_cmos_audio.ipynb`
Jupyter notebook for downloading audio files from JupyterHub. See notebook for usage instructions.

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
