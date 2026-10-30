# Pre-launch checklist for a self-hosted SaaS

Launch day is a bad time to discover that backups were never tested or that the server has no alerting. This checklist covers the operational basics for a SaaS running on your own VPS or small cluster. It is not about features; it is about staying up once people arrive.

## Domains and TLS

- [ ] Apex and `www` both resolve and one redirects to the other
- [ ] Certificates renew automatically; check the renewal date
- [ ] HSTS enabled once you are sure HTTPS works everywhere

## Email

- [ ] Transactional email goes through a dedicated provider, not the server itself
- [ ] SPF, DKIM and DMARC records set
- [ ] Sign-up, password reset and invoice emails tested with Gmail and Outlook addresses

## Data

- [ ] Automated daily database backups stored off the server
- [ ] One full restore tested into a scratch database
- [ ] User uploads on a volume or object storage, included in backups

## Monitoring

- [ ] External uptime check on the homepage and a health endpoint
- [ ] Alerts go to a channel someone actually reads
- [ ] Error tracking catches unhandled exceptions
- [ ] Disk space alert at 80%

## Deploys

- [ ] Deploys are one command or one push
- [ ] Rollback has been tried at least once
- [ ] Database migrations are backward compatible with the previous release

## Security

- [ ] SSH keys only, root login disabled
- [ ] Only ports 80 and 443 open publicly
- [ ] Secrets stored outside the repository
- [ ] Admin panels behind authentication and not indexed

## Capacity

- [ ] A simple load test on the slowest important page
- [ ] Memory limits on containers so one service cannot starve the rest
- [ ] A plan for the next size up if usage grows

## Legal and support

- [ ] Privacy policy and terms published
- [ ] Support email monitored
- [ ] Status page or at least a place to post outage updates

## Cost check

Add up the monthly cost of the server, backups storage, email and monitoring. Small SaaS products often run comfortably on a single modest VPS, and knowing the exact figure helps you price plans with margin.

## The week after launch

Watch error rates and slow queries daily. Most launch issues are small: a missing index, a timeout set too low, an email template with a broken link. Fixing them quickly matters more than any architecture decision.

For a breakdown of what hosting a SaaS really costs, see this comparison of the [cheapest ways to host a SaaS app](https://peon.sh/blogs/cheapest-way-to-host-saas-app-2026).

Disclosure: I work on [Peon](https://peon.sh/).
