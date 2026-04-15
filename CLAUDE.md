# Skills Repo

This repo is the source of truth for personal skills deployed to `~/.claude/skills/`.

## Workflow

1. **Always edit skills here first** — make changes in `/home/jjmr/github-repos/skills/<skill-name>/SKILL.md`
2. **Commit and get approval** — commit the change, review it
3. **Then copy to deployment** — copy the accepted file to `~/.claude/skills/<skill-name>/SKILL.md`

Never edit `~/.claude/skills/` directly. Changes made there are untracked and will be lost or overwritten.

## Deployment command

```bash
cp /home/jjmr/github-repos/skills/<skill-name>/SKILL.md ~/.claude/skills/<skill-name>/SKILL.md
```

## Structure

Each skill lives in its own directory:

```
skills/
  <skill-name>/
    SKILL.md    # Main skill file
```
