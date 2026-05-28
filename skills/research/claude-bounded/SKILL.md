---
name: claude-bounded
description: |
  Use Claude Code for bounded, low-risk tasks that don't require complex code work,
  logic inference, or project planning. Good fits: installing known tools/packages,
  downloading files or model weights, running diagnostics, drafting text, summarizing
  output, or getting a narrow second opinion via non-interactive prompt. Triggers on:
  use claude for this, claude subtask, bounded claude task, ask claude to install/download.
---


## Purpose

Use Claude Code for bounded tasks that are cheaper to hand off than to manage directly and easy for Codex to verify afterward. Good fits include known installs, known downloads including model weights, lightweight shell commands, remote SSH setup checks, quick summaries, small drafts, and narrow second opinions.

Codex remains responsible for deciding whether the task is safe, scoping the prompt, reviewing Claude's actions, and verifying the result.

## Preconditions

Before relying on Claude, confirm the CLI works in the current environment:

```bash
claude --version
claude -p 'Return exactly: CLAUDE_OK'
claude -p 'Run this harmless command and report only its stdout: printf CLAUDE_SHELL_OK'
```

If `claude -p` fails inside the sandbox with an API connection error or hangs with no output, rerun the same minimal check with escalated permissions and request a scoped approval for the prefix `["claude", "-p"]`.

## Permission Model

There are two permission layers:

1. Codex sandbox permission: running Claude may need escalation because Claude Code needs API/network access. Request a narrow approval for the command prefix being used, such as `["claude", "-p"]` or `["claude", "--permission-mode"]`.
2. Claude Code tool permission: choose how much autonomy Claude itself gets with `--permission-mode`, `--allowedTools`, `--disallowedTools`, and `--add-dir`.

For this user's workflow, bounded installs, downloads, and setup commands may use higher Claude permissions by default:

```bash
claude --permission-mode bypassPermissions -p '...'
```

Use this only for bounded tasks where the prompt clearly names the goal, working directory, forbidden actions, and expected verification. Codex must still review Claude's report and verify the result locally.

For tasks that touch important project files, package manifests, git state, or unknown install sources, prefer a constrained command instead:

```bash
claude --permission-mode auto --allowedTools 'Bash(npm install *)' -p '...'
claude --permission-mode auto --allowedTools 'Bash(curl *) Bash(unzip *)' -p '...'
claude --permission-mode auto --allowedTools 'Bash(python3 -m pip install *)' -p '...'
```

When Claude needs file access outside the current directory, add only the required directory:

```bash
claude --permission-mode auto --add-dir /path/to/needed/dir --allowedTools 'Bash(ls *) Bash(curl *)' -p '...'
```

Avoid `--dangerously-skip-permissions`; prefer `--permission-mode bypassPermissions` when higher Claude autonomy is desired.

## Good Uses

- Install a low-risk dependency or CLI tool when the package/source is known.
- Download specific known URLs, release artifacts, datasets, or model weights into an appropriate workspace path.
- Run a short diagnostic, formatting, version, or setup command.
- Run a bounded SSH subtask on a known server from the user's SSH config, especially remote installs, downloads, diagnostics, and environment setup.
- Ask for a concise second opinion on a small design or code question.
- Summarize a short file, diff, error, or log excerpt.
- Draft small text such as a PR note, issue reply, commit message, or checklist.
- Review a narrow code snippet for likely bugs or missing edge cases.
- Generate options for a small command, regex, SQL query, or shell one-liner, then verify before use.

## Avoid

- Do not send secrets, tokens, private keys, credentials, or sensitive personal data.
- Do not ask Claude to perform destructive actions such as deleting files, resetting git state, force-pushing, or overwriting important data.
- Do not ask Claude to install unknown software, run curl-piped-to-shell commands, or download from untrusted sources.
- Do not let Claude run long-lived remote jobs without `tmux`, logging, and a clear stop/check command.
- Do not delegate complex code work, broad refactors, multi-file implementation, or large codebase exploration through this skill.
- Do not delegate complex logic inference, architecture decisions, project-level planning, or open-ended problem solving through this skill.
- Do not treat Claude's output as authoritative. Verify facts, commands, and code before acting on them.

