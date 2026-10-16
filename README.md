# wikijs-shadcn

A [shadcn/ui](https://ui.shadcn.com)-inspired theme for **Wiki.js 2**, applied entirely through the built-in CSS injection — no fork, no custom theme package, no rebuild.

White surfaces on a light grey page, 1px borders instead of shadows, rounded cards, neutral zinc palette, Inter, and callouts restyled as shadcn alerts.

> Screenshots go here once you add them: `docs/before.png`, `docs/after.png`.

## Install

1. Open **Administration → Theme → Code Injection**.
2. Paste the whole of [`theme.css`](theme.css) into **CSS Override**.
3. Click **APPLY**.
4. Hard-reload the page (<kbd>Cmd/Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>R</kbd>) — the old CSS is cached.

Optionally paste [`head-injection.html`](head-injection.html) into **Head HTML Injection** to load Inter. The theme is designed to look right without it.

Nothing else is required, and nothing outside those two fields is touched — uninstalling means clearing the field again.

## Customising

Everything you normally need sits in the `:root` block at the top of `theme.css`. The default accent is shadcn's near-black. To use a brand colour, set three values:

```css
--sh-accent:       #961a1a;  /* buttons, links, active nav item, FAB */
--sh-accent-hover: #7d1515;  /* ~10% darker */
--sh-accent-soft:  #fdf3f3;  /* ~4% tint, used behind the active nav item */
```

Two blocks are marked as optional in the file:

- **§12.4** renders page content as a white panel on the grey background. Delete it if you prefer content flush on the background.
- **§9** restyles callouts. Wiki.js' own `is-info` / `is-success` / `is-warning` / `is-danger` classes are kept, just flattened.

## Why the selectors look over-qualified

This is the part worth knowing before you edit anything.

Wiki.js loads its Vue/Vuetify stylesheets **at runtime**, which means they land in `<head>` *after* the CSS you inject. Vuetify writes its colour classes as:

```css
.v-application .black { background-color: #000 !important }
```

So `!important` meets `!important`, specificity decides, and on a tie the later stylesheet wins — always Vuetify. A plain `.nav-header { background: #fff !important }` does nothing at all.

Every rule here that competes with a Vuetify colour class is therefore lifted to specificity (0,3,1) using an attribute selector:

```css
.v-application header.v-app-bar[class] { … }
.v-application nav.v-navigation-drawer[class] { … }
```

`[class]` matches any element that has a `class` attribute and adds specificity without changing what is matched. That outranks any colour class, so you don't need to know which one Wiki.js applies to a given element — which is useful, because it varies between versions.

Three other gotchas baked into the file:

- **Never set a height on `.v-toolbar__content`.** Vuetify computes the `.v-main` top offset from the app bar's original height; override it and the search field spills out of the bar.
- **Callout arrows are border triangles** in `::before` / `::after`. Clearing their background leaves the triangle; you need `content: none` and `border: 0`.
- **Content styles must stay scoped to `.contents`**, as Wiki.js warns in the admin UI, or the editor layout breaks.

## Scope

- Light mode only. Dark mode is untouched and will look wrong — PRs welcome.
- Targets the **default theme** of Wiki.js 2.x (Vuetify 2). Not tested against Wiki.js 3.
- The admin area is deliberately left alone.

## Licence

MIT — see [LICENSE](LICENSE).

Not affiliated with shadcn/ui or Wiki.js.
