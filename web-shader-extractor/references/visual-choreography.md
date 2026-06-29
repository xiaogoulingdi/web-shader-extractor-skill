# Visual Choreography For Shader Site Cloning

Use this reference when the target page is a polished portfolio, campaign site, product showcase, or other experience where the perceived effect depends on timing, layered composition, and interaction choreography, not shader code alone.

## Capture The Motion Grammar

Before implementing, identify the visual "sentence" of the effect:

- **Entry object**: the object that starts the transition, such as a cursor, arrow, sticker, blob, title, or scroll marker.
- **Transformation**: whether it scales, flips, stretches, refracts, dissolves, or becomes a mask.
- **Reveal logic**: whether text appears by opacity, clip-path, stencil, FBO mask, bloom threshold, geometry occlusion, or camera move.
- **Exit logic**: whether the effect disappears independently, reverses the entry, or hands off to another scene.
- **Hero object continuity**: whether a small object in one scene reappears as the transition driver later.

Record this as a timeline with phase boundaries. Example:

| Phase | Driver | What to observe |
| --- | --- | --- |
| 0.00-0.18 | Small arrow/cursor | moves from corner to center |
| 0.18-0.34 | Arrow/cursor | flips to back face, portal lines begin |
| 0.34-0.58 | Arrow/cursor as mask | grows and reveals headline |
| 0.58-0.72 | Text | holds briefly while background continues |
| 0.72-1.00 | Arrow/cursor | reverse motion, text clips away, object shrinks |

## Distinguish Effect Types

Do not flatten everything into a shader:

- **Material effect**: glass, liquid, dispersion, bloom, fresnel, noise normals.
- **Compositing effect**: FBO/refraction target, background pass, mask pass, postprocessing.
- **DOM/layout effect**: text hierarchy, navigation, stickers, readable content.
- **Motion effect**: scroll easing, pointer response, object staging, reveal timing.
- **Narrative effect**: the object that explains why a transition happens.

If a clone "has the shader" but still feels wrong, inspect motion grammar and narrative continuity before adding more shader math.

## Interaction Fidelity

When comparing pointer or scroll interaction:

- Observe whether objects are physically affected or only visually distorted.
- Separate fixed random trajectories from pointer-driven perturbation.
- Check whether pointer effects persist as rings, smoke, wake, shimmer, or mask deformation.
- Note whether the effect returns to the original shape after pointer exit.
- Check if interaction is local to the object silhouette or screen-wide.

Example: stickers may fall on fixed paths while the glass object refracts them. If DOM stickers are not rendered into the refraction FBO, the glass can never bend them, no matter how good the glass shader is.

## Layering Checklist

For each visual element, record where it lives:

- DOM/CSS layer
- 2D canvas layer
- Three.js scene
- offscreen FBO/pass
- postprocessing pass

Then record visibility order and data flow:

- What is visible to the camera?
- What is visible to the refraction sampler?
- What is only decorative overlay?
- Which layer receives pointer/scroll state?

Common failure: a DOM asset appears visually behind glass, but it is not in the FBO sampled by the glass shader. The user sees overlap, but the material cannot refract it.

## Visual Notes To Capture

During manual observation, write short notes for:

- dominant color space and contrast
- opacity/transparency behavior
- edge highlights and rim lighting
- whether distortion is ring-like, smoke-like, elastic, or turbulent
- how large the transition focus/core appears relative to viewport
- how long each phase holds
- whether the final state contains stray transition objects

Screenshots alone are not enough for scroll narratives. Capture short screen recordings or at least 3-5 screenshots at fixed phase percentages.