## SSH Remote Subtasks

Claude may use the same local SSH configuration as Codex for bounded remote sub-agent work. First confirm the Host resolves without logging in:

```bash
claude --permission-mode bypassPermissions -p 'Run only: ssh -G <host> | sed -n "1,25p". Do not connect.'
```

Then use a bounded remote command with SSH options that fail quickly:

```bash
claude --permission-mode bypassPermissions -p 'Run this bounded remote task: ssh -o BatchMode=yes -o ConnectTimeout=10 <host> "<remote command>". Report stdout, stderr, exit status, and any files changed.'
```

For remote installs or downloads, the prompt must include:

- target host and working directory
- exact package, URL, or tool name
- forbidden actions such as deleting files, changing git state, exposing secrets, or editing unrelated paths
- required verification command
- whether long-running work should be started in `tmux`

Codex should verify the remote result independently with a direct SSH check after Claude reports completion.

## Workflow

1. Decide whether the task is bounded enough for a single non-interactive prompt and does not require complex code work, complex logic inference, or project-level planning.
2. Build a compact prompt with the goal, allowed paths, allowed commands or command family, constraints, and requested output format.
3. For bounded install/download/setup use cases, run Claude with higher autonomy:

```bash
claude --permission-mode bypassPermissions -p 'Your concise prompt here'
```

4. Use `--permission-mode auto --allowedTools ...` instead when the task has a narrow, known command surface or touches important project state.
5. If no shell execution is needed, use plain `claude -p`.
6. For installs or downloads, require Claude to report exactly what it changed: commands run, files created, install location, and any errors.
7. If the prompt needs repository context, pass only the necessary snippets, paths, error messages, or summaries. Prefer not to paste large files.
8. Review the answer critically. For code, commands, dates, APIs, legal/medical/financial facts, or anything that affects user work, verify independently before using it.
9. Confirm the result locally with a minimal check such as `which`, `--version`, `ls`, checksum, import test, or the relevant project command.
10. Relay Claude's useful output to the user or apply it only after it passes your own review.

## Prompt Pattern

Use direct prompts with explicit boundaries:

```text
You are giving an advisory second opinion. Task: <task>.
Context: <small relevant context>.
Constraints: <constraints>.
Output: <format and length>.
Do not assume access to files not included here.
```

For execution tasks, constrain the command surface:

```text
Use Claude Code to perform this bounded setup task.
Goal: <install/download/run command>.
Allowed working directory: <path>.
Permission mode: bypassPermissions is allowed for this bounded task.
Allowed actions: <specific package manager, download URL, or command family, if known>.
Forbidden actions: deleting files, resetting git, editing unrelated files, exposing secrets, complex code work, complex logic inference, project-level planning.
Report: commands run, files changed, final verification output.
Stop and explain if the task needs broader permissions.
```

For SSH tasks:

```text
Use Claude Code as a remote sub-agent for a bounded SSH task.
Host: <ssh config host>.
Remote working directory: <remote path>.
Goal: <small install/download/diagnostic/setup task>.
Use: ssh -o BatchMode=yes -o ConnectTimeout=10 <host> '<command>'.
Forbidden actions: deleting files, resetting git, editing unrelated files, exposing secrets.
For long-running commands, use tmux and report the session name plus log path.
Report: commands run, stdout/stderr summary, files changed, verification output.
```

For code review style tasks, ask for findings first:

```text
Review this small snippet for correctness bugs only. Return findings first, with severity and line references when possible. If no issues, say so.
```

## Reporting

When Claude performed or influenced the result, be transparent in the final answer: mention that Claude was used, summarize what it did, and include Codex's verification result.
