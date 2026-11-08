---
name: fast-browser
description: Speed rules for doing web work with Claude in Chrome (mcp__claude-in-chrome__*) or the built-in browser (mcp__Claude_Browser__*). Use ALWAYS, before the first browser step, when the task involves clicking, filling forms, uploading files or images, or repeating work on sites like Adobe Firefly / Express, Canva, Meta Business Suite, TikTok Studio, Google Flow, Shopify-style admin panels. Also use when the user says "do it faster", "this is too slow", "upload this image to the site", "generate a video on Adobe".
---

# Fast browser

Browser tasks are slow mostly for four reasons: taking a screenshot after every step, doing one action per round trip,
getting stuck in the operating system's file picker, and re-discovering the same site from scratch every time.
These rules remove all four. Goal: a task that took 20 minutes (upload 2 images, type a prompt) takes a few minutes,
not counting the site's own processing time.

## 0. Before you start (once)
- Load the tools in **one** ToolSearch call (Chrome):
  `select:mcp__claude-in-chrome__tabs_context_mcp,mcp__claude-in-chrome__navigate,mcp__claude-in-chrome__browser_batch,mcp__claude-in-chrome__find,mcp__claude-in-chrome__read_page,mcp__claude-in-chrome__get_page_text,mcp__claude-in-chrome__form_input,mcp__claude-in-chrome__file_upload,mcp__claude-in-chrome__javascript_tool,mcp__claude-in-chrome__computer,mcp__claude-in-chrome__tabs_close_mcp`
- **Check the route book first:** `routes/<site>.md` in this skill's folder. If a route exists, follow it directly
  instead of exploring the page again. Included routes:
  [TikTok Studio upload](routes/tiktok-studio-upload.md),
  [Meta Business Suite Reels scheduling](routes/meta-business-suite-reels.md),
  [Meta Ads Manager reading](routes/meta-ads-manager-read.md),
  [Adobe Firefly image + video](routes/adobe-firefly.md),
  [Google Flow video](routes/google-flow.md).
  Routes were recorded on a Turkish-language interface; button names may differ in your language, the method is the same.

## 1. Read the page, don't look at it
- To find a button or field use **`find`** ("Generate button", "prompt textbox", "file input") or **`read_page` with
  filter=interactive**. Click and type with the returned `ref_N`. Do not guess coordinates.
- **Buttons with the same name:** a page can have several buttons with the same label (Meta Business Suite has two "Next"
  buttons, one inside the thumbnail section). Describe the location in the `find` query, e.g.
  "Next button next to Cancel in the bottom footer bar". Read the description `find` returns; if it is in the wrong area,
  change the query instead of clicking.
- Take a screenshot only to check a visual result (is the generated image/video right?) or when `find`/`read_page` could
  not locate the element. When you do, use **`scale: 0.5`** (a quarter of the size, much faster).
- To read text use `get_page_text`; for a single value, `javascript_tool` with `document.querySelector(...).innerText`.

## 2. Batch your actions (the biggest win)
- Send consecutive steps whose outcome you can predict in **one `browser_batch`**: open, wait, click the field, type, submit, wait, check.
  Example: `[navigate, wait 3, find "prompt", left_click ref, javascript_tool (focus check)]`, then
  `[type, javascript_tool (text check), key Enter, wait 5, get_page_text]`.
- If you use coordinates inside a batch, they refer to the screenshot taken BEFORE the batch; prefer `ref`.
- Don't screenshot every intermediate step; verify once at the end of the batch.
- **Typing into the wrong place:** before typing, click the field by `ref` and check focus
  (`javascript_tool`: is `document.activeElement` really that field?). If focus is elsewhere, the keystrokes trigger the
  site's keyboard shortcuts (Instagram opened its notifications panel, Facebook opened a shortcuts dialog).
  After typing, check the text with `document.activeElement.innerText` (or `.value`). So never send the typing step blind
  inside a batch: first click + focus check, then type + text check.

## 3. File and image uploads (sites that say "upload from your computer")
- **NEVER click an "Upload / Browse / Choose file" button (unless the blocking code below is already running).**
  Clicking it opens the operating system's file picker. That window cannot be seen or controlled from the browser tools
  and the task freezes there. This is where hours get lost.
