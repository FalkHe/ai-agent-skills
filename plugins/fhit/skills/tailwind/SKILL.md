---
name: tailwind
description: Tailwind CSS conventions — tokens in config, class ordering, component extraction, responsive and state variants. Preloaded into ui-designer; not invoked by users.
user-invocable: false
---

# Tailwind

- Tokens live in `tailwind.config.*` (`theme.extend`) or `@theme` (v4). Never arbitrary values (`w-[13px]`) when a token exists; new token → config, not inline.
- Class order: layout → box → typography → color → state/responsive. Prettier plugin if installed handles it.
- Repeated class sets ≥3× → component, not `@apply`.
- Dark mode via the project's strategy (`class`/`media`); every color pair needs its `dark:` counterpart if dark mode exists.
- States: `hover: focus-visible: disabled: aria-invalid:`. Never remove focus rings.
- Check purge/content paths cover new file locations.
