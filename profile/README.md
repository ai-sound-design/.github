# AI Sound Design

**AI-assisted sound design inside Pro Tools.** A research prototype that brings sound generation, sound recommendation and spotting into the editor a sound designer already works in, as an AAX plugin backed by interchangeable AI services.

The user marks a time range on any track. The plugin looks at the video beneath it, sends it to a backend, and puts the answer back into the session: a generated sound on a track, a recording from a library, a memory location per sound event, or one generated sound per event with the closest library recordings underneath.

## Two repositories

| Repository | What it is |
|------------|------------|
| [**ai-protools-aax**](https://github.com/ai-sound-design/ai-protools-aax) | The Pro Tools plugin (JUCE, AAX) with its bundled companion scripts. Knows nothing about models: every backend is an *adapter profile*, one small JSON file. |
| [**ai-services**](https://github.com/ai-sound-design/ai-services) | A complete example backend: one `docker compose up` on a machine with an NVIDIA GPU. Also the reference for writing your own, service by service, with the contract each one has to fulfil. |

```
Pro Tools ── AAX plugin ── companion scripts ──HTTP──▶ generation   (MMAudio)
                                              ──HTTP──▶ search       (X-CLIP + CLAP over pgvector)
                                              ──HTTP──▶ spotting     (vision-language model)
                                              ──HTTP──▶ hybrid       (composes the three)
```

## The four workflows

- **Spotting.** A vision-language model describes the sounds a silent picture implies; every event becomes a memory location with start and end timecode, plus a marker per clip boundary.
- **Sound Generation.** Sound for the selected range from the video and an optional prompt, or from the prompt alone, placed at the clip's position.
- **Sound Recommendation.** Sounds from a searchable library, by video, by text or both: your own folders of recordings, the BBC Sound Effects Archive, or any library a small script can describe.
- **Hybrid.** One generated sound per event, each on its own track. Events come from the session's memory locations or from the spotting service. On request the backend also finds library recordings that *sound like* each generated sound, located to the second inside long recordings, and places them underneath, or instead.

## Get started

1. Bring up the backend: clone [ai-services](https://github.com/ai-sound-design/ai-services), run `docker compose up -d`, wait for the first-start downloads, index a sound library.
2. Build the plugin from [ai-protools-aax](https://github.com/ai-sound-design/ai-protools-aax) and set up its embedded Python runtime.
3. Insert the plugin on a track. With the backend on the same machine nothing needs configuring; otherwise edit the addresses in the plugin's Settings.

Each README carries the details, and each service folder in ai-services documents the HTTP contract, so a different model or an existing service can take the place of any part.

## Demo

- [Sound Generation](https://github.com/user-attachments/assets/cbe2bbf0-ac58-4ff3-87b1-0d0e45d20e33)
- [Sound Recommendation](https://github.com/user-attachments/assets/05d01e95-872f-4549-a6ed-f4d72c6ab3d3)
- [Spotting](https://github.com/user-attachments/assets/4fcb9db9-5133-450c-b541-a01176fe2abb)

## Status

Research prototype, anonymized for peer review; not a production tool. The state submitted with the paper is preserved in the plugin repository (branch `paper-submission`, tag `v1-paper-submission`); development continues on `main`. Code is MIT licensed; the models and the BBC archive keep their own terms, listed in each repository.
