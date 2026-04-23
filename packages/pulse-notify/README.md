# @orbital/pulse-notify

**React hooks for live Stellar events.** Drop a hook into any component and receive real-time payment, activity, or custom event streams from an Orbital server — with automatic reconnection and zero wiring.

```bash
pnpm add @orbital/pulse-notify react
```

Requires React 18 or 19. Designed for Next.js App Router, Vite, Remix, and plain React apps.

## What it does

`pulse-notify` opens a browser-native `EventSource` connection to your Orbital server, subscribes to an address, and re-renders your component whenever a new event arrives. It is intentionally thin — no global store, no custom cache, no peer-dependency on a state manager.

You point the hook at your own Orbital server (self-hosted or managed) and pass the address you want to watch.

## Quickstart

```tsx
"use client";
import { useStellarPayment } from "@orbital/pulse-notify";

export function LiveBalance({ address }: { address: string }) {
  const { event, connected, error } = useStellarPayment(
    process.env.NEXT_PUBLIC_ORBITAL_URL!,
    address
  );

  if (error) return <div className="text-red-500">{error}</div>;
  if (!connected) return <div>Connecting…</div>;
  if (!event) return <div>Listening for payments…</div>;

  return (
    <div>
      +{event.amount} {event.asset} from {event.from.slice(0, 8)}…
    </div>
  );
}
```

## Hooks

### `useStellarEvent(config)`

The core hook. Subscribes to any event type on an address.

```ts
const { event, connected, error } = useStellarEvent({
  serverUrl: "https://events.example.com",
  address: "GABC...",
  event: "payment.received", // or "*" for all
  token: "orb_live_abc...",  // optional API key
});
```

### `useStellarPayment(serverUrl, address)`

Shorthand for payments received. Equivalent to `useStellarEvent({ ..., event: "payment.received" })`.

### `useStellarActivity(serverUrl, address)`

Shorthand for all events on an address. Equivalent to `useStellarEvent({ ..., event: "*" })`.

## Return shape

```ts
type EventState = {
  event: NormalizedEvent | null; // Latest event, or null before first arrival
  connected: boolean;            // True once the SSE handshake completes
  error: string | null;          // Error message if the connection fails
};
```

Every render returns the *most recent* event. If you need history, accumulate it yourself in component state:

```tsx
const [history, setHistory] = useState<NormalizedEvent[]>([]);
const { event } = useStellarActivity(url, address);

useEffect(() => {
  if (event) setHistory((h) => [event, ...h].slice(0, 50));
}, [event]);
```

## Authentication

If your Orbital server enforces API-key auth, pass `token` in the config. The hook forwards it as a `?token=` query parameter (since `EventSource` does not support custom headers in browsers).

```tsx
useStellarEvent({
  serverUrl,
  address,
  token: process.env.NEXT_PUBLIC_ORBITAL_TOKEN,
});
```

**Server-only tokens** (secrets) must never ship to the browser. Use a per-user short-lived token issued by your backend.

## Server-side rendering

The hooks are client-only — they rely on `EventSource`, which does not exist in Node. In Next.js App Router, mark the consuming component with `"use client"`. In Remix or Vite SSR, gate the hook behind a client-only boundary.

## Current limitations

- One connection per hook instance. Deduplication across components watching the same address is a planned enhancement.
- No offline queue. Events that arrive while the tab is backgrounded and the connection is closed are not replayed on reconnect.
- `EventSource` reconnect is browser-controlled. Fine-grained retry policy belongs in a future WebSocket-based transport.

## License

MIT
