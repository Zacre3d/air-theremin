# 🎶 Air Theremin

Play music by waving your hands in front of your webcam, like a [theremin](https://en.wikipedia.org/wiki/Theremin), the spooky sci-fi instrument you play without touching it.

- **Right hand**: pitch. Raise it for higher notes.
- **Left hand**: volume. Raise it to get louder, drop it to go quiet.

**[▶ Play it](https://YOUR-USERNAME.github.io/air-theremin/)** (turn your sound up)

<!-- Record a clip with the app's Record button and put it here, it's the best part of the README: -->
<!-- ![me playing the air theremin](demo.gif) -->

## Features

- Real-time hand tracking in the browser, no install and no server
- Four sounds: **Theremin**, **Glass**, **Synth** and **Ghost choir**
- **Snap to notes**: glide freely like a real theremin, or snap to a pentatonic or major scale so anything you play sounds good
- Light trails, a glowing orb that changes colour with pitch, and a live oscilloscope of the sound wave
- **Record button**: saves a video of your performance, with the sound, ready to post
- Works with one hand: if you never use your left hand, it plays at a comfortable volume
- No camera? Drag on the screen to play with a mouse or finger

## How it works

```
Webcam -> MediaPipe Hand Landmarker -> 21 points per hand
                                         |
                       palm centre of each hand (5 knuckle points averaged)
                                         |
              right half of screen --------------- left half of screen
                    |                                    |
          height -> frequency                   height -> volume
     (exponential: C3 to C6, 3 octaves)          (curved, so quiet is easy)
                    |                                    |
                    -----------> Web Audio API <----------
                   oscillator + vibrato -> filter -> volume -> reverb -> speakers
```

A few details that make it feel like an instrument rather than a tech demo:

- **Pitch is exponential, not linear.** Each octave doubles the frequency, so if height mapped straight to Hz, the low notes would be crammed at the bottom. Mapping height to `130.81 × 2^(3 × height)` gives every octave the same amount of space.
- **Smoothing.** Hand tracking jitters a little every frame. Positions are smoothed, and the audio uses `setTargetAtTime` so the pitch glides between values, which is also how a real theremin sounds.
- **Vibrato scales with pitch.** A slow 5.6 Hz wobble whose depth is a fixed percentage of the current note, so it sounds the same on high and low notes.
- **Reverb is generated, not downloaded.** The reverb's impulse response is 2.8 seconds of random noise fading out, created in code when the app starts.
- **Note snapping** converts the frequency to a MIDI note number (`69 + 12 × log2(f / 440)`), picks the nearest note in the chosen scale, and converts it back.
- **Recording** captures the canvas as a video stream and mixes in the audio from a `MediaStreamDestination` node, then saves both with `MediaRecorder`.

## Run it locally

It's one HTML file, but browsers only allow the camera on `https://` or `localhost`:

```bash
python -m http.server 8000
```

Then open http://localhost:8000.


## License

MIT
