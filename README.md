# Doom-Models

Voice (TTS) model catalog for the **Doom AI** Android assistant.

Doom runs **entirely offline** — its voice engine is
[sherpa-onnx](https://github.com/k2-fsa/sherpa-onnx) with Piper VITS voices.
This repo is where the app looks up *which* voices exist and where to get them,
so new voices can be added **without shipping a new APK**.

## How the app uses this

```
GET https://raw.githubusercontent.com/ujjwalD16/Doom-Models/main/models.json
```

The app:

1. loads its **built-in** voice list (always present, always works offline),
2. tries this repo and replaces the list if the fetch succeeds,
3. caches the last good list, so a dead network or a bad file can never leave
   the user with an empty voice picker.

## `models.json` format

A JSON array. One object per voice:

| field | type | meaning |
|---|---|---|
| `id` | string | stable id, e.g. `hi_IN_rohan` |
| `displayName` | string | shown in the app, e.g. `Rohan` |
| `languageTag` | string | `en`, `hi-IN`, `es-ES`, `fr-FR`, `de-DE`, `it-IT`, `pt-BR`, `ru-RU`, `zh-CN`, `ja-JP`, `en-GB` |
| `gender` | string | `Male` / `Female` |
| `archiveName` | string | the `.tar.bz2` file name |
| `sizeMb` | number | approximate download size |
| `downloadUrl` | string | full URL to the archive |
| `previewUrl` | string | optional; empty means "preview after install" |

### Example

```json
{
  "id": "hi_IN_rohan",
  "displayName": "Rohan",
  "languageTag": "hi-IN",
  "gender": "Male",
  "archiveName": "vits-piper-hi_IN-rohan-medium.tar.bz2",
  "sizeMb": 63.0,
  "downloadUrl": "https://github.com/k2-fsa/sherpa-onnx/releases/download/tts-models/vits-piper-hi_IN-rohan-medium.tar.bz2",
  "previewUrl": ""
}
```

## Adding a voice

1. Find a Piper/VITS voice on the
   [sherpa-onnx `tts-models` release](https://github.com/k2-fsa/sherpa-onnx/releases/tag/tts-models).
2. Copy its exact `.tar.bz2` asset name into `archiveName`.
3. Set `downloadUrl` to the release URL for that asset.
4. Pick a unique `id` and a human `displayName`.
5. Add the object to `models.json` and commit.

**Rule:** `downloadUrl` must point at a file that actually exists. A wrong URL
is what made the old "Ryan (Test Server Voice)" entry fail — the app now falls
back safely, but a broken link is still a broken voice.

## Currently included (20 voices, 11 languages)

| Language | Voices |
|---|---|
| English | Ryan, Amy, Lessac, Kristin |
| English (UK) | Cori, Alan |
| **Hindi** | **Rohan, Priyamvada** |
| Spanish | Dave, Sharvard |
| French | Siwis, UPMC |
| German | Thorsten, Eva |
| Italian | Riccardo |
| Portuguese | Faber |
| Russian | Dmitri, Irina |
| Chinese | Huayan |
| Japanese | Kokoro (multi-lang) |

Every `downloadUrl` in `models.json` was verified to resolve on the upstream
release before being committed.

## License

Voice models are the property of their upstream authors (mostly Piper /
rhasspy, various permissive licenses). This repo only stores **metadata** —
no model weights are hosted here.