- Instead: use `find` to locate the page's **`input[type=file]`** element (query: "file input"), then call
  **`file_upload`** with that `ref` and the full file paths (`paths: ["C:\\...\\image1.jpg", "C:\\...\\image2.jpg"]`).
  The file goes straight in and no dialog opens. Several images can go into one input in one call if it supports
  `multiple`; otherwise one `file_upload` per input.
- If `find` cannot see the file input (hidden input), list them with `javascript_tool`:
  `[...document.querySelectorAll('input[type=file]')].map((e,i)=>({i, accept:e.accept, multiple:e.multiple, visible:e.offsetParent!==null}))`
  Then take that input's ref from `read_page` (filter=all) and use `file_upload`. Hidden is fine; `file_upload` works on hidden inputs.
- Drag-and-drop areas ("Drop files here") usually contain a hidden `input[type=file]`; same method.
- **If the input is created only when you press the button (Meta Business Suite):** there is no `input[type=file]` on the
  page until you press "Add video / Upload", and pressing it immediately opens the file picker. In that case:
  (1) BEFORE pressing the button, block the file picker with `javascript_tool`:
  `const oc=HTMLInputElement.prototype.click; HTMLInputElement.prototype.click=function(){ if(this.type==='file'){ if(!this.isConnected){this.style.display='none';document.body.appendChild(this);} return;} return oc.call(this);};`
  (2) press the button (no dialog opens, the input is added to the page), (3) `find "file input"` to get the new input,
  (4) `file_upload`. If the page reloads the patch is gone; run it again.
- **Inputs inside shadow DOM (Adobe Firefly):** `find`/`file_upload` may not reach them. Walk the shadow roots with
  `javascript_tool`, then assign a file through `DataTransfer` (see the Adobe Firefly route).
- There is a 10 MB total limit per `file_upload` call; upload big files in separate calls, or re-encode large videos first
  (for example with ffmpeg at ~9 Mbps, which keeps quality visually identical for short clips).
- One check after uploading: did a thumbnail appear (`find "uploaded image"` or a `scale 0.4` screenshot)?

## 4. Waiting
- While a site is generating (image/video), don't look every 5 seconds. First `wait` for the time the site states
  (in chunks of at most 10 s, back to back inside one batch), then read the status text with `get_page_text`/`find`
  ("Generating", "%", "Download").
- For long jobs (minutes), tell the user "generating, about X min" and wait with as few checks as possible.

## 5. Remember the route (the second time is much faster)
- When the task is done, write or update **`routes/<site>.md`**: the URL, which `find` query found which element, in what
  order, where you waited, which traps there were (pop-ups, cookie banners, tabs). Short bullet points.
  Use [routes/_template.md](routes/_template.md).
- Next time, read this file first and build the batch directly. If the site changed, fix the file.
- Don't write account numbers, e-mails or other private data into route files if you plan to share them; use placeholders
  like `<AD_ACCOUNT_ID>`.

## 6. General speed rules
- Work in the user's already open, signed-in Chrome; open a new tab (in a tab group). Signing in is the user's job.
- On cookie/consent banners pick the most privacy-preserving option; don't browse pages you don't need.
- If something fails, don't repeat the same thing 3 times. Read the state once with `read_page`, try a different way,
  and if that fails tell the user in one sentence.
- **Extension connection errors:** if `file_upload` or another tool returns "never delivered" / "connection was lost"
  TWICE, stop trying and ask the user to close and reopen Chrome. It is the extension connection, not the file size;
  `file_upload` can fail even while other commands still work. Restarting Chrome fixes it.
- **Verify after publishing:** after Share / Schedule, don't say "done". Open the page where the result shows and check it
  (Meta: the planner shows the day and time; TikTok: the posts list shows the caption and "Everyone").
  Don't leave the page before the site's "scheduling / in review" phase finishes.
- **"Leave site?" warning:** if `navigate` stops on an unsaved-changes warning: if it is only a draft you opened yourself
  and no longer need, pass with `force: true`; if it could be the user's unfinished work, don't, ask the user.
- Close the tabs you opened when you are done.
- Safety rules still apply: payment, passwords, purchases, sending or publishing need the user's approval. Speed never skips them.
