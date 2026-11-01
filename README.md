# HedgeDoc LazyCat community package

Upstream https://github.com/hedgedoc/hedgedoc (AGPL-3.0), 1.12.0 from the 1.x LTS line. Package `community.lazycat.app.hedgedoc`, min OS 1.5.0, amd64, mirror delivery, community (喵喵) store only.

HedgeDoc publishes images on **quay.io**, not GHCR or Docker Hub, so the Action config sets an explicit `image_template: quay.1ms.run/hedgedoc/hedgedoc:{tag}` and the mirror resolves through that host. The mirror was verified reachable for this repository before packaging.

One container, no external database: `CMD_DB_URL` points SQLite at a bound directory, and uploads get their own bind:

    /lzcapp/var/db:/hedgedoc/db                        # SQLite database
    /lzcapp/var/uploads:/hedgedoc/public/uploads       # uploaded images

Two settings exist because upstream's defaults are unsafe here:

- `CMD_SESSION_SECRET` defaults to the literal `secret`; this package derives it from a platform `stable_secret` instead.
- `CMD_ALLOW_ANONYMOUS` is set to false so unauthenticated visitors cannot create notes. The local **email strategy stays enabled** on purpose — it is the same strategy that provides `/register`, i.e. the only way the first user can create an account. Disabling `CMD_EMAIL` would lock everyone out.

Also `CMD_ALLOW_FREEURL=false`: letting users claim arbitrary note IDs widens the attack surface for no benefit here.

Not device-tested: registration, login, collaborative editing, image upload, slide mode, export, and persistence across restarts.
