# FG Assistant — Farid Ghani's Personal Android Assistant

A Jarvis-style voice assistant for Android, inspired by the Mark LIV
desktop project's architecture (memory, plugin-style controllers, real
confirmation gating), rebuilt natively in Kotlin for Android.

## What's implemented (skeleton, needs finishing)

- `core/` — command parsing, confirmation flow, trusted-caller policy, memory
- `controllers/` — apps, files, camera, messaging, TTS
- `services/` — background wake-word listener, WhatsApp notification reader,
  call screening

## What every reply/message says first

Per your instruction, any time FG acts on Farid's behalf toward another
person, it opens with a disclosure line (`MessagingController.DISCLOSURE_PREFIX`):

> "Hi, I'm Farid Ghani's AI assistant (FG) — he's unavailable right now."

This is hardcoded, not a toggle, by design.

## Hard constraints baked into the design (Android, not this app, enforces these)

| What you asked for | What's actually possible |
|---|---|
| Silent app install/uninstall | OS always shows its own confirm dialog since Android 8 — no API removes it |
| Fully hidden background mic | Foreground mic service legally requires a visible notification since Android 9 |
| Auto-play & understand WhatsApp voice notes without opening the app | Notifications don't carry the audio file — only "a message arrived." Full access needs either the chat screen open (via an Accessibility Service you grant manually) or WhatsApp Business API |
| Bypass Play Protect | Not something any app can do to itself — this build doesn't attempt it |
| Voice-only identity check for "my father" | Not used for the location-sharing gate — matched against a saved contact ID instead, since voices can be imitated |

None of these are missing features — they're Android's own security model,
the same one every assistant app (including ones already on the Play Store)
has to work within.

## Next steps to make this buildable

1. Open in Android Studio, let Gradle sync, fix any version mismatches.
2. Pick + integrate a wake-word engine (Porcupine needs a free API key;
   Vosk is fully offline, slightly less accurate).
3. Wire `TTSManager` to Gemini Live API (reuse the same key flow as
   Mark LIV's `config/api_keys.json`) or ElevenLabs for voice quality.
4. Finish the `TODO`s in `CameraController`, `MessagingController`,
   `NotificationReaderService` (these need real MediaStore/SmsManager/
   AccessibilityService code — flagged clearly in each file).
5. Build the onboarding UI in `MainActivity` (voice picker, trusted
   contacts, the two manual settings links).
