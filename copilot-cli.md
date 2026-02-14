# github/copilot-cli
```
╭──────────────────────────────────────────────────────────────────────────────────────────────────╮
│  ╭─╮╭─╮                                                                                          │
│  ╰─╯╰─╯  GitHub Copilot v0.0.410                                                                 │
│  █ ▘▝ █  Describe a task to get started.                                                         │
│   ▔▔▔▔                                                                                           │
│  Pick a model with /model. Send this session to GitHub with /delegate, and Copilot will create   │
│  a PR. Copilot uses AI, so always check for mistakes.                                            │
╰──────────────────────────────────────────────────────────────────────────────────────────────────╯
```

## How to

### Start interactive mode

```
$ copilot
```

### Start interactive mode and automatically execute a prompt
```
$ copilot -i "Fix the bug in main.js"
```

### Execute a prompt in non-interactive mode (exits after completion)

```
$ copilot -p "Fix the bug in main.js" --allow-all-tools
```

### shotcut/prefix

- `@`: エージェントに伝えたいファイルを指定する
- `!`: エージェントを介さずにローカルシェルでコマンドを実行する
- `&`: Coding Agentに委任する(GitHub Actionに送る)

### Enable all permissions with a single flag
## help

```
» copilot help
```

Usage:
```
Usage: copilot [options] [command]

GitHub Copilot CLI - An AI-powered coding assistant

Options:
  --acp                               Start as Agent Client Protocol server
  --add-dir <directory>               Add a directory to the allowed list for file access (can be used multiple times)
  --add-github-mcp-tool <tool>        Add a tool to enable for the GitHub MCP server instead of the default CLI subset
                                      (can be used multiple times). Use "*" for all tools.
  --add-github-mcp-toolset <toolset>  Add a toolset to enable for the GitHub MCP server instead of the default CLI
                                      subset (can be used multiple times). Use "all" for all toolsets.
  --additional-mcp-config <json>      Additional MCP servers configuration as JSON string or file path (prefix with @)
                                      (can be used multiple times; augments config from ~/.copilot/mcp-config.json for
                                      this session)
  --agent <agent>                     Specify a custom agent to use
  --allow-all                         Enable all permissions (equivalent to --allow-all-tools --allow-all-paths
                                      --allow-all-urls)
  --allow-all-paths                   Disable file path verification and allow access to any path
  --allow-all-tools                   Allow all tools to run automatically without confirmation; required for
                                      non-interactive mode (env: COPILOT_ALLOW_ALL)
  --allow-all-urls                    Allow access to all URLs without confirmation
  --allow-tool [tools...]             Tools the CLI has permission to use; will not prompt for permission
  --allow-url [urls...]               Allow access to specific URLs or domains
  --alt-screen                        Use the terminal alternate screen buffer
  --available-tools [tools...]        Only these tools will be available to the model
  --banner                            Show the startup banner
  --config-dir <directory>            Set the configuration directory (default: ~/.copilot)
  --continue                          Resume the most recent session
  --deny-tool [tools...]              Tools the CLI does not have permission to use; will not prompt for permission
  --deny-url [urls...]                Deny access to specific URLs or domains, takes precedence over --allow-url
  --disable-builtin-mcps              Disable all built-in MCP servers (currently: github-mcp-server)
  --disable-mcp-server <server-name>  Disable a specific MCP server (can be used multiple times)
  --disable-parallel-tools-execution  Disable parallel execution of tools (LLM can still make parallel tool calls, but
                                      they will be executed sequentially)
  --disallow-temp-dir                 Prevent automatic access to the system temporary directory
  --enable-all-github-mcp-tools       Enable all GitHub MCP server tools instead of the default CLI subset. Overrides
                                      --add-github-mcp-toolset and --add-github-mcp-tool options.
  --excluded-tools [tools...]         These tools will not be available to the model
  --experimental                      Enable experimental features
  -h, --help                          display help for command
  -i, --interactive <prompt>          Start interactive mode and automatically execute this prompt
  --log-dir <directory>               Set log file directory (default: ~/.copilot/logs/)
  --log-level <level>                 Set the log level (choices: "none", "error", "warning", "info", "debug", "all",
                                      "default")
  --model <model>                     Set the AI model to use (choices: "claude-sonnet-4.5", "claude-haiku-4.5",
                                      "claude-opus-4.6", "claude-opus-4.6-fast", "claude-opus-4.5", "claude-sonnet-4",
                                      "gemini-3-pro-preview", "gpt-5.3-codex", "gpt-5.2-codex", "gpt-5.2",
                                      "gpt-5.1-codex-max", "gpt-5.1-codex", "gpt-5.1", "gpt-5", "gpt-5.1-codex-mini",
                                      "gpt-5-mini", "gpt-4.1")
  --no-alt-screen                     Disable the terminal alternate screen buffer
  --no-ask-user                       Disable the ask_user tool (agent works autonomously without asking questions)
  --no-auto-update                    Disable downloading CLI update automatically (disabled by default in CI
                                      environments)
  --no-color                          Disable all color output
  --no-custom-instructions            Disable loading of custom instructions from AGENTS.md and related files
  --no-experimental                   Disable experimental features
  -p, --prompt <text>                 Execute a prompt in non-interactive mode (exits after completion)
  --plain-diff                        Disable rich diff rendering (syntax highlighting via diff tool specified by git
                                      config)
  --resume [sessionId]                Resume from a previous session (optionally specify session by ID, or start a new
                                      session with a specific UUID
  -s, --silent                        Output only the agent response (no stats), useful for scripting with -p
  --screen-reader                     Enable screen reader optimizations
  --share [path]                      Share session to markdown file after completion in non-interactive mode (default:
                                      ./copilot-session-<id>.md)
  --share-gist                        Share session to a secret GitHub gist after completion in non-interactive mode
  --stream <mode>                     Enable or disable streaming mode (choices: "on", "off")
  -v, --version                       show version information
  --yolo                              Enable all permissions (equivalent to --allow-all-tools --allow-all-paths
                                      --allow-all-urls)

Commands:
  login [options]                     Authenticate with Copilot via OAuth device flow
  help [topic]                        Display help information
  init                                Initialize Copilot instructions for this repository
  update                              Download the latest version
  version                             Display version information and check for updates
  plugin                              Manage plugins and plugin marketplaces

Help Topics:
  config       Configuration Settings
  commands     Interactive Mode Commands
  environment  Environment Variables
  logging      Logging
  permissions  Permissions

Examples:
  # Start interactive mode
  $ copilot

  # Start interactive mode and automatically execute a prompt
  $ copilot -i "Fix the bug in main.js"

  # Execute a prompt in non-interactive mode (exits after completion)
  $ copilot -p "Fix the bug in main.js" --allow-all-tools

  # Enable all permissions with a single flag
  $ copilot -p "Fix the bug in main.js" --allow-all
  $ copilot -p "Fix the bug in main.js" --yolo

  # Start with a specific model
  $ copilot --model gpt-5

  # Resume the most recent session
  $ copilot --continue

  # Resume a previous session using session picker
  $ copilot --resume

  # Resume a specific session by ID
  $ copilot --resume <session-id>

  # Start a new session with a specific UUID
  $ copilot --resume 0cb916db-26aa-40f2-86b5-1ba81b225fd2

  # Resume with auto-approval
  $ copilot --allow-all-tools --resume

  # Allow access to additional directory
  $ copilot --add-dir /home/user/projects

  # Allow multiple directories
  $ copilot --add-dir ~/workspace --add-dir /tmp

  # Disable path verification (allow access to any path)
  $ copilot --allow-all-paths

  # Allow all git commands except git push
  $ copilot --allow-tool 'shell(git:*)' --deny-tool 'shell(git push)'

  # Allow all file editing
  $ copilot --allow-tool 'write'

  # Allow all but one specific tool from MCP server with name "MyMCP"
  $ copilot --deny-tool 'MyMCP(denied_tool)' --allow-tool 'MyMCP'

  # Allow GitHub API access (defaults to HTTPS)
  $ copilot --allow-url github.com

  # Deny access to specific domain over HTTPS
  $ copilot --deny-url https://malicious-site.com
  $ copilot --deny-url malicious-site.com

  # Allow all URLs without confirmation
  $ copilot --allow-all-urls

  # Initialize Copilot instructions for a repository
  $ copilot init
```

