# Voice Assistant

## Purpose

Specifies always-listening, wake-word-activated, low-latency, interrupt
capable voice interaction as a first-class NOVA interface, addressing the
requirement for a "true voice assistant" — always listening, natural
conversation, interrupt while speaking, low latency, streaming voice.

## Scope

The voice interaction pipeline and its latency/interrupt requirements.
The underlying speech models are `local-speech-models.md`; provider
selection is `docs/18-providers/provider-routing.md`.

## Pipeline

```
Mic → Wake-word detector (always-on, local) → VAD (voice activity
detection) → Streaming STT → Planner (streaming-aware) → Streaming TTS →
Speaker
```

Every stage after wake-word detection is only active during an actual
utterance — "always listening" means the wake-word detector runs
continuously (fully local, minimal-resource, per
`local-speech-models.md`), not that raw audio is continuously sent to
STT or any cloud provider.

## Wake word

Wake-word detection runs on-device, always, regardless of the routing
policy configured for other capabilities — this is a `privacy-first`
requirement that is not overridable to cloud, since it is the one
component that must run continuously. Users configure the wake word
during the Setup Wizard (`docs/19-setup/setup-wizard.md`) and can add
custom wake phrases from Settings.

## Low latency and streaming

STT and TTS providers used for voice must support the `Stream<DomainChunk>` return type defined in `docs/18-providers/provider-interface.md` —
non-streaming providers can be used for other capabilities but are
excluded from the voice routing policy's candidate set, since round-trip
buffering defeats the responsiveness requirement. The Planner processes
partial transcripts incrementally where possible (e.g., beginning
intent classification before the utterance finishes) rather than waiting
for a final STT result on every turn.

## Interrupt (barge-in)

While NOVA is speaking (TTS streaming out), the VAD stage continues
listening. Detected speech during playback immediately halts TTS output
mid-stream and re-enters the listening state — this barge-in path is
tested as a first-class requirement, not an edge case, since natural
conversation depends on it. Barge-in sensitivity is user-configurable
(aggressive vs. conservative) to account for noisy environments.

## Natural conversation state

Voice sessions maintain a running conversational context distinct from a
single request/response turn, including handling of short affirmations,
corrections, and topic continuation across turns, backed by the same
episodic memory model as text conversations
(`docs/04-memory/memory-architecture.md`) — voice is a modality on the
same underlying conversation state, not a separate memory track.

## Multi-device voice

Wake-word detection runs independently on every device with a microphone
that has voice enabled (desktop, Android companion per
`docs/20-devices/android-companion.md`) — whichever device detects the
wake word first handles that utterance, avoiding both devices responding
to one command.

## Related documents

- `docs/25-failure-modes/FM-13-voice-tts-localization.md` — failure modes for this subsystem
- `local-speech-models.md` — STT/TTS model options and on-device
  requirements
- `docs/18-providers/provider-routing.md` — latency-optimized policy
  used by default for voice
- `docs/20-devices/android-companion.md` — mobile voice capture
- `docs/20-devices/ai-phone.md` — voice as the primary phone interface
  at full maturity
