# Publishing And Attribution

Use this reference when publishing a modified skill or a clone/reconstruction workflow.

## For A Modified Skill

If the skill is based on another author's GitHub project:

1. Check the license first.
2. Preserve copyright/license notices.
3. Mention the upstream project in your README or skill documentation.
4. Clearly mark your changes, especially if behavior or tool assumptions changed.
5. Prefer a fork if you want to publish a modified version with history.

If there is no license, do not assume open-source reuse rights. You can still keep private local changes, but public redistribution is legally ambiguous.

## Separate Skill Code From Cloned Site Work

Treat the skill itself and any reconstructed website as different artifacts:

- The skill may describe reverse-engineering workflow, observation checklists, validation routines, and reusable scripts.
- The reconstructed website may contain implementation code inspired by a target, but should not include copied proprietary assets, private bundles, or credentials unless the license explicitly permits it.
- If publishing both, keep them in separate repositories or clearly separated folders so users can understand what is tooling and what is a demo.
- Attribute the source of the skill workflow and separately attribute any visual reference sites used for study.

## Fork Or Pull Request?

Use this decision rule:

- **Fork only** when your changes are personal, opinionated, or tailored to your workflow.
- **Pull request upstream** when changes are generally useful, documented, and aligned with the original project's scope.
- **Open an issue first** when the change is large, changes philosophy, or adds new dependencies.
- **Keep a separate repo** when your version becomes a derivative skill with different goals.

For this skill, additions like visual choreography, FBO/DOM layering, and validation workflow are likely generally useful. A clean PR could be reasonable if the upstream project is active and welcomes contributions.

Before opening a PR:

1. Create a small branch focused on reusable workflow improvements, not site-specific implementation details.
2. Remove references to private local paths, downloaded target assets, and one-off project names unless they are necessary examples.
3. Keep the diff reviewable: update `SKILL.md`, references, and scripts separately if the upstream style supports it.
4. Explain which real failure modes motivated the changes, such as DOM assets not entering the refraction FBO, pointer effects being global instead of local, or scroll transitions losing the original driver object.
5. If the upstream project uses a different tool assumption, frame Chrome DevTools MCP, browser automation, or FBO validation steps as optional/adaptable rather than universal mandates.

## Suggested Attribution Text

Use a concise note such as:

> Based on the original `web-shader-extractor` workflow by <author/project>. This fork extends the workflow with visual choreography analysis, DOM/WebGL/FBO layering guidance, and iterative validation notes from portfolio-site reconstruction work.

Replace `<author/project>` with the actual upstream name and link.

## What Not To Publish

Avoid publishing:

- downloaded proprietary assets from target websites
- extracted private API keys or tokens
- large copied source bundles from sites without permission
- claims that imply the clone is endorsed by the original creator

Publish process, tooling, and educational notes; keep third-party assets and site-specific extracted code out unless the license allows it.