## Environment Variables

```
» copilot help environment
```

Environment Variables:

  `COLORFGBG`: fallback to detect dark / light backgrounds; uses form of "fg;bg" where each field is a number from 0-15 corresponding to ANSI colors.

  `COPILOT_ALLOW_ALL`: allow all tools to run automatically without confirmation when set to "true".

  `COPILOT_AUTO_UPDATE`: set to "false" to disable downloading updated CLI versions automatically. Can also be disabled with the --no-auto-update flag. Auto-update is enabled by default, except in CI environments (detected via `CI`, `BUILD_NUMBER`, `RUN_ID`, or `SYSTEM_COLLECTIONURI` environment variables), where it is disabled by default.

  `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`: comma-separated list of additional directories to search for custom instructions files (in addition to git root and current working directory).

  `COPILOT_MODEL`: optionally set the agent model. Can be overridden by the --model command line option or the /model command.

  `COPILOT_GITHUB_TOKEN`, `GH_TOKEN`, `GITHUB_TOKEN` (in order of precedence): an authentication token that takes precedence over previously stored credentials.

  `USE_BUILTIN_RIPGREP`: when set to "false", uses the ripgrep binary from PATH instead of the bundled version.

  `PLAIN_DIFF`: when set to "true", disables rich diff rendering (syntax highlighting via diff tool specified by git config). Can also be set with the --plain-diff flag.

  `XDG_CONFIG_HOME`: override the directory where configuration files are stored; defaults to `$HOME/.copilot`.

  `XDG_STATE_HOME`: override the directory where state files are stored; defaults to `$HOME/.copilot`.

