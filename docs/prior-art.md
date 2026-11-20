# Prior art and similar proposals

Surveyed 2026-11-20. I confirmed the GitHub issues with `gh`. The analogues in other ecosystems come from general knowledge, and I did not re-fetch their URLs.

## Verdict

**No one I found proposes a stack as a marketplace-level primitive.** People have asked for the pieces, and Anthropic shipped only one of them: `dependencies` and the "bundle plugin" pattern (see [current-spec-check.md](current-spec-check.md)). The requests for grouping and bulk install were closed without a fix.

## anthropics/claude-code issues

Grouping in a marketplace:

- **[#54315](https://github.com/anthropics/claude-code/issues/54315)** "Support categories/groups in marketplace.json for plugin organization". Opened 2026-04-28. Closed 2026-05-31 as `not_planned`, but the closer was the inactivity bot, not a maintainer. This is the closest request, and a new issue could reference it.

Bulk install, all closed:

- [#43927](https://github.com/anthropics/claude-code/issues/43927) "Bulk install all plugins from a marketplace or multiple plugins in `/plugin install`", opened 2026-04-05.
- [#21370](https://github.com/anthropics/claude-code/issues/21370) "Bulk install all plugins from a marketplace", opened 2026-01-28.
- [#26561](https://github.com/anthropics/claude-code/issues/26561) "Bulk installation feature for enabled plugins", opened 2026-02-18.
- [#14843](https://github.com/anthropics/claude-code/issues/14843) "Bulk enable/disable plugins", opened 2025-12-20.
- [#68639](https://github.com/anthropics/claude-code/issues/68639) "Support default-enabled flag for plugins in marketplace.json", opened 2026-06-15. It led to `defaultEnabled`, which now exists.

Dependencies, which shipped:

- [#9444](https://github.com/anthropics/claude-code/issues/9444) "Support for Plugin Dependencies and Shared Resources", opened 2025-10-12 and closed 2026-08-17.
- [#73522](https://github.com/anthropics/claude-code/issues/73522) "Treat Plugins as First-class Vendored Dependencies", open.
- [#91440](https://github.com/anthropics/claude-code/issues/91440) "Cowork/synced plugin packs can't be listed, inspected, or disabled", closed. "Plugin packs" seems to be a term in Cowork's synced-plugin feature. Worth checking whether that is a group primitive that exists outside the Claude Code CLI.

## Analogues in other ecosystems

| Ecosystem | Mechanism | Install all | Pick members | Notes |
|---|---|---|---|---|
| VS Code | [Extension Packs](https://code.visualstudio.com/api/references/extension-manifest) (`extensionPack` in `package.json`) | Yes | After install (uninstall members individually) | The closest analogue: a pack is itself an extension that lists other extensions. |
| Helm | Umbrella charts, where each sub-chart has a `condition` toggle | Yes | Yes, through values | The best model for optional members. |
| Debian | Metapackages (`Depends`/`Recommends`) and `tasksel` tasks | Yes | `Recommends` can be skipped | `Recommends` is effectively an optional dependency. |
| Arch | Package groups (`pacman -S group`) | Yes | Yes, with an interactive picker | Exactly the "install all or choose" UX. |
| npm | Metapackages and presets | Yes | No | Like the Claude Code bundle plugin today. |
| Homebrew | `Brewfile` / `brew bundle` | Yes | Edit the file | A user-side set, not something the publisher defines. |
| JetBrains | `<depends optional="true">` in `plugin.xml` | Yes | Optional dependencies | The precedent for an `optional` flag. |

Two lessons from these:

- Optional members need a first-class flag. Debian has `Recommends`, JetBrains has `optional="true"` and Helm has `condition`.
- The Arch group picker is the install UI I want.

## Community

Large community marketplaces and lists (ComposioHQ/awesome-claude-plugins, ccplugins/awesome-claude-code-plugins, laurigates/claude-plugins, claude-market/marketplace) and the aggregator sites all present flat lists. A Dev Genius post, "The Claude Code Plugin Starter Stack for Web Developers", talks about "starter packs" informally. None of them implement a stack primitive.
