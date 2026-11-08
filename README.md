<p align="center">
  <a href="https://milartajewelry.com"><img src="assets/milarta-logo.jpg" alt="Milarta Jewelry" width="260"></a>
</p>

# fast-browser

*Made by [Milarta Jewelry](https://milartajewelry.com), Grand Bazaar, Istanbul.*

A Claude Code skill that makes browser work with **Claude in Chrome** much faster.

Without it, Claude takes a screenshot after every click, does one action per round trip, gets stuck in the
file picker when uploading, and re-learns the same site every time. Uploading two images and typing one prompt
took us 20 minutes. With these rules the same task takes a few minutes.

## What it changes
- **Reads the page instead of looking at it:** `find` / `read_page` instead of screenshots and guessed coordinates.
- **Batches actions:** predictable steps go in one `browser_batch` call, checked once at the end.
- **Uploads without the file picker:** never clicks "Upload", sends the file straight to the page's `input[type=file]`
  with `file_upload`, including hidden inputs, inputs created on click (Meta Business Suite) and shadow DOM inputs (Adobe Firefly).
- **Types in the right place:** checks focus before typing, so keystrokes don't trigger site shortcuts.
- **Remembers routes:** after a task it writes `routes/<site>.md`; next time it follows the route instead of exploring.
- **Waits sensibly:** waits for the site's stated time, then reads the status text.

Included routes: TikTok Studio upload, Meta Business Suite Reels scheduling, Meta Ads Manager reading,
Adobe Firefly image + video, Google Flow video, and a template for your own.

## Install
1. Download this repository (Code -> Download ZIP) and extract it.
2. Copy the `fast-browser` folder to:
   - all projects: `~/.claude/skills/fast-browser` (Windows: `C:\Users\<you>\.claude\skills\fast-browser`)
   - or one project only: `<project>/.claude/skills/fast-browser`
3. That's it. Skills load without a restart. Claude uses it automatically for browser tasks, or say "use fast-browser".

Requires the Claude in Chrome extension (or the Claude desktop app's built-in browser).

## Safety
- No code, no scripts to run, no network calls of its own. It is a text file of rules plus route notes.
- It never types passwords or payment details and keeps the normal approval rules for sending, publishing and buying.
- If you share your own route files, replace account numbers and e-mails with placeholders like `<AD_ACCOUNT_ID>`.

## Notes
- Routes were recorded on a Turkish-language interface; button labels may differ in your language. The method is the same.
- Sites change. If a route stops working, Claude fixes the route file.

---

## Türkçe

Claude'un Chrome eklentisiyle sitelerde yapılan işleri hızlandıran bir Claude Code becerisi. Claude her adımda
ekran resmi çekmek yerine sayfayı yazı olarak okur, adımları topluca yapar, görsel yüklerken Windows dosya
penceresine takılmaz ve öğrendiği site yollarını not eder. Kurulum: `fast-browser` klasörünü
`C:\Users\<kullanıcı>\.claude\skills\` içine kopyalayın.

## License
MIT
