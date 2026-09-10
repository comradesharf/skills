# UniFFI Xcode Skill

<!-- agent-list:start -->
Supports coding agents that implement the [Agent Skills specification](https://agentskills.io).
<!-- agent-list:end -->

This repository contains the `uniffi-xcode` skill for integrating Rust libraries
with Xcode or Swift Package Manager through UniFFI.

## Install a Skill

Install the skill in the current project:

```bash
npx skills add comradesharf/skills --skill uniffi-xcode
```

Install it globally for use across projects:

```bash
npx skills add comradesharf/skills --skill uniffi-xcode --global
```

You can also install from the repository URL:

```bash
npx skills add https://github.com/comradesharf/skills --skill uniffi-xcode
```

## Use a Skill Without Installing

Generate a prompt for the skill without adding it to an agent:

```bash
npx skills use comradesharf/skills --skill uniffi-xcode
```

To start a supported agent with the generated prompt, pass its agent name:

```bash
npx skills use comradesharf/skills --skill uniffi-xcode --agent claude-code
```

## What is the UniFFI Xcode Skill?

The skill helps an agent implement and diagnose a working Rust-to-Swift bridge.
It covers:

- inspecting Rust crates, UniFFI exports, Apple consumers, and existing build setup;
- generating Swift bindings, C headers, module maps, and XCFrameworks;
- wiring generated bindings into Swift Package Manager or Xcode;
- verifying architectures, symbols, package resolution, and Swift-to-Rust calls;
- diagnosing missing modules, undefined symbols, stale bindings, and runtime loading failures.

The skill preserves an existing integration model when one is present. For a new
integration, it generally prefers a local Swift package with generated Swift
sources and an XCFramework binary target.

## Supported Agents

The skill uses standard `SKILL.md` metadata and can be installed into any agent
supported by the Skills CLI. The exact installation path depends on the selected
agent and whether the skill is installed at project or global scope.

## Creating Skills

This repository is itself a small skills repository. Add a new skill under
`skills/<name>/SKILL.md` with YAML frontmatter containing the required fields:

```markdown
---
name: my-skill
description: What this skill does and when to use it
---

# My Skill

Instructions for the agent to follow when the skill is activated.
```

Keep detailed, reusable material in a `references/` directory and link to it from
the skill instructions. For the expected UniFFI integration decisions, see
[`references/integration.md`](skills/uniffi-xcode/references/integration.md).

## Compatibility

The skill is agent-agnostic at the instruction level. Agent-specific behavior,
such as supported frontmatter fields, installation paths, and tool permissions,
is determined by the agent that loads it.

The integration output is platform-specific: the consuming project must provide
the required Rust Apple targets, Apple SDKs, Xcode or Swift Package Manager, and
an appropriate build destination.

## Troubleshooting

### Skill is not found

Confirm that the skill was installed with the correct name:

```bash
npx skills add comradesharf/skills --list
```

The repository must contain a valid `SKILL.md` with `name` and `description`
frontmatter fields.

### Skill does not load in an agent

- Verify that it was installed to the path used by the selected agent.
- Check the agent documentation for skill-loading requirements.
- Confirm that the `SKILL.md` frontmatter is valid YAML.

### UniFFI integration fails

Read the [integration guide](skills/uniffi-xcode/references/integration.md) for
packaging decisions and the [audit checklist](skills/uniffi-xcode/references/checklist.md)
for verification coverage. Do not treat binding generation alone as proof that
the Xcode integration works.

## Related Links

- [Agent Skills Specification](https://agentskills.io)
- [Skills Directory](https://skills.sh)
- [Skills CLI](https://github.com/vercel-labs/skills)
- [UniFFI Documentation](https://mozilla.github.io/uniffi-rs/)
- [Apple Developer Documentation](https://developer.apple.com/documentation/)

## License

This repository does not currently include a license file.
