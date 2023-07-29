# EchoCraft

A Streamlit web app for zero-shot voice cloning. Upload a short voice sample, give it a name, type any text, and EchoCraft generates speech in that voice using the **Tortoise TTS** model.

## Features

- **Voice cloning from a short clip.** Upload a WAV or MP3 sample (10+ seconds recommended) to create a reusable voice profile.
- **Voice library.** Save multiple voices with names and optional profile pictures, then pick one from a list.
- **Automatic audio preparation.** Samples are resampled to 22.05 kHz and split into roughly 6-second chunks, the clip length Tortoise works best with.
- **Conditioning latents.** Each voice is encoded once into Tortoise conditioning latents and reused for every generation, so there's no repeated processing.
- **Quality presets.** Trade speed for quality with `ultra_fast`, `fast`, `standard`, or `high_quality`.
- **In-browser playback** of the generated audio.
- **Half-precision inference** to reduce GPU memory use.

## Tech Stack

| Layer | Technology |
|---|---|
| Interface | Streamlit |
| TTS model | Tortoise TTS (autoregressive and diffusion decoder) |
| Deep learning | PyTorch, torchaudio |
| Audio processing | librosa, soundfile |
| Utilities | Pillow, sortedcontainers |

## How It Works

1. **Upload.** The user uploads a reference recording and names the voice.
2. **Preprocess.** The audio is loaded at 22.05 kHz and split into 6-second chunks.
3. **Encode.** Tortoise turns the chunks into conditioning latents that capture the speaker's voice.
4. **Store.** The latents are saved in the voice library, along with the name and picture.
5. **Generate.** The user selects a voice, enters text and picks a preset. Tortoise then generates speech conditioned on that voice's latents.

## Quality Presets

| Preset | Quality | Speed |
|---|---|---|
| `ultra_fast` | Low | Fastest |
| `fast` | Decent | Fast, good for bulk generation |
| `standard` | Very good | Slow |
| `high_quality` | Best | Very slow |

## Installation

A **CUDA GPU** is strongly recommended. Tortoise is very slow on CPU.

**1. Clone the repository**

```bash
git clone https://github.com/zaynnmotiwala/echocraft.git
cd echocraft
```

**2. Create an environment (Python 3.10 or 3.11)**

```bash
python -m venv venv
venv\Scripts\activate          # macOS/Linux: source venv/bin/activate
```

**3. Install the dependencies**

```bash
pip install -r requirements.txt
```

> `requirements.txt` pins `torch==2.0.1+cu117`. If your CUDA version is different, install PyTorch first from [pytorch.org](https://pytorch.org/get-started/locally/), then install the rest.

The Tortoise model weights download automatically on the first run.

## Usage

```bash
streamlit run main.py
```

Open **http://localhost:8501**, then:

1. **Add a voice.** Enter a name, upload a reference clip and, optionally, a profile picture. Then click **Add Voice**.
2. **Select the voice** from the voice list.
3. **Enter the text** you want to generate.
4. **Choose a preset.** `fast` is a good place to start.
5. Click **Generate Audio** and play the result.

### Tips for better clones

- Use clean recordings with **one speaker**, **no background music**, and minimal noise.
- 10–30 seconds of reference audio usually gives good results.
- Short sentences generate faster and sound more natural.

## Project Structure

```text
├── main.py                # Streamlit UI (voice library, text input, presets, playback)
├── voice_management.py    # Voice and VoiceManager classes, audio preprocessing, Tortoise inference
├── backend.py             # Streamlit session-state and selector helpers
├── questionmark.png       # Default profile picture
├── voicedata/             # Sample reference recordings
└── requirements.txt
```

## Known Limitations

- **Voices are stored per session.** The voice library is kept in memory and resets when the app restarts.
- **Generation is slow.** `standard` and `high_quality` can take several minutes per sentence, even on a GPU.
- **The default image path is hardcoded.** `voice_management.py` loads `drive/MyDrive/questionmark.png`, a Google Colab path. Change it to `questionmark.png` when running locally.
- **The "Delete Voice" button is not wired up yet.**

## Roadmap

- [ ] Save voice profiles to disk
- [ ] Working voice deletion
- [ ] Download button for generated audio
- [ ] Long-text generation with automatic sentence splitting
- [ ] Docker image

