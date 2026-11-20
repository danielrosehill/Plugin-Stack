# Plugin-Stack

An idea note: a **plugin stack**, a second-level primitive inside a Claude Code plugin marketplace that holds a related set of plugins together, so a user can install the whole stack or just the members they want.

Status: idea only (jotted 2026-11-20). No implementation.

## Background: how I already work

- **Marketplaces are the unit of federation.** A marketplace is what someone adds and installs from. I run several: a private one, a public one, and one for my business. Each is a block with its own installation primitive.
- **Atomic tooling.** When a few skills are tightly related, I give them their own plugin, even if similar skills exist in other plugins. I'd rather draw clean boundaries around what each plugin contains than grow one large plugin.
- **Networks of plugins.** Some of my plugins are meant to work together, but I split them at the plugin level instead of putting everything in one plugin, because each part is its own project to maintain.

### Example: shopping

There's a base **shopping** plugin, plus a plugin for each retailer (Amazon, Sweetwater, Grainger, ...). Each retailer is its own problem: one has an API, another can only be reached through a real browser (Claude in Chrome), another has its own pricing or shipping traps. So each one gets its own plugin.

My usual pattern is a base plugin with a skill that installs the accessory plugins: "install the shopping plugin, then pick the retailer plugins you want; they're all in the same marketplace."

That works, but the grouping lives in a skill's prose and not in the marketplace itself.

## The proposal

Add a **stack** to the marketplace format: a named group of plugins that sits between the marketplace and the plugin.

```
marketplace
└── stack: online-shopping
    ├── shopping            (base, required)
    ├── amazon              (optional)
    ├── grainger            (optional)
    └── sweetwater          (optional)
```

In the install UI, a user would:

- see stacks as well as single plugins,
- install a whole stack in one action, or
- open the stack and choose which members to install (the base is preselected and the accessories are checkboxes).

Some properties a stack might have:

- **Members:** references to plugins in the same marketplace (and possibly other marketplaces).
- **Roles:** `base` / `required` versus `optional` members.
- **Description:** what the stack does as a whole, which none of the individual plugins says on its own.
- **No content of its own:** a stack only groups plugins. Skills, commands and MCP servers stay in the plugins, so atomic plugins stay atomic.

A sketch of what it could look like in `marketplace.json`:

```json
{
  "name": "my-marketplace",
  "plugins": [ "...as today..." ],
  "stacks": [
    {
      "name": "online-shopping",
      "description": "Shop across retailers: base orchestrator plus per-retailer plugins.",
      "members": [
        { "plugin": "shopping", "role": "base" },
        { "plugin": "amazon", "role": "optional" },
        { "plugin": "grainger", "role": "optional" },
        { "plugin": "sweetwater", "role": "optional" }
      ]
    }
  ]
}
```

## Why not just one big plugin?

- Each member has its own version, maintenance cycle and failure modes (for example, a retailer changes its site).
- A user may only want the retailers they actually use.
- Members can still be installed alone, outside the stack.
- The stack records the intent "these belong together", which currently exists only in my head or in an installer skill.

## Further notes

- [docs/current-spec-check.md](docs/current-spec-check.md): the proposal checked against the plugin marketplace format as Anthropic currently defines it.
- [docs/prior-art.md](docs/prior-art.md): similar proposals and analogues in other ecosystems.
