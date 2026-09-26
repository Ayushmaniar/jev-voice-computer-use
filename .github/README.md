<p align="center">
  <img src="../voice_control/assets/jev-voice-logo.png" alt="Jev Voice Control logo" width="96">
</p>

<h1 align="center">Jev Voice Control</h1>

<p align="center">
  <b>Hold Right Ctrl, say what you want, and Windows does it.</b><br>
  Jev computer use that finishes multi-step tasks and types into text fields, with no LLM.
</p>

<p align="center">
  <a href="https://github.com/Ayushmaniar/jev-voice-computer-use/releases/latest"><b>Download for Windows</b></a>
  · free · MIT license · no admin rights or Python needed
</p>

![Saying "Switch Windows to light mode" while the app clicks through Settings on its own](assets/demo-light-mode.gif)

*One spoken sentence. The app opens Settings, goes to Personalization and picks the light theme. The pill at the
bottom shows each step as it happens.*

## Get started

1. **Get an OpenRouter key** at [openrouter.ai/keys](https://openrouter.ai/keys). Jev runs through OpenRouter, so there
   is no waitlist, even while TypeSafe's own Jev signups are paused. A TypeSafe AI key works too.
2. **Download and run** [`JevVoiceSetup`](https://github.com/Ayushmaniar/jev-voice-computer-use/releases/latest). It
   installs for your user only. If it finds an OpenRouter key on the PC, it offers to use it. Otherwise it asks for
   one on first start.
3. **Wait for the green dot.** On first start the app downloads its speech model: about 150 MB, or 1.6 GB with the
   NVIDIA libraries on a PC with an NVIDIA GPU. When the dot on the small pill at the bottom of the screen turns green,
   hold **Right Ctrl**, speak, and let go.

Things to try:

- "Switch Windows to light mode."
- "Go to Gmail, search for OpenRouter, and open the receipt email."
- "Create an event tomorrow at 3 p.m. called Dentist." (in Google Calendar)
- "Turn on French subtitles." (in VLC)

## How it works

[Jev](https://openrouter.ai/docs/guides/community/jev) is TypeSafe's decision model. It never writes text. You give
it options and it picks one, with a probability. That makes it fast and cheap, but on its own it does one thing, like
one click. The harness in this repo turns it into something you can use every day:

- **It reads the screen as controls, not pixels.** Windows UI Automation lists the buttons, text fields, links and
  menus of the app in front of you. No screenshots are taken.
- **It loops until the goal is done.** Jev picks one action. The harness does it, waits for the screen to settle,
  reads it again, and asks Jev whether the goal is finished. Then it repeats, up to 12 actions.
- **It types without an LLM.** Every run of 1 to 14 consecutive words from what you said becomes an option (up to
  254 of them, with quoted text and whatever follows "type" or "search for" first). Jev picks the one that belongs
  in the focused field. Say "search for OpenRouter" and it types "OpenRouter". Everything it types is copied from your
  own words.
- **Speech stays on your PC.** [faster-whisper](https://github.com/SYSTRAN/faster-whisper) turns speech into text,
  on an NVIDIA GPU when there is one. That takes about 0.3 s on an RTX 4070 laptop.

[`voice_control/GOAL_MODE.md`](../voice_control/GOAL_MODE.md) describes the loop in detail, and
[`voice_control/README.md`](../voice_control/README.md) covers the app, its settings and its tests.

## Does it work?

Steve Sewell published a benchmark of Jev computer use:
[steve8708/jev-browser-benchmark](https://github.com/steve8708/jev-browser-benchmark). This app was run on a rebuild
of it:

| System | Tasks finished | Cost per success |
| --- | ---: | ---: |
| Jev-only systems (published) | 33–37% | $0.0012–0.0018 |
| **Jev Voice Control** (rebuild, 61 tasks × 2 runs) | **68%** | $0.0032 |
| Screenshot LLM or LLM + Jev hybrid (published) | 89.5% | $0.0090–0.0128 |

It's a rebuild, not the same measurement. Part of the task set was used to tune the harness, and failures include
Gmail moves, Drive and Spotify. [`voice_control/BENCHMARK.md`](../voice_control/BENCHMARK.md) has every
run, the held-out results and each failure.

## Safety and privacy

- **It only listens while you hold Right Ctrl.** There is no wake word or always-on microphone.
- **It asks before destructive clicks.** Delete, Remove, Erase, Discard, Trash, Uninstall and the Delete key wait for
  your approval. Save, Send and Submit run on their own while **Run actions automatically** is on. Turn it off to
  approve every step.
- **Passwords stay manual.** It never types into password or verification-code fields. An email or phone field only
  gets an address or number you said.
- **What leaves your PC:** the text of what you said, the names of the controls on screen, open window titles and
  installed app names go to OpenRouter or TypeSafe, whichever key you use. Your audio is transcribed on your PC.
- **No logs unless you ask.** By default the app keeps no record of your commands. Turn on **Keep logs** in the activity panel
  to save each command's audio and the on-screen text it acted on in `%LOCALAPPDATA%\Jev Voice Control\logs`,
  for replaying and debugging.

## Limits

- **Windows only**, because it depends on Windows UI Automation.
- **Most apps work, but not all.** Games and apps that draw their own controls expose little to UI Automation, and
  quality varies from app to app.
- **It can't write text for you.** It types the words you said. It won't compose a reply you didn't dictate.
- **It's a first version.** About a third of the benchmark tasks still fail. Please
  [open an issue](https://github.com/Ayushmaniar/jev-voice-computer-use/issues) when it gets something wrong.

## Other ways to install

**With a coding agent.** Ask it to install Jev Voice Control. [AGENTS.md](../AGENTS.md) has everything it needs.
In short, it runs:

```powershell
.\installer\install.ps1
```

This builds Setup if needed, installs silently, picks up a key already on the PC, and prints the status. The
installed `configure.cmd` lists the keys it found, saves or checks a key, and reports whether everything is ready.

**Build the installer yourself** (needs Inno Setup: `winget install JRSoftware.InnoSetup`):

```powershell
.\installer\build.ps1        # writes installer\dist\JevVoiceSetup-<version>.exe
```

**From source**, for development:

```powershell
python -m venv .venv-voice
.\.venv-voice\Scripts\python.exe -m pip install -r requirements-voice.txt
.\.venv-voice\Scripts\python.exe -m voice_control.app
```

The app asks for a key on first start, offering any it finds. You can also put `OPENROUTER_API_KEY=...` in
`.env.openrouter`, or a TypeSafe AI key as `TYPESAFE_API_KEY=...` in `.env.typesafe`, at the repository root (both
are git-ignored). [voice_control/README.md](../voice_control/README.md) covers usage, settings, design notes and
tests.

## License

[MIT](../LICENSE).
