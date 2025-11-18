# First Contributions Guide

**How to make your first contribution to Open WebUI.**

**Documented:** November 18, 2025

---

## 🎯 Why Contribute?

Contributing to Open WebUI helps you:
- Build real-world experience with modern technologies
- Join a supportive developer community
- Improve software used by thousands of users
- Strengthen your portfolio

**No contribution is too small!** Documentation fixes, bug reports, and small features are all valuable.

---

## 🚀 Quick Start Checklist

- [ ] Fork the repository
- [ ] Set up development environment ([GETTING_STARTED.md](./GETTING_STARTED.md))
- [ ] Find a good first issue
- [ ] Create a feature branch
- [ ] Make your changes
- [ ] Write tests
- [ ] Submit pull request

---

## 1️⃣ Fork and Clone

### Fork the Repository

1. Go to https://github.com/open-webui/open-webui
2. Click **Fork** in the top-right
3. Clone your fork:

```bash
git clone https://github.com/YOUR_USERNAME/open-webui.git
cd open-webui
```

### Add Upstream Remote

```bash
git remote add upstream https://github.com/open-webui/open-webui.git
git remote -v
# Should show:
# origin    https://github.com/YOUR_USERNAME/open-webui.git (fetch)
# upstream  https://github.com/open-webui/open-webui.git (fetch)
```

### Keep Your Fork Updated

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## 2️⃣ Find a Good First Issue

### Where to Look

1. **GitHub Issues:** https://github.com/open-webui/open-webui/issues
   - Filter by label: `good first issue`
   - Filter by label: `help wanted`
   - Filter by label: `documentation`

2. **Ask in Discord:** https://discord.gg/open-webui
   - #contributors channel
   - Ask maintainers for guidance

### Types of Good First Issues

✅ **Documentation:**
- Fix typos or unclear explanations
- Add code examples
- Improve README.md

✅ **UI Improvements:**
- Accessibility fixes
- Responsive design tweaks
- Icon updates

✅ **Small Features:**
- Add confirmation dialogs
- Implement tooltips
- Add keyboard shortcuts

✅ **Bug Fixes:**
- Fix console errors
- Resolve edge cases
- Improve error messages

---

## 3️⃣ Development Workflow

### Create a Feature Branch

```bash
git checkout main
git pull upstream main
git checkout -b feature/your-feature-name
```

**Branch naming:**
- `feature/add-dark-mode`
- `fix/chat-scroll-bug`
- `docs/update-api-guide`

### Make Your Changes

1. **Read related code** to understand patterns
2. **Follow conventions** in [PATTERNS_AND_CONVENTIONS.md](./PATTERNS_AND_CONVENTIONS.md)
3. **Test thoroughly** before committing
4. **Write clear commit messages**

**Example commit messages:**
```bash
git commit -m "feat: add character counter to chat input"
git commit -m "fix: resolve chat scroll position bug"
git commit -m "docs: update API authentication section"
```

**Commit message format:**
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `style:` - Formatting, whitespace
- `refactor:` - Code restructuring
- `test:` - Adding tests
- `chore:` - Build tasks, dependencies

### Run Tests

**Frontend:**
```bash
npm run test
npm run lint
```

**Backend:**
```bash
cd backend
pytest
ruff check .
```

**E2E (if applicable):**
```bash
npm run cy:run
```

### Push Your Changes

```bash
git push origin feature/your-feature-name
```

---

## 4️⃣ Submit a Pull Request

### Create PR on GitHub

1. Go to your fork: https://github.com/YOUR_USERNAME/open-webui
2. Click **Compare & pull request**
3. Fill out the PR template:

```markdown
## Description
Brief summary of what you changed and why.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Code refactoring

## Testing
- [ ] Tested locally
- [ ] Added unit tests
- [ ] All tests pass

## Screenshots (if UI change)
[Attach before/after screenshots]

## Related Issue
Closes #123
```

### PR Best Practices

✅ **Keep it focused:** One feature/fix per PR
✅ **Write clear descriptions:** Explain what and why
✅ **Add screenshots:** For visual changes
✅ **Link issues:** Use "Closes #123" to auto-close
✅ **Respond to feedback:** Be open to suggestions
✅ **Update if requested:** Make changes promptly

---

## 5️⃣ Code Review Process

### What to Expect

1. **Automated checks:** CI/CD runs tests
2. **Maintainer review:** Within 1-3 days
3. **Feedback:** Suggestions for improvements
4. **Approval:** Once ready, PR is merged

### Responding to Feedback

```bash
# Make requested changes
git add .
git commit -m "fix: address review feedback"
git push origin feature/your-feature-name
```

**Tips:**
- Don't take feedback personally - it's about code quality
- Ask questions if feedback is unclear
- Thank reviewers for their time
- Learn from suggestions for future PRs

---

## 🎯 Example First Contributions

### Example 1: Fix Typo in Documentation

**Issue:** README.md has typo in installation instructions

**Steps:**
```bash
# 1. Create branch
git checkout -b docs/fix-readme-typo

# 2. Edit file
# Change "Instal dependencies" → "Install dependencies"

# 3. Commit
git add README.md
git commit -m "docs: fix typo in installation section"

# 4. Push and create PR
git push origin docs/fix-readme-typo
```

---

### Example 2: Add Confirmation Dialog

**Issue:** Chat deletion should require confirmation

**Steps:**

1. **Create branch:**
```bash
git checkout -b feature/add-delete-confirmation
```

