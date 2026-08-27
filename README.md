# ChatThing WebMCP Demo

A small fake storefront that shows a ChatThing widget discovering and using tools provided by its host page through WebMCP.

[![Watch the 24-second ChatThing WebMCP demo](https://files.catbox.moe/1r9ug2.jpg)](https://files.catbox.moe/6izvwd.mp4)

[Watch the 24-second MP4](https://files.catbox.moe/6izvwd.mp4)

The assistant can:

- search the products rendered on the page;
- add a matching product to the bag and update the page UI;
- check a delivery estimate for a UK postcode.

Everything is fake and runs in the browser. The catalogue, bag and delivery result do not touch a backend or customer data.

## How it works

The page exposes three tools through `document.modelContext`. ChatThing loads with `webMcp: true`, discovers the compatible tool schemas and mirrors them into the embedded conversation. When the model invokes a tool, the SDK forwards the call to the host page and returns its result to the conversation.

The demo feature-detects the runtime at startup:

- when the browser supplies a complete `document.modelContext`, the tools are registered with that native runtime;
- otherwise, the page installs a minimal compatibility shim implementing the same `registerTool()`, `getTools()`, `executeTool()` and `toolchange` contract.

The status in the page header reports whether the current session is using the `native` or `shim` runtime.

## SDK behavior without WebMCP

ChatThing does not require a WebMCP runtime. With `webMcp` omitted or false, the bridge is never started. With `webMcp: true` in a browser that has no valid `document.modelContext`, the SDK logs a warning and safely leaves the bridge off; the rest of the widget continues to work normally.

## Configure a bot

Create a ChatThing bot and enable **Advanced SDK features** on its web channel. This server-controlled setting enables client-side power-up registration; `webMcp: true` alone is intentionally insufficient.

Then edit [`config.js`](./config.js):

```js
window.WEBMCP_DEMO_CONFIG = {
  botId: "your-bot-id",
  chatThingUrl: "https://chatthing.ai",
  widgetScript: "https://chatthing-sdk.pages.dev/index.js",
};
```

These values are public browser configuration, not secrets.

You can also override them without editing the repository:

```text
/?botId=YOUR_BOT_ID&url=https%3A%2F%2Fchatthing.ai&script=https%3A%2F%2Fchatthing-sdk.pages.dev%2Findex.js
```

## Run locally

```bash
pnpm install
pnpm dev
```

Open <http://localhost:4173>.

## Deploy

The project is a static Vite site and can be deployed to Cloudflare Pages, Netlify, Vercel or GitHub Pages.

- Build command: `pnpm build`
- Output directory: `dist`

After deploying, update `config.js` with the live bot and app URL, rebuild, and redeploy.

## Expected demo

Ask:

> Find a warm lamp under £70 and add it to my bag.

The assistant should call `search_store_catalogue`, then `add_store_item_to_bag`. The page will animate the matching card, show a confirmation and change the bag count from 0 to 1.

## License

MIT
