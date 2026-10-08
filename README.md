# 💌 Birthday Letter Maker

An animated birthday card that runs entirely in the browser. You write a letter, get a link, and the person who opens it gets a small show:

- a particle intro that forms their age, then bursts into fireworks
- floating envelopes with a 3D tilt and a glowing animated border
- a wax-sealed scroll that unrolls when they tap the seal
- words that come into focus one by one, with animated emojis
- a music-box "Happy Birthday" generated live with the Web Audio API
- a cake whose candles they can blow out for a final fireworks show

It works for one or two people (twins, best friends…), in French or English, with four color themes.

**Live demo:** `https://<your-username>.github.io/<repo-name>/`
**Make your own:** `https://<your-username>.github.io/<repo-name>/create.html`

## How it works

There's no backend and no database. `create.html` turns the form into JSON, compresses it with the browser's built-in `CompressionStream` (deflate), encodes it as base64url, and puts it after the `#` in the link:

```
index.html#d=z<compressed-base64url-json>
```

`index.html` reads that hash, decompresses and validates it, and builds the card from it. The part after `#` is never sent to the server, so the letter stays between you and whoever has the link. It also means anyone with the link can read it, so share it only with the people it's for.

All text from a link is escaped before rendering, so a crafted link can't inject HTML or scripts.

## Project structure

| File | What it does |
| --- | --- |
| `index.html` | The card. Reads the link data, or falls back to `DEFAULT_CONFIG`. |
| `create.html` | The form that generates links. Opening `create.html#d=...` loads an existing card back for editing. |

Inside `index.html`, the script is split into numbered sections:

1. **Content:** `DEFAULT_CONFIG`, the card shown when there's no link data
2. **Themes and UI text:** colors for each theme, and button labels in FR/EN
3. **Link codec:** decoding and validating the `#d=` data
4. **Helpers:** escaping, and wrapping emojis so they animate
5. **Build:** generates the title, envelopes (inline SVG) and gate text from the config
6. **Music box:** sine-wave notes with a decay envelope and a feedback delay for shimmer
7. **Canvases:** an animated glow background, and one particle system for sparks, confetti, fireworks and the intro
8. **Interaction:** gate → intro → envelope → scroll → letter → cake

## Customizing

**Without the form:** edit `DEFAULT_CONFIG` near the top of the script in `index.html`:

```js
const DEFAULT_CONFIG = {
  lang: "en",
  age: 25,
  date: "March 12, 2026",
  fromNote: "with all my love",
  from: "Your big brother, Alex",
  recipients: [
    { name: "Amara", theme: "royal", emojis: ["👑","✨","💜"],
      greeting: "Amara!! 🎉",
      body: "First paragraph...\n\nSecond paragraph...",
      love: "Happy birthday, queen. 👑💜" }
  ]
};
```

**Add a theme:** copy one of the entries in `THEMES`, change the colors, and add a matching swatch in `create.html`.

## Deploying on GitHub Pages

1. Push `index.html`, `create.html` and this README to a repository.
2. Go to **Settings → Pages**, choose the `main` branch and the root folder, and save.
3. Your card is live at `https://<your-username>.github.io/<repo-name>/` within a minute or two.

## Browser support

The card works in current versions of Chrome, Edge, Safari (16.4+) and Firefox (113+). Compressed links need `CompressionStream`, which those versions support. The generator falls back to uncompressed links when it isn't available. Animations are reduced for visitors who turn on "reduce motion" in their system settings.

## Tech

Plain HTML, CSS and JavaScript, with no framework and no build step.

- **CSS:** `@property` for the rotating conic-gradient borders, 3D transforms, `mask-composite` for the animated letter frame, `prefers-reduced-motion`
- **Canvas 2D:** additive blending for glow, a lightweight particle system, and pixel sampling of rendered text for the intro
- **Web Audio:** an oscillator-based music box with no audio files
- **IntersectionObserver:** word-by-word reveal as the letter scrolls into view
- **Streams API:** `CompressionStream` and `DecompressionStream` to keep links short

Some effects (blur-in text, split-letter titles, glowing borders, 3D tilt, click sparks) are inspired by the [React Bits](https://reactbits.dev) collection, rebuilt here in vanilla JavaScript.

## License

MIT. Feel free to fork it and make someone's birthday.
