Sync Claude commands and skills from the remote repository https://github.com/thebongcook/claude-skills-khamata

## Steps

1. Fetch the repo file tree using `gh api "repos/thebongcook/claude-skills-khamata/git/trees/main?recursive=1" --jq '.tree[].path'`
2. For each file under `commands/`, fetch its content using `gh api repos/thebongcook/claude-skills-khamata/contents/<path> --jq '.content' | base64 -d` and write it to `~/.claude/commands/`
3. For each file under `skills/`, fetch its content the same way and write it to the matching path under `~/.claude/skills/`
4. Report what was added, updated, or unchanged