## Configuration Settings

```
» copilot help config
```

Configuration Settings:

  `allowed_urls`: list of URLs or domains that are allowed to be accessed without prompting.
    Supports exact URLs (e.g., "https://github.com"), domain patterns (e.g., "github.com"),
    and wildcard subdomains (e.g., "*.github.com").

  `alt_screen`: whether to use the terminal alternate screen buffer; defaults to `false`.
  - Can also be enabled with the --alt-screen flag or disabled with the --no-alt-screen flag
  - Using either flag persists the preference to config

  `auto_update`: whether to automatically download updated CLI versions; defaults to `true`.

  `banner`: frequency of showing animated banner; defaults to "once".
  - "always" displays it every time
  - "never" disables it
  - "once" displays it the first time

  `beep`: whether to beep when user attention is required; defaults to `true`.

  `compact_paste`: whether to collapse large pasted content into compact tokens; defaults to `true`.
    - When `true` (default), pastes with more than 10 lines are displayed as `[Paste #N - X lines]`
    - When `false`, all pasted content is displayed directly in the input

  `custom_agents.default_local_only`: whether to default to only use local custom agents (no remote org/enterprise); defaults to `false`.

  `denied_urls`: list of URLs or domains that are denied access. Denial rules take precedence
    over allow rules.

  `experimental`: whether to enable experimental features; defaults to `false`.
    - Can be enabled with --experimental flag or /experimental command

  `launch_messages`: list of messages to display in the banner on startup.
  - One message is randomly selected each time the CLI starts
  - Useful for team announcements or reminders

  `log_level`: log level for CLI; defaults to "default". Set to "all" for debug logging.

  `model`: AI model to use for Copilot CLI; can be changed with /model command or --model flag option.
  - "claude-sonnet-4.5"
  - "claude-haiku-4.5"
  - "claude-opus-4.6"
  - "claude-opus-4.6-fast"
  - "claude-opus-4.5"
  - "claude-sonnet-4"
  - "gemini-3-pro-preview"
  - "gpt-5.3-codex"
  - "gpt-5.2-codex"
  - "gpt-5.2"
  - "gpt-5.1-codex-max"
  - "gpt-5.1-codex"
  - "gpt-5.1"
  - "gpt-5"
  - "gpt-5.1-codex-mini"
  - "gpt-5-mini"
  - "gpt-4.1"

  `streamer_mode`: whether to enable streamer mode; defaults to `false`.
    - Hides preview model names and quota details (useful for streaming or screen sharing)
    - Can be toggled with /streamer-mode command; persists across sessions

  `parallel_tool_execution`: whether to enable parallel execution of tools; defaults to `true`.
    - Can be disabled with --disable-parallel-tools-execution flag

  `render_markdown`: whether to render markdown in the terminal; defaults to `true`.

  `screen_reader`: whether to enable screen reader optimizations; defaults to `false`.

  `stream`: whether to enable streaming mode; defaults to `true`.

  `theme`: theme to color and stylize output; defaults to "auto".
  - "auto" detects terminal background and decides based on luminosity
  - "dark" uses contrasting ANSI colors for dark backgrounds
  - "light" uses contrasting ANSI colors for light backgrounds

  `trusted_folders`: list of folders where permission to read or execute files has been granted.

  `undo_without_confirmation` (experimental): whether to skip the undo confirmation when pressing Esc Esc; defaults to `false`.
  - When `true`, double-escape will immediately undo to the previous snapshot

  `update_terminal_title`: whether to update the terminal tab/window title with current intent; defaults to `true`.
  - Shows what the agent is currently working on in the terminal title bar
  - Only works on supported terminal emulators (xterm, iTerm, Windows Terminal, etc.)