2. **Add confirmation in frontend:**
```svelte
<!-- src/lib/components/chat/ChatItem.svelte -->
<script lang="ts">
  async function handleDelete() {
    const confirmed = confirm('Are you sure you want to delete this chat?');
    if (!confirmed) return;

    await deleteChat(chat.id);
  }
</script>

<button onclick={handleDelete}>Delete</button>
```

3. **Test manually:**
- Click delete button
- Verify confirmation appears
- Test both "OK" and "Cancel"

4. **Commit and push:**
```bash
git add src/lib/components/chat/ChatItem.svelte
git commit -m "feat: add confirmation dialog for chat deletion"
git push origin feature/add-delete-confirmation
```

5. **Create PR with screenshot**

---

### Example 3: Add Unit Test

**Issue:** `formatRelativeTime` function needs tests

**Steps:**

1. **Create test file:**
```typescript
// src/lib/utils/formatRelativeTime.test.ts
import { describe, it, expect } from 'vitest';
import { formatRelativeTime } from './formatRelativeTime';

describe('formatRelativeTime', () => {
  it('shows seconds for recent timestamps', () => {
    const now = Math.floor(Date.now() / 1000);
    expect(formatRelativeTime(now - 30)).toContain('seconds ago');
  });

  it('shows minutes for older timestamps', () => {
    const now = Math.floor(Date.now() / 1000);
    expect(formatRelativeTime(now - 120)).toContain('2 minutes ago');
  });

  it('shows hours for very old timestamps', () => {
    const now = Math.floor(Date.now() / 1000);
    expect(formatRelativeTime(now - 7200)).toContain('2 hours ago');
  });
});
```

2. **Run tests:**
```bash
npm run test
```

3. **Commit:**
```bash
git add src/lib/utils/formatRelativeTime.test.ts
git commit -m "test: add unit tests for formatRelativeTime"
git push origin test/format-relative-time
```

---

## 🐛 Reporting Bugs

If you find a bug but don't know how to fix it, report it!

### Bug Report Template

```markdown
## Description
Clear description of the bug.

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. See error

## Expected Behavior
What should happen.

## Actual Behavior
What actually happens.

## Screenshots
[If applicable]

## Environment
- OS: [e.g., macOS 14.0]
- Browser: [e.g., Chrome 120]
- Open WebUI Version: [e.g., 0.3.0]

## Additional Context
Any other relevant information.
```

---

## 💡 Suggesting Features

### Feature Request Template

```markdown
## Problem
Describe the problem or need.

## Proposed Solution
How would you solve it?

## Alternatives Considered
Other approaches you thought about.

## Additional Context
Mockups, examples, references.
```

---

## 🎓 Learning Resources

### Open WebUI Docs

- [GETTING_STARTED.md](./GETTING_STARTED.md) - Development setup
- [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) - System design
- [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md) - Common tasks
- [PATTERNS_AND_CONVENTIONS.md](./PATTERNS_AND_CONVENTIONS.md) - Code style

### Technology Guides

- **Svelte 5:** https://svelte.dev/docs/svelte/overview
- **SvelteKit:** https://svelte.dev/docs/kit/introduction
- **FastAPI:** https://fastapi.tiangolo.com/
- **SQLAlchemy:** https://docs.sqlalchemy.org/en/20/

### Community

- **Discord:** https://discord.gg/open-webui
- **GitHub Discussions:** https://github.com/open-webui/open-webui/discussions
- **Twitter:** @openwebui

---

## 🏆 Recognition

Contributors are recognized in:
- GitHub Contributors page
- Release notes
- Community shoutouts

**Top contributors may receive:**
- Contributor role in Discord
- Early access to features
- Input on roadmap decisions

---

## ❓ Common Questions

### Q: I'm new to open source. Where do I start?

**A:** Start with documentation fixes or small UI improvements. These are low-risk and help you learn the workflow.

### Q: My PR was rejected. What now?

**A:** Don't be discouraged! Ask for clarification, learn from feedback, and try again. Every contributor gets PRs rejected sometimes.

### Q: How long does review take?

**A:** Usually 1-3 days, but it depends on maintainer availability. Ping in Discord if no response after a week.

### Q: Can I work on an issue someone else is assigned to?

**A:** No, unless it's been abandoned (no activity for 2+ weeks). Ask first before working on assigned issues.

### Q: Should I ask before starting work?

**A:** For small fixes, no. For large features, yes! Comment on the issue or ask in Discord to avoid duplicate work.

### Q: I broke something. Help!

**A:** No worries! Ask in Discord #help channel. Everyone breaks things when learning.

---

## 🎉 Your First PR Checklist

Before submitting:
- [ ] Code follows project conventions
- [ ] Tests pass locally
- [ ] Linting passes
- [ ] Commit messages are clear
- [ ] Branch is up to date with `main`
- [ ] PR description is complete
- [ ] Screenshots added (if UI change)

After submitting:
- [ ] Respond to CI failures
- [ ] Address review feedback
- [ ] Thank reviewers
- [ ] Celebrate! 🎊

---

## 🚀 Beyond Your First PR

After your first successful contribution:

1. **Tackle bigger issues** - Try intermediate difficulty
2. **Review others' PRs** - Learn by reading code
3. **Help newcomers** - Answer questions in Discord
4. **Propose features** - Share your ideas
5. **Become a regular contributor** - Build your impact

---

**Welcome to the Open WebUI community! We can't wait to see your contributions.** 🌟

---

**Last updated:** November 18, 2025
