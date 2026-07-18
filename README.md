# T&C Lawyered — Android

**You clicked agree. We actually read it.**

A privacy-first Android app that reads Terms & Conditions and Privacy Policies
for you and explains them in plain English — TL;DR, key risks, what data is
collected, who it's shared with, and your rights. It runs on **your own** AI key,
and nothing about you ever leaves your phone.

> Sibling of the Chrome extension in the main
> [T&C Lawyered](https://github.com/tousifahmed20/T-C-Lawyered) repo. Both share
> the same hive backend, so a policy summarized on one client is instantly
> available on the other.

## 📲 Install

1. Download the latest **`app-debug.apk`** from the
   [Releases page](https://github.com/tousifahmed20/tc-lawyered-android/releases/latest).
2. Open it on your phone and tap **Install**. If prompted, allow *Install unknown
   apps* for your browser or files app (Android's normal sideload step).
3. Open the app → **Settings** → add an API key for one provider (Anthropic,
   OpenAI, Gemini, or [OpenRouter, which has free models](https://openrouter.ai/models)).

That's it. Requires Android 8.0 (API 26) or newer.

## How you feed it a policy

Android sandboxes apps so none can silently watch another. So there are two ways in:

- **Share sheet** — from any app or browser, **Share → T&C Lawyered** hands over a
  link or selected text. Best for full, multi-screen policy pages.
- **Floating bubble** — a hovering bubble captures the current screen and reads it
  with on-device OCR. Best for short in-app consent pop-ups. With the optional
  accessibility permission it can auto-scroll a long page and stitch the whole thing.

Either way, the text is processed on-device; only the extracted policy text goes to
*your* AI provider to be summarized.

## Privacy

- **No accounts, no tracking, no analytics.**
- Your API key is encrypted on-device (Android Keystore) and only ever sent to the
  provider you choose.
- Breach checks are matched **on your phone** — the site you're on is never sent out.
- The only thing that ever leaves the device is a hashed policy + its summary, and
  only after an authenticity check. Nothing that identifies you.

## Build from source

Requires Android Studio (Ladybug+) and JDK 17. The Gradle wrapper is committed
(pinned to 8.11.1), so no system Gradle is needed.

```bash
./gradlew assembleDebug   # → app/build/outputs/apk/debug/app-debug.apk
```

`local.properties` (your SDK path) is machine-specific and git-ignored — use
forward slashes: `sdk.dir=C:/Users/you/AppData/Local/Android/Sdk`.

> ⚠️ Clone to a path with **no `&`** in it — an `&` anywhere in the absolute path
> breaks the native build tools (aapt2/d8) mid-build.

The overlay and screen-capture features need a real device (or emulator) to grant
the draw-over-apps and MediaProjection permissions.

## Architecture

```
app/src/main/java/dev/tclawyered/app/
├── core/       Hash, domain-normalization, chunking, prompts — kept byte-for-byte
│               in sync with the extension so cross-client hive hits work
├── llm/        Provider abstraction + adapters (OpenRouter / Anthropic / OpenAI / Gemini)
├── pipeline/   hash → hive lookup → validate → summarize → diff → gated upload
├── data/       Hive client, encrypted settings, Room storage, breach + reputation lookups
├── capture/    Screen capture (MediaProjection) + ML Kit OCR
├── overlay/    Floating bubble + summary card
├── share/      Share-sheet entry point
├── audio/      Native text-to-speech
└── ui/         Home, Settings, History, Summary
```

The hash is the shared cache key across all clients, so `core/Hasher` and
`core/Domain` must stay identical to the extension's JS. See the main repo's
[`docs/PHASE2.md`](https://github.com/tousifahmed20/T-C-Lawyered/blob/main/docs/PHASE2.md)
for the backend contract.

## License

[MIT](LICENSE) — open source, no warranty.
