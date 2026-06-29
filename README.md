# Web Shader Extractor Skill

A Codex skill for extracting, analyzing, and rebuilding WebGL, Canvas, shader, FBO refraction, layered composition, and motion effects from websites.

This repository is a single-skill fork/adaptation based on the `web-shader-extractor` skill from [lixiaolin94/skills](https://github.com/lixiaolin94/skills). I kept it as a separate focused repository so it can publish and install only this one skill, while still preserving attribution to the upstream project.

If you want full GitHub fork lineage, the cleaner path is to fork `lixiaolin94/skills`, remove unrelated skills from your fork, and keep this repository as the focused published package. For now, this repo documents the upstream source clearly and keeps the reusable skill separate from demo/clone code.

This repository contains only one skill:

- `web-shader-extractor`

## Case Study / 实战表现

This fork was refined during a hands-on clone study of [Haoqi Wen](https://haoqi.design/)'s personal website, especially the homepage glass/liquid `hello` typography, falling stickers, curtain-like lighting, pointer interaction, FBO refraction, and arrow-driven scroll transition.

这次修改来自一次实战练习：复刻 [Haoqi Wen](https://haoqi.design/) 的个人网站首页，并在复刻过程中把观察、拆解、FBO 分层、运动编排和验证流程沉淀回这个 skill。它不是对原站素材或生产代码的再发布，而是一次学习型复刻，用来验证 skill 在真实高端 WebGL 页面上的工作方式。

- Original website: [haoqi.design](https://haoqi.design/)
- Original launch post: [Haoqi Wen on X](https://x.com/wenhaoqi/status/2068327540595552355)
- Example original work pages: [Inspire Mono](https://haoqi.design/inspire_mono), [WASM Design Utils](https://haoqi.design/wasm_design_utils)
- Clone demo: [xiaogoulingdi.github.io/hellodesign](https://xiaogoulingdi.github.io/hellodesign/)
- Clone repository: [xiaogoulingdi/hellodesign](https://github.com/xiaogoulingdi/hellodesign)

What the case study added back into the skill:

- observe the source page's motion grammar before writing shader code
- separate real pointer influence from fixed random motion plus local ripple/refraction
- verify which DOM/WebGL assets must enter the FBO before glass refraction can work
- treat scroll transitions as staged choreography, not just opacity changes
- compare original and clone through screenshots, phase captures, and interactive probing

## Install

Install this skill from this GitHub repository:

```bash
npx skills add https://github.com/xiaogoulingdi/web-shader-extractor-skill --skill web-shader-extractor
```

Or copy the `web-shader-extractor/` folder into your local Codex skills directory.

## What This Version Adds

This version extends the original workflow with notes from reconstructing high-end WebGL portfolio effects:

- visual choreography and scroll narrative analysis
- DOM/WebGL/FBO layering checks
- glass, liquid, refraction, mask, and portal reconstruction guidance
- iterative validation for pointer, scroll, and transition fidelity
- publishing, attribution, fork, and PR decision guidance

## Attribution

This skill is based on and adapted from the `web-shader-extractor` skill in [lixiaolin94/skills](https://github.com/lixiaolin94/skills).

The original repository README describes the project license as MIT. This focused fork preserves attribution to the upstream project and adds workflow notes, validation guidance, case-study notes, and publishing guidance developed during local WebGL reconstruction work.

## Publishing Boundaries

This repository is intended to publish the reusable skill workflow only.

Do not include downloaded proprietary assets, extracted private keys, copied production bundles, or site-specific code from third-party websites unless their license explicitly permits redistribution.

When using this skill to study another website, cite the original author and source clearly. Keep clone/demo work separate from the reusable skill itself, and describe reconstruction work as practice, research, or study unless you have explicit permission for commercial or official use.
