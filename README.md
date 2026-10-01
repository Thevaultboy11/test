# Face Recognition Test — static site for Vercel

Everything is plain static files. No build step, no login, no server.

```
index.html        the whole test (participant flow + Experimenter screen)
images/           17 aligned pictures (3 references + 14 test pictures)
vercel.json       small config (clean URLs, image caching)
```

## Deploy (pick one)

**A. Drag and drop (no GitHub)**
1. Unzip this folder.
2. Go to https://vercel.com/new → scroll down to deploy without Git, or run `npx vercel` inside the folder
   (`npx vercel --prod` for the public URL). Framework preset: **Other**. Build command: none. Output directory: `.` (root).

**B. GitHub**
1. Create a new repo and upload the contents of this folder (index.html, images/, vercel.json at the repo root).
2. On https://vercel.com/new import the repo → Framework preset **Other** → Deploy.
   Every push to the repo redeploys automatically.

## Answer key
Open `index.html`, find `BUILTIN_PICTURES` near the top. `correct` = position of the friend counting
from the LEFT (1 = leftmost). `null` = not scored yet. Change, save, redeploy.

## Where results go
Each participant's answers are saved in **their own browser** (local storage). That's fine if everyone
does the test on your laptop. If people take it on their own phones/computers, you won't see their
results unless you turn on collection:

### Optional: collect all results in one Google Sheet (free, ~5 min)
1. Create a Google Sheet → Extensions → Apps Script → paste:
```js
const COLS = ['participant_id','session_start','timestamp','participant_group','phase','block_number','block_order','stimulus_group','own_group','target_name','trial_number','picture','picture_file','num_faces','correct_position','chosen_position','correct','rt_ms'];
function doPost(e){
  const sh = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  if (sh.getLastRow() === 0) sh.appendRow(COLS);
  const r = JSON.parse(e.postData.contents);
  sh.appendRow(COLS.map(c => r[c] ?? ''));
  return ContentService.createTextOutput('ok');
}
```
2. Deploy → New deployment → type **Web app** → Execute as: **Me** → Who has access: **Anyone** → Deploy, copy the URL.
3. In `index.html` set `const RESULTS_URL = 'https://script.google.com/macros/s/.../exec';` and redeploy.
