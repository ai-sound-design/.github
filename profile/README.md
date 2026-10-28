# AI Sound Design

**AI-assisted sound design inside Pro Tools.** A research prototype that brings sound generation, sound recommendation and spotting into the editor a sound designer already works in, as an AAX plugin backed by interchangeable AI services.

The user marks a time range on any track. The plugin looks at the video beneath it, sends it to a backend, and puts the answer back into the session: a generated sound on a track, a recording from a library, a memory location per sound event, or one generated sound per event with the closest library recordings underneath.

## Three repositories

| Repository | What it is |
|------------|------------|
| [**ai-protools-aax**](https://github.com/ai-sound-design/ai-protools-aax) | The Pro Tools plugin (JUCE, AAX) with its bundled companion scripts. Knows nothing about models: every backend is an *adapter profile*, one small JSON file. |
| [**ai-services**](https://github.com/ai-sound-design/ai-services) | A complete example backend: one `docker compose up` on a machine with an NVIDIA GPU. Also the reference for writing your own, service by service, with the contract each one has to fulfil. |
| [**ai-sound-webui**](https://github.com/ai-sound-design/ai-sound-webui) | A browser app with the same four modes, for working without Pro Tools: the basics of a DAW, and it can open Pro Tools sessions (`.ptx`). Runs as one more container next to the backend. |

```
Pro Tools ── AAX plugin ── companion scripts ──HTTP──▶ generation   (MMAudio)
                                              ──HTTP──▶ search       (X-CLIP + CLAP over pgvector)
                                              ──HTTP──▶ spotting     (vision-language model)
                                              ──HTTP──▶ hybrid       (composes the three)
Browser ──── web UI ───────────────────────────HTTP──▶ the same four services
```

## The four workflows

- **Spotting.** A vision-language model describes the sounds a silent picture implies; every event becomes a memory location with start and end timecode, plus a marker per clip boundary.
- **Sound Generation.** Sound for the selected range from the video and an optional prompt, or from the prompt alone, placed at the clip's position.
- **Sound Recommendation.** Sounds from a searchable library, by video, by text or both: your own folders of recordings, the BBC Sound Effects Archive, or any library a small script can describe.
- **Hybrid.** One generated sound per event, each on its own track. Events come from the session's memory locations or from the spotting service. On request the backend also finds library recordings for each event: the event's description proposes candidates, the generated sound picks among them and the matching stretch inside each, and they are placed underneath, or instead.

## Get started

1. Bring up the backend: clone [ai-services](https://github.com/ai-sound-design/ai-services), run `docker compose up -d`, wait for the first-start downloads, index a sound library.
2. Build the plugin from [ai-protools-aax](https://github.com/ai-sound-design/ai-protools-aax) and set up its embedded Python runtime.
3. Insert the plugin on a track. With the backend on the same machine nothing needs configuring; otherwise edit the addresses in the plugin's Settings.

Without Pro Tools, start [ai-sound-webui](https://github.com/ai-sound-design/ai-sound-webui) next to the backend instead of steps 2 and 3.

Each README carries the details, and each service folder in ai-services documents the HTTP contract, so a different model or an existing service can take the place of any part.

## Demo

The four modes in Pro Tools, recorded with the backend from ai-services; the videos play in the [plugin's README](https://github.com/ai-sound-design/ai-protools-aax#modes).

- [Spotting](https://github.com/user-attachments/assets/1b022c5e-7b1d-4c9f-98c3-0eaf6f0797dd)
- [Sound Recommendation](https://github.com/user-attachments/assets/3e4330c4-9918-4de3-83ac-7c54e1992e78)
- [Sound Generation](https://github.com/user-attachments/assets/3c08a27e-84db-42ea-bae5-5ca85c67e3d0)
- [Hybrid](https://github.com/user-attachments/assets/0d8f86b1-ce94-4fe8-9c39-b86115dfc923)

## Status

Research prototype, anonymized for peer review; not a production tool. The state submitted with the paper is preserved in the plugin repository (branch `paper-submission`, tag `v1-paper-submission`); development continues on `main`. Code is MIT licensed; the models and the BBC archive keep their own terms, listed in each repository.
