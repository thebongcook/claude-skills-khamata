# claude-skills-khamata

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A collection of custom commands and skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code), Anthropic's CLI tool.

## Commands

Commands are slash-command prompts you invoke with `/command-name` inside Claude Code.

| Command | Description | Usage |
|---------|-------------|-------|
| `commit-git` | Generate a commit message for staged changes | `/commit-git` |
| `commit-all-git` | Stage all changed files and generate a commit message | `/commit-all-git` |
| `translate-en-jp` | Translate an English technical document to Japanese | `/translate-en-jp <text>` |
| `translate-diff-en-jp` | Translate only recently changed sections (via git diff) to Japanese | `/translate-diff-en-jp <source> <target> <style>` |

## Skills

Skills are autonomous capabilities that Claude Code can invoke automatically when the context matches.

| Skill | Description | Trigger |
|-------|-------------|---------|
| `arxiv-pdf-lookup` | Download and read arXiv paper PDFs directly, with access to figures, tables, and exact layout | User shares an arXiv URL or paper ID |

## Installation

### Commands

Copy the command files to your Claude Code commands directory:

```bash
cp commands/*.md ~/.claude/commands/
```

### Skills

Copy each skill directory to your Claude Code skills directory:

```bash
cp -r skills/* ~/.claude/skills/
```

## What are Claude Code Custom Commands and Skills?

**Custom commands** are user-defined slash commands stored as Markdown files in `~/.claude/commands/`. When you type `/command-name` in Claude Code, the corresponding `.md` file is loaded as the prompt.

**Custom skills** are stored in `~/.claude/skills/<skill-name>/SKILL.md`. Each skill has a frontmatter block with `name` and `description`, and a body that tells Claude Code when and how to use the skill. Skills are triggered automatically based on context.

See the [Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code) for more details.

## License

MIT
