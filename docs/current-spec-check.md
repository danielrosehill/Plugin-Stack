# The proposal against the current Claude Code marketplace format

Checked 2026-11-20 against the official docs at code.claude.com: `plugins/marketplace-reference`, `plugins/manifest-reference`, `plugins/dependencies`, `plugins/install` and `plugins/org`. Claude Code at that time was v2.1.3xx. "Doc" means a fetched doc page states it. "Inferred" means it is my conclusion from the docs.

## What exists today

| Mechanism | What it does | Source |
|---|---|---|
| `category` (per plugin entry) | A free-form string "for organizing the catalog". Each plugin has one. | doc |
| `tags` / `keywords` | Free-form strings "for search". | doc |
| `dependencies` (in `plugin.json` or the marketplace entry) | Takes `"name"`, `"name@marketplace"` or `{name, marketplace, version}` with a semver range. Installing a plugin installs and enables its dependencies at the same scope. | doc |
| **Bundle plugin** | The docs' own pattern ("Bundle plugins for a team"): a plugin whose manifest holds only `name` and `dependencies`, so that one install pulls in a curated set. Their example is `backend-standard`. | doc |
| `allowCrossMarketplaceDependenciesOn` | A top-level marketplace field. Without it, dependencies on other marketplaces are refused. | doc |
| `defaultEnabled: false` | The plugin starts disabled, but it is forced on if an enabled plugin depends on it. | doc |
| `enabledPlugins` + `extraKnownMarketplaces` | Settings in managed settings or a repo's `.claude/settings.json`. Managed settings auto-install and lock; repo settings enable a plugin but each user still installs it. | doc |
| `claude plugin prune` | Uninstalling a bundle leaves its dependencies in place until you prune. | doc |

**There is no grouping primitive between the marketplace and the plugin** (doc: none is described). The `/plugin` UI has Discover, Installed, Marketplaces and Errors tabs, installs one plugin at a time, and has no multi-select. The docs don't say whether the UI groups plugins by `category`.

## How close the bundle plugin gets

Without anything new, a stack could be built today as a bundle plugin:

```json
{ "name": "online-shopping-stack",
  "dependencies": ["shopping", "amazon", "grainger", "sweetwater"] }
```

That covers **install the whole stack**. It is missing the rest of the proposal:

| Stack requirement | Bundle plugin today |
|---|---|
| Install everything in one action | Yes |
| Choose a subset of members at install time | **No.** Dependencies are all-or-nothing. `defaultEnabled: false` doesn't help because dependencies are force-enabled. |
| Mark members as base/required vs optional | **No.** There is no optional-dependency field. |
| Show the group as a group in the UI | **No.** It shows as just another plugin, and you can't see its members from the browse view. |
| Add a member without a release | **No.** You have to edit and version-bump the bundle's `dependencies`. Auto-update then installs the new member (doc, v2.1.319+). |
| Uninstall the whole group cleanly | Partly: uninstall the bundle, then `claude plugin prune`. |
| Leave members atomic and installable alone | Yes |

My current workaround, a base plugin with an "install accessories" skill, covers subset choice (the agent asks) but nothing else on the list, and the grouping lives in skill prose.

## What the proposal would actually add

Inferred: the smallest change to the format that delivers a stack:

1. **Optional dependencies**, e.g. `"optionalDependencies"` or `{ "name": "amazon", "optional": true }`. The installer offers them as checkboxes and does not force-install them. Most of the semantics would come from this field alone.
2. **A `stacks` array in `marketplace.json`**, or a `"type": "stack"` flag on a bundle plugin, so the UI can render the group, list its members and offer "install all / choose".
3. **Stack-level uninstall** that removes members no other plugin needs, which is roughly `prune` scoped to the stack.

Option 2 could reuse the bundle-plugin machinery as it is: a stack is a bundle plugin with optional members and a UI hint. That makes it a small extension to the format, not a new concept, and probably the easiest form to propose upstream.
