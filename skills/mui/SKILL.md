---
name: mui
description: MUI (Material UI) conventions — theme tokens, sx vs styled, component slots, accessibility. Preloaded into ui-designer; not invoked by users.
user-invocable: false
---

# MUI

- All values from the theme: `theme.palette.*`, `theme.spacing()`, `theme.typography.*`. Extend the theme in the existing `createTheme` call; never hardcode hex/px.
- `sx` for one-off, `styled()` for reused, theme `components` overrides for global defaults. Not all three for one thing.
- Use component slots/`slotProps` instead of wrapping and overriding CSS.
- Layout: `Stack`, `Grid`, `Box`; no custom flex wrappers.
- Accessibility: every icon-only button has `aria-label`; form fields via `TextField` with `label` and `helperText` for errors.
- Never import from `@mui/material` root in hot paths if the project uses path imports; follow the existing import style.
