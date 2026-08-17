# RuleCMS Widget Integration - Vue Demo

A demonstration of how to integrate **RuleCMS widgets** into a Vue application using `@rulecms/widget-vue` and `@rulecms/source-components-vue`.

## Live Demo

**[View the live demo](https://use-rulecms-example-vue-project.vercel.app)**

Experience the RuleCMS Vue widget integration without running the app locally.

## What is RuleCMS?

**RuleCMS** is a visual content management system that lets you:

- Build widgets visually using a drag-and-drop composer
- Create responsive content that works across devices
- Publish instantly and get a unique published key for each widget
- Embed those widgets in any Vue app with a few lines of code

## How widget integration works

1. **Design** — Log into [rulecms.com](https://rulecms.com) and compose a widget.
2. **Publish** — RuleCMS generates a **published key** (`{environmentId}---widget-…`).
3. **Authenticate** — Create an **app token** in project settings so your app can fetch published widgets.
4. **Integrate** — Install the Vue packages, wrap the app with `RuleCMSWidgetProvider`, and render `RuleCMSWidget`.

`@rulecms/widget-vue` fetches and lays out the widget. It does not ship UI cards. You register `@rulecms/source-components-vue` as the default component library so text, images, buttons, and the rest of the default set can render.

## Quick start

```bash
git clone <repository-url>
cd use_rulecms_example_vue_project
npm install
npm start
```

The app is at [http://localhost:5173](http://localhost:5173). Demo credentials in `src/rulecms-config.ts` are enough to render the widget without extra setup. Or skip local setup and use the [live demo](https://use-rulecms-example-vue-project.vercel.app).

## Adding RuleCMS widgets to your own Vue app

### Step 1: Install the packages

```bash
npm install @rulecms/widget-vue @rulecms/source-components-vue
```

Peer dependency: `vue` >= 3.3.

### Step 2: Register the library and render a widget

```vue
<script setup>
import { RuleCMSWidget, RuleCMSWidgetProvider } from '@rulecms/widget-vue';
import * as sourceComponents from '@rulecms/source-components-vue';

const token = 'your-app-token';
const publishedKey = 'your-published-key';
const libraries = { default: sourceComponents };
</script>

<template>
  <RuleCMSWidgetProvider :token="token" :libraries="libraries">
    <RuleCMSWidget :published-key="publishedKey" />
  </RuleCMSWidgetProvider>
</template>
```

### Step 3: Get your credentials

**App token**

1. Log into [rulecms.com](https://rulecms.com)
2. Open project settings → API / app tokens
3. Generate a token with widget access

**Published key**

1. Create or edit a widget in the composer
2. Click Publish
3. Copy the generated published key

### Step 4: Run the app

```bash
npm start
```

## This demo's credentials

Demo values live in `src/rulecms-config.ts` (same widget and token as the React and Angular examples):

- `DEMO_RULECMS_TOKEN` — app token used to fetch the widget
- `DEMO_PUBLISHED_KEY` — published key for the demo widget

Replace those constants with your own token and published key when you wire this up to your project.

## Understanding the packages

### `@rulecms/widget-vue`

| Piece | Role |
|---|---|
| `RuleCMSWidgetProvider` | Supplies token, optional endpoint, and library registrations to widgets below it |
| `RuleCMSWidget` | Fetches a published widget and renders its layout tree |

**Provider props**

- `token` (required): app token from RuleCMS project settings
- `endpoint` (optional): API origin override. Leave unset so published tokens hit widget-cache and `dev.` tokens hit rulecms.com
- `libraries` (required for default cards): `{ default: sourceComponents }`

**Widget props**

- `publishedKey` (required): `{environmentId}---widget-…` for staging/production
- `token` / `endpoint` / `libraries`: optional overrides if you are not using the provider
- `mode`: `'client-fetch'` (default) or `'pre-fetched'`
- `componentProps`: extra host props for a component instance, keyed by column id (`r-button` reads `onClick`)

### `@rulecms/source-components-vue`

The default card library: `r-text`, `cloudinary-advanced-image`, `r-video`, `r-icon`, `r-button`, `r-divider`, `r-embed`, `r-list`, `r-accordion`. Register it on the provider. The widget package never imports this library itself.

## Multiple widgets

```vue
<RuleCMSWidgetProvider :token="token" :libraries="libraries">
  <header>
    <RuleCMSWidget :published-key="headerKey" />
  </header>
  <main>
    <RuleCMSWidget :published-key="mainKey" />
  </main>
</RuleCMSWidgetProvider>
```

## Pre-fetched rendering

Fetch on the server (or at build time), then render without a client refetch:

```ts
import { fetchRuleCMSWidget } from '@rulecms/widget-vue';

const data = await fetchRuleCMSWidget({
  publishedKey: 'your-published-key',
  token: 'your-app-token',
});
```

```vue
<RuleCMSWidget
  mode="pre-fetched"
  :published-key="publishedKey"
  :initial-data="data"
  :libraries="libraries"
/>
```

This demo uses **client-fetch** so `npm start` is enough.

## Host `componentProps`

Pass a click handler (or any extra prop) to one component instance by column id:

```vue
<RuleCMSWidget
  :published-key="publishedKey"
  :component-props="componentProps"
/>
```

```ts
const componentProps = {
  'b721c4e2-…': { onClick: (event: Event) => console.log('cta', event) },
};
```

## Troubleshooting

**Widget not displaying**

- Confirm the published key and token in `src/rulecms-config.ts`
- Confirm `RuleCMSWidgetProvider` wraps `RuleCMSWidget`
- Confirm `{ default: sourceComponents }` is registered
- Check the browser console

**Missing cards / blank layout**

- `@rulecms/widget-vue` will not render default cards unless `@rulecms/source-components-vue` is registered

## Scripts

| Command | What it does |
|---|---|
| `npm start` | Dev server at http://localhost:5173 |
| `npm run build` | Production build in `dist/` |
| `npm run preview` | Preview the production build |

## Project structure

```
use_rulecms_example_vue_project/
├── src/
│   ├── App.vue              # Provider + widget
│   ├── main.ts
│   ├── rulecms-config.ts    # Demo token and published key
│   └── style.css
├── package.json
├── vercel.json
└── README.md
```

## Learn more

- RuleCMS: [rulecms.com](https://rulecms.com)
- [`@rulecms/widget-vue`](https://www.npmjs.com/package/@rulecms/widget-vue)
- [`@rulecms/source-components-vue`](https://www.npmjs.com/package/@rulecms/source-components-vue)
- Vue: [vuejs.org](https://vuejs.org)

## License

MIT
