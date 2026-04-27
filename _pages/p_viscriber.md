---
layout: single
title: "No Cloud, No Compromise: How I Built ViScriber for Secure Local Transcription"
excerpt: "Sensitive meeting recordings. A data privacy wall. No budget for enterprise tools. This is how I built a desktop transcriber that never touches the internet."
author_profile: true
permalink: /p_viscriber/
date: 2026-04-26
last_modified_at: 2026-04-26
toc: true
toc_label: "Table of Contents"
toc_icon: "microphone"
toc_sticky: true
tags:
  - cybersecurity
  - privacy
  - open-source
  - python
  - project
categories:
  - Projects
header:
  teaser: /assets/img/p_viscriber/vis-home.PNG
---

There's a moment in every consulting project where the work stacks up faster than you can process it. For me, it was interviews — lots of them.

I was in the middle of a requirements-gathering phase for a project at a financial institution. Back-to-back stakeholder sessions, each one recorded, each one full of details I needed to capture accurately. The kind of information where missing a word could mean missing a requirement, and missing a requirement could mean a very expensive problem down the line.

The obvious answer? Send the recordings to a cloud transcription service. Upload, wait, get your text back. Done.

Except — that wasn't an option.

---

## The Wall I Hit

The recordings contained sensitive operational information. Internal processes, system names, workflow details. Nothing you'd want sitting in a third-party cloud provider's infrastructure, even temporarily. The organisation had clear data handling requirements, and "upload to the internet" didn't pass that bar.

So cloud transcription was off the table. That left me with two options:

1. Listen back to every recording manually and type up notes.
2. Find another way.

Option 1 wasn't realistic. I was the only person on my side of the table. The volume of interviews meant that manual review would eat days I didn't have. And honestly, transcribing your own recordings by hand is one of those tasks that sounds manageable right up until you're three hours into a two-hour recording at 0.5x speed.

I needed something that could run locally — entirely on my machine, no data leaving, no accounts, no uploads — and still produce usable output.

---

## The Idea Takes Shape

I'd heard of [OpenAI Whisper](https://github.com/openai/whisper) before. It's an open-source speech recognition model that runs offline. The catch: using it out of the box requires comfort with the command line and Python environments. That's fine for me, but it wouldn't scale to anyone else who needed the same capability.

What I really wanted was a tool that:
- Ran completely offline
- Handled real audio from real meetings (background noise, accents, overlapping voices — the works)
- Produced clean, readable output without manual cleanup
- Could be handed to someone else without a Python tutorial

Nothing I found quite fit. So I built it.

{% include figure popup=true image_path="/assets/img/vis-home.PNG" alt="ViScriber home screen" caption="The ViScriber home screen — drag files in, pick your settings, and go." %}

---

## What ViScriber Does

[ViScriber](https://github.com/bpsghub/ViScriber) is a desktop application that transcribes video and audio files locally using Whisper. You drag in a file, hit Start, and get a transcript back. No account, no upload, no internet required after the first setup.

The key design decisions were driven by the problem, not by what was technically interesting:

**It had to be offline.** Whisper runs entirely on your machine. The audio never leaves. The transcript never leaves. There's no API call happening in the background.

**It had to work for non-technical users.** The app comes with a packaged installer — you download it, run it, and it works. No Python. No command line. No environment setup. Windows, macOS, and Linux are all supported.

**It had to handle the output usefully.** You get a `.txt` file and an `.srt` subtitle file for every recording. The `.srt` format is timestamped, which means you can jump straight to the part of the recording that matches any line of text. That alone saved a lot of time during review.

There's also an optional AI summary feature — you can pipe the transcript through Claude, OpenAI, or a local Ollama model to get a structured summary. That part does require an internet connection (or a locally running Ollama instance), and it's completely optional. The core transcription never needs it.

{% include figure popup=true image_path="/assets/img/vis-transcribing.PNG" alt="ViScriber transcribing a file" caption="ViScriber mid-transcription. Processing runs locally — the progress bar is the only thing moving on the network side." %}

---

## The Part That Surprised Me

Getting the transcription working was the straightforward part. The harder problem was packaging.

Shipping a Python application to someone who doesn't have Python installed is genuinely tricky. The app needs to bundle the runtime, the dependencies, and — for audio extraction — FFmpeg, a separate tool that most people have never heard of. Get any of that wrong and the user gets an unhelpful error on first launch.

I ended up using PyInstaller to bundle everything into a single executable, with platform-specific installers for each OS. The Windows build uses an Inno Setup script for a proper install wizard. macOS gets a DMG. Linux gets an AppImage.

FFmpeg was a headache. It's required to extract audio from video files, but it can't always be bundled due to licensing constraints. The solution: if FFmpeg isn't found at launch, the app detects it, tells you what's missing, and shows you exactly how to get it. Clear error messages beat silent failures.

---

## The Honest Limitation

Transcription quality depends on which Whisper model you choose, and that depends on your hardware.

The models range from `tiny` (75 MB, fast, lower accuracy) to `large` (1.5 GB, slow on CPU, highest accuracy). On a modern laptop with a decent GPU, the large model is fast enough to be practical. On older hardware with only CPU, you're looking at slower processing times.

For meeting transcriptions with clear audio, the `small` model hits a good balance. But if your recordings have heavy background noise or multiple speakers talking over each other — Whisper will do its best, but it's not magic.

That's a hardware constraint, not a software one. And it's a reasonable tradeoff when the alternative is your recordings sitting in someone else's cloud.

---

## The Result

{% include figure popup=true image_path="/assets/img/vis-result.PNG" alt="Completed transcript output" caption="A finished transcript — clean text output ready to work with, timestamped and no upload required." %}

ViScriber solved the immediate problem. The interview recordings got transcribed locally, the outputs were clean enough to work from directly, and the data stayed where it was supposed to stay — on the machine, under control.

The project forced me to think about a problem that comes up more often than people realise: what do you do when the convenient cloud solution isn't an option? Sometimes the answer is "accept the constraint." Sometimes it's "build the thing that doesn't have the constraint."

This time, building it was the right call.

---

## What's Next

The v1.0.2 release is out now on GitHub. If you're in a situation where local transcription would be useful — regulated industries, legal work, research, or just general privacy preference — it's worth a look. No setup friction, no account required.

[View ViScriber on GitHub →](https://github.com/bpsghub/ViScriber)

Longer term, I'm thinking about speaker diarisation (identifying *who* said what, not just *what* was said) and better handling of multi-speaker recordings. Those are harder problems. But the foundation is there.

---

*Have a use case where this would be useful, or a scenario I haven't thought of? I'd genuinely like to hear it — reach out on [LinkedIn](https://www.linkedin.com/in/bayu-p-suyatno/).*
