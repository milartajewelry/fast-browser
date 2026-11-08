# Route: TikTok Studio, post a video (worked October 2026)

- URL: https://www.tiktok.com/tiktokstudio/upload (loads late: navigate + wait 8, add 5 if needed).
- `find "file input accepting video"` -> take the ref with type=file -> `file_upload` (10 MB limit).
- wait 10 -> caption: `find "caption editor (contenteditable)"` -> click -> ctrl+a, Delete -> type.
  Type hashtags in a separate `type`, then wait 2 + Escape (closes the suggestion list). Check: `[contenteditable=true]` innerText.
- AI-generated label: `find "Show more"` -> click -> `find "AI-generated content switch"` -> click ->
  the dialog's "Turn on" button -> confirm the switch is "on" with `read_page` ref.
- `find "Post button"` -> click. If the content check isn't finished you get "Continue to post?": choose "Post now".
- Verify: /tiktokstudio/content -> under the caption first "Content in review / Only me", after ~30-60 s "Everyone".
- Trap: the Chrome extension sometimes doesn't deliver `file_upload` ("never delivered") even while other commands work.
  Not a size problem; restarting Chrome fixes it. After 2 tries, tell the user instead of retrying.
