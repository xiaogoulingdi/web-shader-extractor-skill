# Layering And FBO Reconstruction

Use this reference when rebuilding glass, liquid, refraction, portal, mask, or mixed DOM/WebGL scenes.

## FBO Refraction Rules

A glass shader refracts only the texture it samples. If an element is not rendered into that texture, it cannot appear inside the glass.

When rebuilding:

1. Identify the source texture sampled by the material.
2. Identify which objects are rendered into that source.
3. Recreate the source scene or pass before tuning material parameters.
4. Render DOM-like assets into WebGL if they must appear inside refraction.
5. Keep the visible DOM layer and the FBO copy synchronized by shared state.

## DOM Asset Refraction Strategies

Choose the lowest-friction strategy:

- **Sprite mirror**: keep DOM assets for layout/interaction, but render matching sprites into the FBO with shared positions.
- **Full WebGL migration**: move the asset fully into Three.js if it must participate in depth, masking, refraction, or lighting.
- **Approximation pass**: render colored silhouettes or blobs into the FBO when exact asset fidelity is unnecessary.
- **CSS-only fallback**: if the browser is weak, keep visible DOM assets and reduce glass refraction expectations.

## Pass Order Template

For glass over a procedural backdrop with falling stickers:

1. Update global pointer/scroll/time state.
2. Update DOM sticker positions and write shared screen coordinates.
3. Render procedural curtain/backdrop into FBO.
4. Render mirrored sticker sprites into the same FBO.
5. Render glass mesh sampling that FBO.
6. Render FX overlays such as smoke, pixel trail, portal lines.
7. Render DOM text/UI masks.

If the order is wrong, the visual may be one frame late or the glass may sample stale content.

## Color And Alpha

Check:

- renderer output color space
- texture color spaces for loaded images
- tone mapping and exposure
- premultiplied alpha expectations
- transparent material depthWrite/depthTest settings

For glass-like materials, excessive base color mix can hide the FBO. Too little base color can make the object feel like a flat lens. Tune after the pass order is correct.

## Debugging Symptoms

| Symptom | Likely cause |
| --- | --- |
| Glass does not bend stickers/images | assets are visible DOM only, not in FBO |
| Refraction appears black/flat | render target not populated, wrong clear color, wrong render order |
| Colors look washed out | sRGB/linear mismatch or tone mapping difference |
| Object flickers/disappears | depthWrite/depthTest issue on transparent material |
| Interaction feels delayed | state update order or damp/easing too slow |
| Ripples feel fake | screen-space rings not clipped to object silhouette |

