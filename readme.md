# tmux-tools

> AI-powered tmux session management — naming, summaries, and more

<p align="center">
	<img src="media/demo.gif" width="600">
</p>

Stop guessing which session is which. `tmux-tools` reads the working directory, git branch, and recent terminal output from each session, sends it to a **local** LLM via [Ollama](https://ollama.com), and gives you descriptive names and summaries.

```
tmux-tools name
  async-calm-bear                     → ascentwow-quests
  claude-saadnaveed-233113            → saadbase-workout-api
  claude-saad-081854                  → homelab-deploy

tmux-tools summarize
  ascentwow-quests
    Built custom quest chain with Commander Lyanna NPC. Needs in-game coord tuning. Idle.

  saadbase-workout-api
    Refactoring workout service to use new API schema. In progress, tests passing.
```

## Install

```sh
curl -fsSL https://raw.githubusercontent.com/saadnvd1/tmux-tools/main/tmux-tools \
  -o /usr/local/bin/tmux-tools && chmod +x /usr/local/bin/tmux-tools
```

Or clone:

```sh
git clone https://github.com/saadnvd1/tmux-tools.git
cd tmux-tools
chmod +x tmux-tools
ln -s "$PWD/tmux-tools" /usr/local/bin/tmux-tools
```

### Requirements

- [tmux](https://github.com/tmux/tmux)
- [Ollama](https://ollama.com) running locally with any model pulled

## Usage

```sh
tmux-tools name                   # rename all sessions
tmux-tools name -d                # dry run — preview names
tmux-tools name my-session        # rename one session
tmux-tools summarize              # summarize all sessions
tmux-tools summarize my-session   # summarize one session
tmux-tools kill sess1 sess2       # kill specific sessions
tmux-tools list                   # list all sessions
```

### Commands

| Command | Alias | Description |
|---------|-------|-------------|
| `name` | `n` | Rename sessions with AI-generated descriptive names |
| `summarize` | `s` | Summarize what each session is doing |
| `kill` | `k` | Kill one or more sessions by name |
| `list` | `l` | List sessions with current names |

### Options

| Flag | Description | Default |
|------|-------------|---------|
| `-d, --dry-run` | Show proposed names without renaming | off |
| `-s, --skip-attached` | Skip the currently attached session | off |
| `-m, --model` | Ollama model to use | `gemma3:12b` |

## How it works

For each tmux session, it captures three pieces of context:
1. **Working directory** of the active pane
2. **Git branch** (if in a repo)
3. **Last 30-50 lines** of terminal output

This context is sent to a local Ollama model. For `name`, it generates a short kebab-case name. For `summarize`, it produces a 1-2 sentence description of what the session is doing and its current status. Everything runs on your machine — nothing leaves localhost.

## mobile-dev integration

If you use [mobile-dev](https://github.com/saadnvd1/mobile-dev), tmux-tools is available from the sessions menu:

- **s)** Summarize all sessions with AI
- **r)** Rename all sessions with AI (dry run first, confirm to apply)

## Tips

#### Use a smaller model for speed

```sh
tmux-tools name -m llama3.1:8b
```

#### Set a default model

```sh
export TMUX_TOOLS_MODEL=llama3.1:8b
```

#### Preview before renaming

```sh
tmux-tools name -d
```

## FAQ

#### Does this send my terminal output to the cloud?

No. Everything runs through Ollama on localhost. No data leaves your machine.

#### What if two sessions get the same name?

It detects conflicts and appends a short hash suffix to deduplicate.

#### What models work best?

Any Ollama model works. `gemma3:12b` gives the best names. `llama3.1:8b` is faster with slightly less descriptive results.

## Related

- [cc-teleport](https://github.com/saadnvd1/cc-teleport) - Wrap Claude Code sessions in tmux for device-hopping
- [cc-remote](https://github.com/saadnvd1/cc-remote) - Launch Claude Code on a remote VM from your local terminal
- [mobile-dev](https://github.com/saadnvd1/mobile-dev) - TUI menu for mobile SSH development

## License

MIT
