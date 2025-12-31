# Contributing to NoobBook

Thanks for your interest in contributing to NoobBook!

## Branch Strategy

We use two main branches:

| Branch | Purpose |
|--------|---------|
| `main` | Stable release. Use this to test and play around with NoobBook. |
| `develop` | Latest changes. This is where all new work goes. |

We don't have a separate staging branch yet - `develop` serves as both development and staging for now.

## How to Contribute

1. **Fork the repository**

2. **Pull from `develop`** (not main)
   ```bash
   git checkout develop
   git pull origin develop
   ```

3. **Create your feature branch**
   ```bash
   git checkout -b your-feature-name
   ```

4. **Make your changes**
   - See `CLAUDE.md` for code guidelines
   - Follow existing patterns in the codebase

5. **Push and create a Pull Request to `develop`**
   ```bash
   git push origin your-feature-name
   ```
   Then open a PR targeting the `develop` branch.

## Important

- PRs to `main` will be rejected
- Always target `develop` for your pull requests
- Keep PRs focused on a single feature or fix

## Questions?

Open an issue or reach out at [noob@noobbooklm.com](mailto:noob@noobbooklm.com)
