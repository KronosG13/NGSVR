# Contributing to NGSVR

First off — thank you for being here. NGSVR is a community project and every contribution matters, whether it's code, documentation, testing, or just spreading the word.

Please read this before opening a PR or issue.

---

## Code of Conduct

Be respectful. This project is built by people who care about open social VR. Harassment, gatekeeping, and bad faith behavior will result in removal. That's the whole policy.

---

## Ways to Contribute

### 💻 Code
- Unreal Engine 5 (Blueprints or C++)
- Networking / EOS integration
- OpenXR / SteamVR integration
- Avatar pipeline (VRM, IK)
- World loading (glTF/GLB)
- UI systems

### 🎨 Technical Art
- Avatar shader development
- VRM pipeline tooling
- World creation tools and templates
- Performance optimization

### 🧪 Testing
- Testing on different VR headsets
- Testing different tracker setups (SlimeVR, Vive, etc)
- Bug reporting and reproduction steps

### 📝 Documentation
- Architecture documentation
- Setup guides
- API/system documentation
- Wiki contributions

### 🎭 Content
- Test avatars (VRM format, CC0 or equivalent license)
- Test worlds (glTF/GLB format, CC0 or equivalent license)

---

## Getting Started

> ⚠️ The project is in early POC stage. A full contributor build guide will be published at Milestone 1. Watch the repo for updates.

When ready, the setup will require:
- Windows 10 or 11
- Unreal Engine 5.4 or later
- Visual Studio 2022 (Community is fine)
- Git + Git LFS

---

## Branch Structure

| Branch | Purpose |
|---|---|
| `main` | Stable, always buildable |
| `dev` | Active development, may be unstable |
| `feature/feature-name` | Feature branch |

**Always branch off `dev`, not `main`.**

---

## Pull Request Guidelines

1. Branch from `dev`
2. Keep PRs focused — one feature or fix per PR
3. Write a clear description of what changed and why
4. If it's a visual change, include screenshots or a short video
5. Make sure the project still builds before opening a PR
6. Link any related issues

---

## Reporting Bugs

Use the bug report issue template. Include:
- What happened
- What you expected to happen
- Steps to reproduce
- Your hardware (headset, trackers, GPU)
- Unreal Engine version

---

## Feature Requests

Use the feature request issue template. Check existing issues first to avoid duplicates. Big feature ideas are better discussed in Discord before opening an issue.

---

## Questions?

Join the Discord (link in README). That's where the real-time discussion happens.

---

*NGSVR is licensed under GPLv3. By contributing, you agree that your contributions will be licensed under the same.*
