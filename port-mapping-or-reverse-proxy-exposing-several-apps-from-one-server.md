# Port mapping or reverse proxy: exposing several apps from one server

The first app on a new server is often exposed with a simple port mapping, such as `-p 80:3000`. The second app gets `-p 8080:3000`, the third `-p 8081:8000`, and soon users are visiting URLs with port numbers. A reverse proxy fixes this, and it is worth setting up before the second app arrives.

## Problems with port mapping alone

- Users need port numbers in URLs, which look odd and are often blocked by corporate networks
- Each app needs its own TLS setup, or no TLS at all
- Every published port is a potential exposure, and Docker publishes ports around most host firewalls
- Moving an app to another server means changing URLs

## What a reverse proxy does

A single proxy listens on 80 and 443 and routes requests by hostname:

- `app.example.com` to the app container on port 3000
- `api.example.com` to the API container on port 8000
- `status.example.com` to a monitoring tool

Each app keeps its internal port, nothing else is published, and the proxy handles certificates for every domain.

## Choosing a proxy

- **Caddy**: short config files, automatic HTTPS, very approachable
- **Traefik**: reads Docker labels, so apps register themselves
- **Nginx**: battle tested and flexible, more manual for certificates

All three work. Pick one and use it for everything on the server.

## Network layout

Create one shared network for the proxy and the apps it serves. Databases stay on each app's private network. Nothing except the proxy publishes ports.

## When port mapping is still fine

- Local development
- Internal services reachable only over a VPN
- Binding to `127.0.0.1` for an admin tool you reach through an SSH tunnel

## Migrating an existing server

1. Start the proxy on 80 and 443
2. Add routes for each app by hostname
3. Point DNS records at the server
4. Remove the old `ports:` entries from each app
5. Scan the server from outside to confirm only 22, 80 and 443 are open

## Resource planning

A proxy uses very little memory. The real limit for many apps on one server is RAM for the apps and databases themselves, so set container memory limits and watch usage as you add more.

For a complete walkthrough, including resource budgets and DNS, see this guide to [running multiple apps on one VPS](https://peon.sh/blogs/how-to-run-multiple-apps-on-one-vps).

Disclosure: I work on Peon.
