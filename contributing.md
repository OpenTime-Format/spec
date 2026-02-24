# Contributing to OpenTime

Thanks for your interest in contributing! OpenTime is an open spec and community input makes it better.

## Ways to Contribute

### Report Issues
Found something unclear or inconsistent in the spec? [Open an issue](../../issues). Include:
- Which part of the spec you're referencing
- What's confusing or incorrect
- A suggested fix if you have one

### Propose Changes
Have an idea for a new item type, field, or behavior? Start with a discussion:
1. [Open an issue](../../issues) describing the proposal
2. Explain the use case — what problem does it solve?
3. Show an example of what the YAML would look like
4. Wait for community feedback before submitting a PR

### Fix Typos & Improve Docs
Small fixes are always welcome. Just open a PR directly.

### Build an Implementation
Building a parser, validator, or app that uses OpenTime? Awesome. Open a PR to add it to the Implementations table in the README.

### Add Examples
Real-world `.ot` files help people understand the format. Add yours to the `examples/` directory.

## Submitting a Pull Request

1. Fork the repo
2. Create a branch (`feat/new-field`, `fix/typo-in-spec`, etc.)
3. Make your changes
4. Submit a PR with a clear description of what and why

## Spec Change Process

Changes to the core spec go through a lightweight review:

1. **Proposal** — Issue describing the change and its use case
2. **Discussion** — Community feedback (minimum 7 days for non-trivial changes)
3. **Draft PR** — Spec language for the proposed change
4. **Review & Merge** — Maintainer review and merge

Bug fixes, typos, and clarifications can skip straight to a PR.

## Extension Fields

The `x_` prefix namespace is open for anyone. You don't need permission to use extension fields in your app — that's the whole point. But if you'd like your prefix registered in the docs (e.g., `x_myapp`), open a PR to add it to `spec/extensions.md`.

## Code of Conduct

Be kind. Be constructive. Assume good intent. We're building something useful together.

## License

By contributing, you agree that your contributions will be licensed under the same terms as the project:
- Spec text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- Code and examples: [MIT](./LICENSE-MIT)
