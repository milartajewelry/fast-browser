# Route: Google Flow, talking video (November 2026)

The user signs in (an account with a Gemini / Google AI plan; Flow credits come with it). Never type passwords.

## Steps
- URL `https://flow.google.com/` -> "New project" (the first time there are welcome and privacy dialogs; leave marketing boxes unticked).
  CAREFUL: in the welcome dialog a "Sign out" button sits next to "Next"; clicking the wrong one signed out every Google account.
  Use `find` with the exact label, not coordinates.
- The project page has a chat agent on the right. The settings icon in the bottom box -> "Agent settings":
  default video ratio, count, model list (Veo Lite / Fast / Quality and others).
  **Changing the model resets the ratio to 16:9; pick 9:16 again.** Then "Save".
- Prompt box: `find "What do you want to create?"` -> click, check focus (contenteditable DIV) -> type -> Enter.
- The agent asks "Start this for N credits?" -> "Confirm". Done in ~1 min.
- Click the video -> editor; download icon at the top: GIF / 720p (original) / 1080p (upscaled). The file lands in Downloads.

## Traps
- In non-English lines, Veo sometimes blurs words. Check the audio against the script before delivering
  (e.g. with a speech-to-text model) and re-voice the bad parts if needed.
