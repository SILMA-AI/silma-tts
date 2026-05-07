
# SILMA TTS: A Lightweight Open Bilingual (Arabic - English) Text to Speech Model 


[![HF](https://img.shields.io/badge/-%F0%9F%A4%97%20Hugging%20Face-black)](https://huggingface.co/silma-ai/silma-tts)
[![hfspace](https://img.shields.io/badge/🤗-HF%20Space-yellow)](https://huggingface.co/spaces/silma-ai/silma-tts-v1-demo)


**SILMA TTS v1** is a high-performance, **150M-parameter** bilingual (Arabic/English) TTS model developed by [SILMA AI](https://silma.ai). Built on the cutting-edge **F5-TTS diffusion architecture**, the model was **pretrained from scratch** using tens of thousands of hours of high-quality public and proprietary data. To give back to the community, SILMA TTS is released under a highly permissive license, making state-of-the-art speech synthesis accessible for both **research and commercial use**.


## Features

1. High-Fidelity Audio: Superior speech synthesis with high-quality output
2. 150M Parameter Model: Lightweight and efficient, works well in low-resource environments
3. Instant Voice Cloning
4. Ultra-Low Latency: Optimized for real-time applications with RTF around 0.12 (RTX 4090 GPU)
5. Bilingual Arabic & English Support: Native-level fluency for Arabic Fusha/MSA as well as English
6. Advanced Arabic Diacritization: Full support for Tashkeel to ensure precise pronunciation and context
7. Text Normalization: Utilizing NeMo Text Processing
8. Commercial-Friendly Licensing: Fully open-source under the Apache 2.0 License

## Installation

### Using pip 

```bash

# make sure ffmpeg is installed
apt-get update && apt install ffmpeg -y

# create and activate the environment
python -m venv silma-tts-env
source silma-tts-env/bin/activate

# install silma-tts library
pip install silma-tts

```

### From source

```bash

# make sure ffmpeg is installed
apt-get update && apt install ffmpeg -y

# create and activate the environment
python -m venv silma-tts-env
source silma-tts-env/bin/activate

# clone repo and install
git clone https://github.com/SILMA-AI/silma-tts.git
cd silma-tts
pip install -e .

```


## Usage & Inference

### Using Gradio app

```bash

# Run the following command
silma-tts-app

```

Then open the following browser link
http://127.0.0.1:7860/


### Inference using Python

```python

import time
from silma_tts.api import SilmaTTS

silma_tts = SilmaTTS()

## the voice/style you want to clone
reference_audio_file = "/root/silma-tts/src/silma_tts/infer/ref_audio_samples/ar.ref.24k.wav"
## the transcription of the reference_audio_file
reference_audio_text = "ويدقق النظر في القرآن الكريم وسائر الكتب السماوية ويتبع مسالك الرسل العظام عليهم الصلاة والسلام."

time_start = time.time()

wav, sr, spec = silma_tts.infer(
    ref_file=reference_audio_file,
    ref_text=reference_audio_text, # can also be left None - will be transcribed on the fly
    gen_text="""
    أنا نموذج جديد من سلمى لتحويل النص إلى كلام، يمكنني التحدث باللغة العربية مع أو بدون علامات التشكيل.
    I am the new SILMA model for converting text to speech, I can speak Arabic with or without diacritics.
    """.strip(),
    file_wave=str("generated_audio.wav"),
    seed=None,
    speed=1
)

time_end = time.time()
print(f"Time elapsed:{(time_end-time_start):.2f} seconds")

## Note 1: generated audio file (generated_audio.wav) will be saved in the current directory
## Note 2: You can also use the "wav" variable (raw waveform) to play the audio or return it via API

```

You can also run the example above directly using the following command, but only if you installed from source:

```bash

python src/silma_tts/infer/example.py

```



## Training

Our model is 100% compatible with [F5-TTS v1.1.7](https://github.com/SWivid/F5-TTS/releases/tag/1.1.7). This means you can make use of all the great resources, tools and community experience in the F5-TTS project.

### Steps

```bash
## clone F5-TTS v1.1.7
cd /root
git clone --depth 1  --branch 1.1.7 https://github.com/SWivid/F5-TTS.git
cd F5-TTS
pip install -e .

## download silma-tts model weights, vocab.txt, patched finetuning script and config.yaml
hf download silma-ai/silma-tts  --local-dir /root/silma-tts-v1-weights

## create the project, then replace the default configuration, vocabulary, and fine-tuning Python file. Note that the patched finetune_cli.py overrides the F5TTS_v1_Base config with the silma-tts model config.
mkdir /root/F5-TTS/data/finetuning_project_char
cp /root/silma-tts-v1-weights/vocab.txt /root/F5-TTS/data/finetuning_project_char
cp /root/silma-tts-v1-weights/finetune_cli.py /root/F5-TTS/src/f5_tts/train/finetune_cli.py
echo /root/silma-tts-v1-weights/config.yaml > /root/F5-TTS/src/f5_tts/configs/F5TTS_v1_Base.yaml



## open F5-TTS UI training pipeline 
f5-tts_finetune-gradio --port 7860 --host 0.0.0.0

## After preparing your data: go to "Train Model" tab -> "Path to the Pretrained Checkpoint" and enter the path to the silma-tts model weights file: /root/silma-tts-v1-weights/model.pt while leaving "Tokenizer File" empty

## For more information please follow the F5-TTS training guide below
## https://github.com/SWivid/F5-TTS/tree/main/src/f5_tts/train

```
Summary: you need to use the F5-TTS v1.1.7 training code, but use our config file, vocab, patched finetuning script and our pretrained weights


## Acknowledgements

This repo builds directly upon the excellent foundation laid by the [F5-TTS](https://github.com/SWivid/F5-TTS) project. The core architecture and the majority of the code is derived from their work. Our work introduces new pretrained weights and significant optimizations to the inference code.

**Other projects**
1. We use [CATT](https://github.com/abjadai/catt) to enrich arabic text with Tashkeel in case text doesn't include tashkeel.
2. We use [NeMo-text-processing](https://github.com/NVIDIA/NeMo-text-processing) to handle text normalization.



## Support

Unfortunately we don't have the capacity to actively support this repo. We also believe it is best to consolidate resources and knowledge in a single location. This is why we encourage you to visit the official F5-TTS repository, which is highly active and supported by a fantastic community.

* Please check if your question is already answered in the [F5-TTS Issues](https://github.com/SWivid/F5-TTS/issues?q=is%3Aissue) or open a new issue
* We monitor the F5-TTS Issues and will engage there if needed
* If you have a question that you think only we can answer, then please open a [community discussion](https://huggingface.co/silma-ai/silma-tts/discussions) on our HuggingFace repo
 


## Citation

```
@article{silma-tts-v1,
  title   = {SILMA TTS: A Lightweight Open Bilingual Text to Speech Model},
  author  = {SILMA AI},
  year    = {2026},
  url     = {https://github.com/SILMA-AI/silma-tts}
}
```
## License
1. Code: MIT License
2. Model Weights: Apache-2.0 License


## Disclaimer
**Please use the model responsibly.** By using this voice cloning ability, you agree to the following rules:

* **Get Consent:** Only clone voices with explicit, documented permission from the speaker.
* **No Malice or Fraud:** Do not use cloned voices to deceive, scam, harass, or defame others.
* **No Misinformation:** Do not create deepfakes to spread fake news or manipulate public opinion.
* **Be Transparent:** Always clearly disclose that the audio is AI-generated when sharing it.

> **Note:** You are solely responsible for the audio you generate. Misuse of this technology may result in severe legal consequences, including fraud, defamation, or right-of-publicity violations.
