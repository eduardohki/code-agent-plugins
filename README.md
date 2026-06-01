# code-agent-plugins

Personal Claude Code plugin marketplace, also consumable by the `skills` CLI tool for other coding agents (OpenCode, Pi, etc.).

## Plugins

| Plugin | Description |
| --- | --- |
| [`engineering-practices`](./plugins/engineering-practices) | Skills covering software engineering practices like requirements, BDD, work decomposition, and delivery flow. |

## Install in Claude Code

Add this repo as a marketplace and install a plugin from it:

```sh
/plugin marketplace add eduardohki/code-agent-plugins
/plugin install engineering-practices@runlevel0
```

## Use with the `skills` CLI

```sh
npx skills add eduardohki/code-agent-plugins
```

## Credits

The [`shape-features`](./plugins/engineering-practices/skills/shape-features) skill draws on the [Dojo Consortium](https://dojoconsortium.org/docs/) body of work on engineering practices, used under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
