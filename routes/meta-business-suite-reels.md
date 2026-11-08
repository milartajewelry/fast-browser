# Route: Meta Business Suite, schedule an Instagram Reel (worked October 2026)

- URL: `https://business.facebook.com/latest/reels_composer?business_id=<BUSINESS_ID>&asset_id=<PAGE_ID>`
- Upload limit: Chrome `file_upload` is 10 MB. Re-encode big videos first with ffmpeg at ~9 Mbps
  (an 8 s video is ~6 MB, visually identical).
- Order (all with `find` + `ref`, no coordinates):
  1. navigate + wait 6.
  2. `javascript_tool`: patch `HTMLInputElement.prototype.click` so type=file does nothing (no file picker; see SKILL.md section 3).
  3. `find "Add video button"` -> click -> `find "file input type=file"` -> `file_upload` (full path).
  4. wait 8 -> `find "Share to combobox"` -> click -> pick only the accounts you want (e.g. deselect the Facebook page) -> Escape.
     Check: `[role=combobox]` innerText shows only the Instagram account.
  5. `find "Reels caption textbox"` -> click -> type (\n line breaks and emoji work) -> check with `document.activeElement.innerText`.
  6. `find "Next button next to Cancel in the bottom footer bar"` (careful: the thumbnail section has another "Next", not that one).
  7. `find "Schedule option under scheduling options; Public radio"` -> click both.
  8. `find "schedule date field, hour spinbutton, minute spinbutton"` -> click hour and minute and type (date defaults to today).
  9. `find "bottom right Schedule submit button (next to Back)"` -> click -> wait at least 20 s ("Scheduling your post").
  10. Verify: on the content_calendar page the day column shows "HH:MM" and a thumbnail with the Instagram icon.
- Traps:
  - With only one video in the media, "Delete" doesn't work ("At least one video is required"). To change the video, reopen the page with `force` and start over.
  - Business Suite has no "AI info" label; if needed, write it in the caption or post from the Instagram app.
  - Trial Reels are not available in Business Suite.
