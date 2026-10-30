# Choosing S3 compatible storage for backups on a budget

Keeping backups on the same server as the data is not a backup. The cheapest reliable fix for small setups is S3 compatible object storage. There are many providers now, and the right choice depends on volume, egress and where your servers are.

## What matters for backups

Backups are written often and read rarely. That makes these factors important:

- **Storage price per GB per month**
- **Egress cost**, which you pay when you restore
- **Minimum storage duration**, some providers charge for 30 or 90 days even if you delete sooner
- **Region**, ideally a different location from your server
- **Object lock or versioning**, which protects against an attacker deleting backups

## Common options

- **Cloudflare R2**: no egress fees, simple pricing, good for restores you might run often
- **Backblaze B2**: low storage price, egress free up to a generous ratio and through partner CDNs
- **Hetzner Object Storage**: convenient if your servers are already at Hetzner, but consider a different provider for geographic separation
- **AWS S3**: the reference implementation, more expensive, useful if you are already in AWS
- **Self-hosted MinIO**: only makes sense on a separate machine in a separate location

## Credentials

Create a dedicated key per server or per application with access to one bucket only. If a server is compromised, the attacker should not be able to read or delete every backup you have.

Where the provider supports it, give the backup key write permission without delete permission and use lifecycle rules to expire old objects.

## Retention

A simple scheme for databases:

- Daily dumps kept for 14 days
- Weekly dumps kept for 8 weeks
- Monthly dumps kept for a year

Lifecycle rules can enforce this without scripts.

## Test the restore path

Once a month, download the latest dump to a scratch machine and restore it. Time how long it takes. The first time you do this is usually when you find a missing permission or a dump that is silently empty.

## Encryption

Most providers encrypt at rest, but encrypting dumps yourself before upload means the provider never sees plain data. Store the encryption key somewhere other than the server and the bucket.

## Wiring it into your platform

Many deployment platforms can upload database backups directly to S3 compatible storage once you add the endpoint, bucket and keys. That removes the need for custom cron scripts on each server.

For an example of configuring AWS, R2, B2, MinIO or Hetzner buckets as backup destinations, see the docs on [connecting S3 storage for backups](https://peon.sh/docs/storages).

Disclosure: I work on [Peon](https://peon.sh/).