### ~/.copilot/config.json:
```json
{
  "theme": "auto",
  "banner": "never",
  "beep": true,
  "log_level": "all",
  "model": "gpt-5-mini"
}
```

## Log Levels

```
» copilot help logging
```

Log Levels:
```
  none     No logging output
  error    Only error messages
  warning  Error and warning messages
  info     Error, warning, and info messages
  debug    All messages including debug
  all      Same as debug
  default  Error, warning, and info messages (same as info)

Examples:
  # Set logging to ./logs
  $ copilot --log-dir ./logs

  # Enable debug level logging
  $ copilot --log-level debug
```

## Tool Permissions

```
» copilot help permissions
```

Tool Permissions:
```
Permissions:

  The GitHub Copilot CLI provides granular control over tool, URL, and path permissions to ensure safe
  operation. By default, the CLI restricts certain actions and prompts for user confirmation when necessary.
  You can customize these permissions using the available command line options.

  Tool Permissions:

  Tool availability is controlled via the --available-tools and --excluded-tools options. The --available-tools option
  disables all other tools; the --excluded-tools option disables only the specified tools. These filters decide which
  tools the model can see.

  Tool permissions are managed via --allow-tool, --deny-tool, and --allow-all-tools. Denial rules always take
  precedence over allow rules, even --allow-all-tools. These flags control approval prompts and do not expose tools
  that were filtered out by --available-tools/--excluded-tools.

  The --allow-tool and --deny-tool parameters take a permission pattern of form kind(argument) where the argument
  is optional and dependent on the kind. The following kinds are supported:

    shell(command:*?)
        Exactly matches a specific shell command to be run. If the command is omitted, all shell commands are allowed.
        In most cases, this will be an exact match against the command name. To match prefixes, use the :* suffix.
        This is particularly useful for git and gh commands, where approval is performed on a first-level subcommand
        basis, e.g. "git push" or "gh pr create". To match all git commands, use "shell(git:*)". Wildcard matching is
        performed on the stem of the command, so "shell(git:*)" will match "git push" but not "gitea".

    write
        Matches tools that create and modify files, except shell tool invocations. To allow all shell redirections,
        set --allow-all-tools.

    <mcp-server-name>(tool-name?)
        Exactly matches a specific tool from a specific MCP server, or all tools from that server if omitted.
        The server name is the name of the MCP server as configured in your environment (e.g. "MyMCP").
        The tool name is the name of the tool as registered with the MCP server (e.g. "my_tool").

    url(domain-or-url?)
        Matches URL access requests. Applies to the shell and web-fetch tools. If omitted, matches all URLs.
        Supports exact URLs: "url(https://github.com)"
        Supports protocol + domain patterns: "url(https://github.com)" or "url(https://*.github.com)"
        Note: All patterns are protocol-aware. Domains without a protocol default to https://.

  URL Permissions:

  The --allow-url and --deny-url parameters accept URLs or protocol + domain patterns.
  All URL permissions are protocol-aware - approving https://example.com does NOT allow http://example.com.
  Patterns without an explicit protocol default to https://.

    # Allow GitHub API access via HTTPS (default)
    $ copilot --allow-url github.com

    # Allow HTTP access explicitly
    $ copilot --allow-url http://example.com

    # Deny access to specific domain over HTTPS
    $ copilot --deny-url https://malicious-site.com

    # Allow all URLs
    $ copilot --allow-all-urls

  Path Permissions:

  By default, file access is restricted to paths within the current working directory and its subdirectories,
  plus the system temporary directory. The --allow-all-paths flag disables this verification and allows access
  to any path on the filesystem. The --disallow-temp-dir flag prevents automatic access to the system temporary
  directory.

    # Allow access to any path on the filesystem
    $ copilot --allow-all-paths

    # Prevent automatic access to the system temporary directory
    $ copilot --disallow-temp-dir

  Enabling All Permissions:

  The --allow-all and --yolo flags are equivalent shortcuts that enable all permissions at once. They are
  equivalent to specifying --allow-all-tools --allow-all-paths --allow-all-urls together. These flags are
  useful for non-interactive scripts or when you want to run without any confirmation prompts.

    # Enable all permissions with a single flag
    $ copilot --allow-all
    $ copilot --yolo

    # Equivalent to:
    $ copilot --allow-all-tools --allow-all-paths --allow-all-urls

  It is expected that these permissions will be extended in the very near future to support wildcard matching and other richer functionality.
  ```

