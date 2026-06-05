# lich-starter-t3

The [T3 Stack](https://create.t3.gg/) (Next.js + tRPC + Prisma + Tailwind), preconfigured to run with [lich](https://lich.sh) for per-worktree isolation.

Run this template in N git worktrees and you get N independent dev stacks side by side. Each gets its own Postgres, its own dynamically allocated ports, its own dashboard tile, and its own friendly URL. No port juggling, no compose project collisions, no manual env wrangling.

## Quickstart

You need [lich](https://lich.sh) and Docker (or OrbStack / Podman).

```bash
git clone https://github.com/RPate97/lich-starter-t3
cd lich-starter-t3
lich up
```

That's it. Lich:

1. Spins up Postgres in a Docker container on a dynamically allocated host port.
2. Sets `DATABASE_URL` to the right value via `${services.postgres.host_port}` interpolation.
3. Runs `npm install` if `node_modules` doesn't exist.
4. Starts the Next.js dev server.
5. Runs `prisma db push` after Postgres reports ready.
6. Prints a friendly URL like `http://web.lich-starter-t3.lich.localhost:3300/`.

Open the URL, you have a working full-stack t3 app.

## The point: run multiple stacks at once

From a second worktree:

```bash
git worktree add ../lich-starter-t3-feature -b feature
cd ../lich-starter-t3-feature
lich up
lich stacks
```

```
WORKTREE                       STATUS  UPTIME    SERVICES  URL
lich-starter-t3                up      00:02:15  2/2       http://web.lich-starter-t3.lich.localhost:3300/
lich-starter-t3-feature        up      00:00:08  2/2       http://web.lich-starter-t3-feature.lich.localhost:3300/
```

Two stacks. Two databases. Two dev servers. No collisions. Same lich.yaml.

This is the whole point of lich, and it works for any stack that fits the `lich.yaml` shape. This template is a working example.

## Without lich

If you want to run this without lich, the t3 scripts still work:

```bash
./start-database.sh                   # spins up a Postgres container on port 5432
npm install
npm run db:push
npm run dev
```

You give up worktree isolation and the per-worktree port allocation, but the app runs.

## Files of interest

- `lich.yaml` — the stack definition. One Postgres service, one Next.js owned process. ~30 lines.
- `prisma/schema.prisma` — Prisma data model. Change as needed.
- `src/app/` — Next.js App Router code.
- `src/server/api/` — tRPC routers.
- `src/env.js` — t3-env runtime env schema.
- `.env` — local env (gitignored). Lich overrides `DATABASE_URL` at runtime; other vars flow through.

## Where to go next

- **lich docs**: [lich.sh](https://lich.sh)
- **t3 docs**: [create.t3.gg](https://create.t3.gg)
- **Add NextAuth**: this template excludes auth for a faster demo. Re-run `npm create t3-app` against a fresh dir with auth enabled and port the relevant files over, or follow [the NextAuth.js Next.js guide](https://next-auth.js.org/getting-started/example).
- **Switch from Prisma to Drizzle**: same drill — `create-t3-app` supports both; choose the Drizzle path and re-port.

## License

MIT. The t3 stack scaffold itself is MIT (see [create-t3-app](https://github.com/t3-oss/create-t3-app)).
