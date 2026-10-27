# Deploy and Host Rackula on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/rackula?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=rackula)

[Rackula](https://github.com/RackulaLives/Rackula) is a visual designer for server racks. Drag devices into a rack, see them with real front and rear images from the NetBox device-type library, and plan the layout before you move hardware. It works for a homelab shelf or a row in a server room. You can track power, label every unit, and export the rack as an image or PDF to document what's where. This template runs Rackula with server-side storage behind a password, so your layouts live on Railway instead of one browser.

## About Hosting Rackula

The stack is two services, both upstream's official images pinned to a release:

- **Rackula** (`ghcr.io/rackulalives/rackula`) is the web app, served by nginx on a public domain. It checks every request with the API, so nothing is visible before you sign in.
- **API** (`ghcr.io/rackulalives/rackula-api`) stores layouts as YAML files on a volume, with automatic snapshots. It only listens on Railway's private network. Writes need a token that only the web service's nginx holds.

**Signed in from the start.** The template turns on Rackula's local auth with the username `admin` and a generated password. Sessions are signed with a generated secret, and CSRF protection is on.

**Tiny footprint.** Measured idle: Rackula ~45 MB and API ~95 MB. It runs on the Free plan.

## Common Use Cases

- Planning a homelab rack before buying or moving hardware
- Documenting a server room or network closet so anyone can see what sits in each U
- Sharing rack diagrams in runbooks, tickets and handovers (image or PDF export)
- Checking fit, depth and power budget for new equipment
- Keeping a versioned history of rack changes with layout snapshots

## Dependencies for Rackula Hosting

- None besides the two included services; no database is needed (layouts are files on a volume)

### Deployment Dependencies

- [Rackula on GitHub](https://github.com/RackulaLives/Rackula)
- [Rackula self-hosting guide](https://github.com/RackulaLives/Rackula/blob/main/docs/deployment/SELF-HOSTING.md)
- [Template source on GitHub](https://github.com/two-workspaces/rackula)

### Implementation Details

**Sign in.** When the deploy is green, open the Rackula domain and sign in as `admin` with the password in the **API** service's `RACKULA_LOCAL_PASSWORD` variable. To change either, edit `RACKULA_LOCAL_USERNAME` or `RACKULA_LOCAL_PASSWORD` (12+ characters) on the API service and redeploy it.

**Single sign-on.** Rackula also supports OIDC. Set `RACKULA_AUTH_MODE=oidc` on both services and add the `RACKULA_OIDC_*` variables from the self-hosting guide to the API.

**Public, no login.** For a read-only showcase or a private network, set `RACKULA_AUTH_MODE=none` on both services. Anyone with the URL can then edit layouts.

**Custom domain.** Add the domain to the Rackula service, then set `CORS_ORIGIN` on the API to `https://your.domain` and redeploy the API.

**Upgrading.** Change both image tags to the same newer release from the [releases page](https://github.com/RackulaLives/Rackula/releases) and redeploy. Layouts on the volume are kept.

**Back up.** Each layout is a folder of YAML (plus snapshots and any custom device images) under `/data` on the API volume. Use Railway's volume backups, or export layouts from the app.

## Why Deploy Rackula on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying Rackula on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
