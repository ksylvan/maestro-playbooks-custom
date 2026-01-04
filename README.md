# Maestro Playbooks

A curated collection of playbooks for [Maestro](https://github.com/pedramamini/Maestro), the cross-platform desktop app for orchestrating AI coding agents.

## About Maestro

[Maestro](https://runmaestro.ai/) is a powerful tool for managing multiple AI agents autonomously. Created by [Pedram Amini](https://github.com/pedramamini), it enables developers to run AI coding agents (Claude Code, OpenAI Codex, OpenCode) for extended periods on complex tasks with features like Auto Run, Git Worktrees, Group Chat, and mobile remote control.

This repository provides ready-to-use playbooks that leverage Maestro's Auto Run & Playbooks feature to automate common workflows and tasks.

## What are Playbooks?

Playbooks are markdown-based checklists that Maestro's Auto Run feature processes through AI agents. Each task in a playbook gets its own AI session with clean conversation context, allowing for:

- **Repeatable Workflows** - Define once, run many times
- **Batch Processing** - Queue multiple tasks for autonomous execution
- **Loop Support** - Run playbooks continuously or a set number of times
- **Full History** - Track progress and results across all runs

Learn more about [Auto Run & Playbooks](https://docs.runmaestro.ai/autorun-playbooks) in the official Maestro documentation.

## Repository Structure

```
maestro-playbooks/
├── development/          # Development workflow playbooks
├── testing/             # Testing and QA playbooks
├── deployment/          # Deployment and CI/CD playbooks
├── documentation/       # Documentation generation playbooks
├── refactoring/         # Code refactoring playbooks
└── templates/           # Playbook templates to customize
```

## Getting Started

### Prerequisites

- [Maestro](https://github.com/pedramamini/Maestro) installed and configured
- At least one AI coding agent (Claude Code, OpenAI Codex, or OpenCode) installed and authenticated

### Using a Playbook

1. Clone this repository or download individual playbook files
2. Open Maestro and navigate to your project
3. Import or reference the playbook in Maestro's Auto Run feature
4. Configure any playbook-specific variables or settings
5. Start the Auto Run and monitor progress

See the [Maestro Auto Run documentation](https://docs.runmaestro.ai/autorun-playbooks) for detailed instructions.

## Available Playbooks

Coming soon - playbooks will be added as this repository develops*

## Creating Your Own Playbooks

Playbooks are simple markdown files with checkboxes. Here's a basic template:

```markdown
# My Custom Playbook

## Task 1: Description
- [ ] Subtask 1
- [ ] Subtask 2

## Task 2: Description
- [ ] Subtask 1
- [ ] Subtask 2
```

Check the `templates/` directory for more sophisticated examples and best practices.

## Contributing

Contributions are welcome! If you've created a useful playbook:

1. Fork this repository
2. Add your playbook to the appropriate directory
3. Update the README with a description of your playbook
4. Submit a pull request

Please ensure your playbooks:

- Are well-documented with clear task descriptions
- Include any necessary setup instructions or prerequisites
- Follow the existing directory structure
- Are tested with Maestro's Auto Run feature

## Resources

- **Maestro**: [GitHub Repository](https://github.com/pedramamini/Maestro) | [Website](https://runmaestro.ai/)
- **Documentation**: [docs.runmaestro.ai](https://docs.runmaestro.ai)
- **Discord Community**: [Join the conversation](https://discord.gg/SVSRy593)
- **Auto Run Guide**: [Using Playbooks](https://docs.runmaestro.ai/autorun-playbooks)

## License

This repository is licensed under the [AGPL-3.0 License](LICENSE), the same license as Maestro itself.

## Acknowledgments

- [Pedram Amini](https://github.com/pedramamini) for creating Maestro
- The Maestro community for inspiration and feedback
