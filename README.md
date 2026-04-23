# AI Plugins

A collection of Claude Code plugins for automating development workflows.

## Available Plugins

### jira-to-pr

Complete workflow automation from Jira task to GitHub pull request.

**Features:**
- Automatic Jira task fetching via `acli`
- Structured planning with approval gates
- Comprehensive review checklist
- DCO sign-off and AI attribution
- PR creation guidance

**Usage:**
```bash
/jira-to-pr TASK-ID
```

See [jira-to-pr documentation](plugins/jira-to-pr/README.md) for details.

## Installation

### Installing Individual Plugins

Copy the plugin folder to your Claude Code plugins directory:

```bash
# Install jira-to-pr plugin
cp -r plugins/jira-to-pr ~/.claude/plugins/

# Restart Claude Code or reload plugins
```

### Installing All Plugins

```bash
# Clone the repository
git clone https://github.com/ksimon1/ai-plugins.git
cd ai-plugins

# Copy all plugins
cp -r plugins/* ~/.claude/plugins/

# Restart Claude Code or reload plugins
```

## Plugin Structure

Each plugin follows the standard Claude Code plugin structure:

```
plugins/plugin-name/
├── .claude-plugin/
│   └── plugin.json         # Plugin metadata
├── commands/
│   └── command-name.md     # Command definitions
└── README.md               # Plugin documentation
```

## Prerequisites

Plugin-specific prerequisites are documented in each plugin's README.

## Contributing

Contributions are welcome! When adding new plugins:

1. Follow the standard plugin structure
2. Include comprehensive documentation
3. Add examples and usage instructions
4. Update this README with the new plugin

## License

Apache-2.0

## Author

Karel Simon
