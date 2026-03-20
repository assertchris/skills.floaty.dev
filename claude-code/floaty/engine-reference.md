# Engine Quick Reference

Floaty games use `engine.scope()`:

```javascript
engine.scope(({ start, cls, btn, text, rect, circle, line, pixel, sprite, mouse, camera, audio, random, tilemap, network }) => {
  start(({ delta }) => {
    cls('black')
    // game logic here
  }, {
    width: 400,
    height: 300,
    sprites: {},
    sounds: {},
  })
})
```

Both `sprites` and `sounds` must always be passed to `start()`, even if empty (`sprites: {}`, `sounds: {}`).

Do NOT pass a `target` option to `start()`. The playground editor and preview handle canvas targeting automatically — specifying a target will break rendering.

- Full API reference: https://floaty.dev/api
- Getting started guide: https://floaty.dev/getting-started
- Tutorials: https://floaty.dev/tutorials
