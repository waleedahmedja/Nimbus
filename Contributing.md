# Contributing

Nimbus is a personal project with a clear design philosophy. Contributions are welcome — but this isn't a feature request board. The roadmap is intentional and the scope is tight.

If you want to contribute, read [DESIGN.md](DESIGN.md) and [ARCHITECTURE.md](ARCHITECTURE.md) first. Understand what Nimbus is trying to be before suggesting what it could become.

---

## What's worth contributing

- Bug fixes — especially edge cases in the sky canvas, solar calculations, or API error handling
- Performance improvements that don't change visible behaviour
- Accessibility improvements — contrast, content descriptions, touch target sizes
- Locale/unit handling bugs
- Documentation corrections

## What's not a fit

- New features that aren't on the V2 roadmap
- UI changes that conflict with the design system
- Additional settings
- Notification types beyond what's documented in the roadmap
- Anything that adds personality or gamification

If you're unsure — open an issue first. Don't spend time on a PR that won't land.

---

## How to submit a PR

1. Fork the repo
2. Create a branch: `fix/your-description` or `improve/your-description`
3. Keep changes focused — one concern per PR
4. Match the existing code style — same architecture, same naming conventions
5. Test on at least one physical device (emulator is not enough for canvas work)
6. Open the PR with a clear description of what changed and why

---

## Code style

- Kotlin standard conventions throughout
- No wildcard imports
- `ktlint` formatting — run before committing
- Inline comments for anything non-obvious; no comments restating what the code says
- Every `@Composable` that takes more than 3 parameters uses named arguments

---

— **waleedahmedja**