## Interactive Mode Commands

```
» copilot help commands
```

Interactive Mode Commands:
```
  /add-dir <directory>                                           Add a directory to the allowed list for file access
  /agent                                                         Browse and select from available agents (if any)
  /allow-all, /yolo                                              Enable all permissions (tools, paths, and URLs)
  /changelog [version]                                           Display changelog for CLI versions
  /clear, /new                                                   Clear the conversation history
  /compact                                                       Summarize conversation history to reduce context window usage
  /context                                                       Show context window token usage and visualization
  /cwd, /cd [directory]                                          Change working directory or show current directory
  /diff                                                          Review the changes made in the current directory
  /exit, /quit                                                   Exit the CLI
  /experimental [on|off|show]                                    Show available experimental features, or enable/disable experimental mode
  /share [file|gist] [path]                                      Share session to markdown file or GitHub gist
  /feedback                                                      Provide feedback about the CLI
  /help                                                          Show help for interactive commands
  /ide                                                           Connect to an IDE workspace
  /init [suppress]                                               Initialize Copilot instructions for this repository, or suppress the init suggestion
  /instructions                                                  View and toggle custom instruction files
  /list-dirs                                                     Display all allowed directories for file access
  /login                                                         Log in to Copilot
  /logout                                                        Log out of Copilot
  /mcp [show|add|edit|delete|disable|enable] [server-name]       Manage MCP server configuration
  /model, /models [model]                                        Select AI model to use
  /plan [prompt]                                                 Create an implementation plan before coding
  /plugin [marketplace|install|uninstall|update|list] [args...]  Manage plugins and plugin marketplaces
  /rename <name>                                                 Rename the current session (alias for /session rename)
  /reset-allowed-tools                                           Reset the list of allowed tools
  /resume [sessionId]                                            Switch to a different session (optionally specify session ID)
  /review [prompt]                                               Run code review agent to analyze changes
  /session [checkpoints [n]|files|plan|rename <name>]            Show session info and workspace summary. Use subcommands for details.
  /skills [list|info|add|remove|reload] [args...]                Manage skills for enhanced capabilities
  /streamer-mode, /on-air                                        Toggle streamer mode (hides preview model names and quota details for streaming)
  /tasks                                                         View and manage background tasks (subagents and shell sessions)
  /terminal-setup                                                Configure terminal for multiline input support (shift+enter and ctrl+enter)
  /theme [show|set|list] [auto|<theme-id>]                       View or configure terminal theme
  /usage                                                         Display session usage metrics and statistics
  /user [show|list|switch]                                       Manage GitHub user list
```

## Models

Select Model:
```
Choose the AI model to use for Copilot CLI. The selected model will be persisted and used for future sessions.

Some models are not available. For information on Copilot policies and subscription, visit:
https://github.com/settings/copilot/features

 Search models...

❯ Claude Sonnet 4.5 (default) ✓                                      1x
  Claude Haiku 4.5                                                0.33x
  Claude Opus 4.6                                                    3x
  Claude Opus 4.6 (fast mode) (Preview) (requires enablement)        9x
  Claude Opus 4.5                                                    3x
  Claude Sonnet 4                                                    1x
  Gemini 3 Pro (Preview)                                             1x
  GPT-5.3-Codex                                                      1x
  GPT-5.2-Codex                                                      1x
  GPT-5.2                                                            1x
  GPT-5.1-Codex-Max                                                  1x
  GPT-5.1-Codex                                                      1x
  GPT-5.1                                                            1x
  GPT-5                                                              1x
  GPT-5.1-Codex-Mini (Preview)                                    0.33x
  GPT-5 mini                                                         0x
  GPT-4.1                                                            0x

↑↓ to navigate · Enter to select · Esc to cancel
```
