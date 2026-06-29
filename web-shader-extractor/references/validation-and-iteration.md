# Validation And Iteration

Use this reference after an initial port exists and the user is refining fidelity.

## Validate In Three Modes

1. **Static screenshots**: compare composition, brightness, color, spacing, and visible artifacts.
2. **Timed phase captures**: compare transition stages at fixed scroll/time percentages.
3. **Interactive probing**: move pointer across target objects and scroll slowly/quickly to inspect response.

For scroll narratives, do not rely on a single screenshot. Most regressions appear between phases.

## Browser Automation Boundaries

Use browser automation for:

- detecting blank canvas, loader stuck states, console errors
- screenshotting specific viewport sizes
- checking DOM/CSS state
- smoke-testing scroll/pointer interactions

If automation times out on heavy WebGL pages, use shorter actions, local dev server checks, or Chrome headless/CDP screenshots. Record what was and was not verified.

## Visual Diff Questions

Ask these before tuning random numbers:

- Is the source material being sampled by the shader the same kind of content?
- Is the transition driven by the same object?
- Is the scene layer order equivalent?
- Is the timing curve equivalent?
- Is the effect local to the same object or full-screen?
- Is the final state free of stray transition objects?

Only tune parameters after answering these.

## Iteration Log

For serious reverse-engineering work, keep a short implementation log:

- observed source behavior
- attempted reconstruction
- known mismatch
- root cause hypothesis
- fix
- verification method

This log can later become `EXTRACTION-REPORT.md`.

## Performance Checks

Watch for:

- too many full-resolution passes
- excessive high-DPI render targets
- canvas loops drawing thousands of primitives every frame
- unnecessary DOM layout reads inside animation frames
- asset duplication between DOM and WebGL without shared state

Prefer stable dimensions and bounded object counts. Use lower pixel ratio or simplified FX on mobile.

