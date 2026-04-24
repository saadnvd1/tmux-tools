# tmux-namer

> AI-powered descriptive naming for your tmux sessions

<p align="center">
	<img src="media/demo.gif" width="600">
</p>

Stop guessing which session is which. `tmux-namer` reads the working directory, git branch, and recent terminal output from each session, sends it to a **local** LLM via [Ollama](https://ollama.com), and renames sessions to something actually useful.

```
async-calm-bear                     → ascentwow-quests
claude-saadnaveed-233113            → saadbase-workout-api
claude-saad-081854                  → homelab-deploy
```

## Install

```sh
curl -fsSL https://raw.githubusercontent.com/saadnvd1/tmux-namer/main/tmux-namer -o /usr/local/bin/tmux-namer && chmod +x /usr/local/bin/tmux-namer
```

Or clone:

```sh
git clone https://github.com/saadnvd1/tmux-namer.git
cd tmux-namer
chmod +x tmux-namer
ln -s "$PWD/tmux-namer" /usr/local/bin/tmux-namer
```

### Requirements

- [tmux](https://github.com/tmux/tmux)
- [Ollama](https://ollama.com) running locally with any model pulled

## Usage

```sh
tmux-namer                  # rename all sessions
tmux-namer -d               # dry run — preview names without renaming
tmux-namer my-session       # rename one specific session
tmux-namer -s               # skip the currently attached session
```

### Options

| Flag | Description | Default |
|------|-------------|---------|
| `-d, --dry-run` | Show proposed names without renaming | off |
| `-s, --skip-attached` | Skip the currently attached session | off |
| `-m, --model` | Ollama model to use | `gemma3:12b` |
| `-l, --list` | List sessions with current names | — |

## How it works

For each tmux session, it captures three pieces of context:
1. **Working directory** of the active pane
2. **Git branch** (if in a repo)
3. **Last 30 lines** of terminal output

This context is sent to a local Ollama model with instructions to generate a short, descriptive kebab-case name. The model runs entirely on your machine — nothing leaves localhost.

## Tips

#### Use a smaller model for speed

```sh
tmux-namer -m llama3.1:8b
```

Smaller models are faster (~2s vs ~5s per session) and still produce good names.

#### Set a default model

```sh
export TMUX_NAMER_MODEL=llama3.1:8b
```

#### Preview before renaming

Always run with `-d` first if you want to see what it'll do:

```sh
tmux-namer -d
```

## FAQ

#### Does this send my terminal output to the cloud?

No. Everything runs through Ollama on localhost. No data leaves your machine.

#### What if two sessions get the same name?

It detects conflicts and appends a short hash suffix to deduplicate.

#### What models work best?

Any Ollama model works. `gemma3:12b` gives the best names. `llama3.1:8b` is faster with slightly less descriptive results.

#### Can I undo a rename?

tmux sessions are just names — rename them back with `tmux rename-session -t new-name old-name`.

## Related

- [cc-teleport](https://github.com/saadnvd1/cc-teleport) - Wrap Claude Code sessions in tmux for device-hopping
- [cc-remote](https://github.com/saadnvd1/cc-remote) - Launch Claude Code on a remote VM from your local terminal

## License

MIT
