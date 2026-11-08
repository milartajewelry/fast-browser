# Route: Adobe Firefly, image + image-to-video (November 2026)

Signed in to Chrome by the user. The remaining credits show in the profile menu; video cost is shown next to the Generate button.

## Image
- URL: `https://firefly.adobe.com/generate/image`.
- The aspect ratio list can't be scrolled; pick it with JS (shadow DOM): walk shadow roots, find `sp-menu-item`
  whose innerText is the ratio you want (e.g. "Portrait (9:16)") and `.click()` it.
- Prompt box: ProseMirror (`DIV`, aria "Prompt"), the text area at the bottom. Click -> ctrl+a -> focus check -> type.
  **Max 1000 characters**, the rest is cut.
- Generate: bottom right. ~15-20 s.
- Getting the result: the page's `img` blob URLs (search in shadow DOM). The newest one is not necessarily first; tell them apart by **size**.
  Download: `a.href=blob; a.download='name.png'; a.click()` -> goes to Chrome's Downloads.
- Reference image (same room/character): `find`/`file_upload` can't see the input. Give a blob to the shadow-DOM
  `input#file-input` with JS through `DataTransfer`. Confirm with the "Reference images (1/6)" text.
- Clicking a generated image opens the Edit view and the tab can freeze (screenshot timeouts); don't click unless needed.

## Video
- URL: `https://firefly.adobe.com/generate/video`. The model menu lists Firefly Video, Veo, Kling, Seedance, Runway, Ray and others.
- Some models reject a first frame with a real-looking human face ("reference content can't contain real human faces");
  no credits are charged. Veo accepted it.
- When you change the model, the aspect ratio resets to 16:9: pick 9:16 again (JS, as above).
- First frame: "Frames" -> "First" box. In shadow DOM there are 2 `input[type=file]` inside `FIREFLY-UPLOAD-NEW` (0 = First, 1 = Last).
  If you generated the image on another page, **save the blob to IndexedDB while still on that page**, then read it on the
  video page and give it with `DataTransfer` (blob URLs die when the page changes).
- Duration menu: open it, then JS-click the `sp-menu-item` with the duration text.
- Generate: "Generating video..." takes ~1.5-2.5 min; the tab must stay open. When done a `video` blob appears; download it
  the same way. You can't start a new generation before the current one finishes (button disabled).
- Screenshots sometimes time out after 30 s (heavy page): read the status text with JS instead, reopen the page if needed.

## Traps
- With sound off, clips come back silent.
- The credit counter doesn't update during generation; reload for the real number.
- Two tabs can work at once: generate an image in one tab while a video renders in another (IndexedDB is shared).
- If `javascript_tool` output contains long text that looks like function source, it may be blocked as "cookie/query string data"; return only the result.
