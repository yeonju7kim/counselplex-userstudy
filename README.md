# CounselPlex User Study

A/B pairwise comparison of counseling responses (CounselPlex vs PersonaPlex† FT), hosted on GitHub Pages.

## Structure

- `index.html` — single-page user study app (vanilla JS, no dependencies)
- `audio_ab/S##_…/A.wav, B.wav` — 20 sample pairs (silence capped 5s, 1.3× speed)
- `ab_key.csv` (NOT committed) — mapping of A/B → model. Keep private until after collection.

## Deploy to GitHub Pages

1. Push this folder to a public GitHub repo (e.g., `yourname/counselplex-userstudy`).
2. Repo → **Settings** → **Pages** → Source: `Deploy from a branch`, Branch: `main` / `(root)`. Save.
3. Wait ~1 minute. URL appears: `https://yourname.github.io/counselplex-userstudy/`
4. Share the URL with participants.

## Study design

- 20 conversation snippets (~30–60 sec each, 1.3× speed for brevity)
- Each snippet ends with two candidate responses (A, B) — same voice (woman_supporter), random assignment
- Participant picks A / B / About the same per sample
- At end: download responses as JSON, copy to clipboard, or send via mailto

**Total participant time**: ~30–45 minutes.

## Collecting responses

After participants finish, they send the downloaded JSON back via email (the page offers download + mailto + copy options). No backend needed.

To analyze, load the JSON files and join against `ab_key.csv`:

```python
import json, csv, glob
key = {r['sample_id']: r for r in csv.DictReader(open('ab_key.csv'))}
for resp_file in glob.glob('responses/*.json'):
    j = json.load(open(resp_file))
    for sid_full, ans in j['responses'].items():
        sid = sid_full.split('_')[0]  # 'S01'
        a_model = key[sid]['A_model']
        winner = ans['choice']
        if winner == 'A': preferred = a_model
        elif winner == 'B': preferred = key[sid]['B_model']
        else: preferred = 'tie'
        # tally CounselPlex preference
```

## Notes

- `.gitignore` excludes `ab_key.csv` so it's never committed publicly. Keep a local copy.
- Audio total: ~31 minutes across 20 samples × 2 models.
- Both A and B use the same TTS voice (woman_supporter); participants judge content, not voice.
