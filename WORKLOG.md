# Worklog

## 2026-10-16 — Studio CI migration

Goal: move existing Notes workflow compute to Studio while preserving its Hugo 0.96.0 extended behavior and explicit hosted rollback.

The Linux ARM64 job uses a root-owned Hugo binary compiled once from the exact upstream v0.96.0 commit (2fd4a7d3d6845e75f8b8ae3a2a7bd91438967bbb), CGO_ENABLED=1 and the extended build tag. This is a locally compiled binary, not an upstream release binary. The workflow checks its frozen checksum and exact version before adding it to PATH. Other platforms retain the original peaceiris/actions-hugo@v2 setup with hugo-version 0.96.0 and extended=true. The original Docker indexing, hugo --minify build, hugo branch trigger, master Pages deployment and CNAME remain unchanged. The inherited disabled upstream-only Docker publish workflow is untouched.

Validation during preparation: shell syntax passed; parsed before/after equality permits only runs-on, platform condition on the original Hugo setup and one additional platform setup step; profile/default/hosted/fork resolutions checked. No build, install, workflow dispatch or deployment was performed. Before applying this candidate, centrally build/install the pinned binary, verify its version/checksum, dynamic libraries and synthetic SCSS compilation, then perform frozen-source build-only checks of both Notes repositories without running their Deploy step. Apply using [skip ci] so the source migration itself does not deploy Pages.

Hosted rollback (retain JSON quotation marks): STUDIO_CI_UBUNTU_20_04 = "ubuntu-24.04". The original source label ubuntu-20.04 was retired on April 15, 2025, so hosted rollback and public fork fallback deliberately use Ubuntu 24.04 x64 while preserving Hugo 0.96.0 extended. The profile variable retains its original-label name. Evidence: https://github.blog/changelog/2025-01-15-github-actions-ubuntu-20-runner-image-brownout-dates-and-other-breaking-changes/. Local profile: ["self-hosted","Linux","ARM64","studio-linux","studio-run-{0}"]. Public fork pull requests use the functional hosted ubuntu-24.04 fallback. Runtime readiness is separate from this prepared source manifest.
