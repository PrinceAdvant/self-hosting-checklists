# Database migrations during deploys: where and when to run them

Schema migrations are the riskiest part of most deploys. Code can be rolled back in seconds; a dropped column cannot. Where and when migrations run decides whether a deploy is boring or stressful.

## Option 1: in the container entrypoint

The app runs migrations on start, then boots. Simple, and popular with frameworks like Rails and Django.

Problems:

- With several replicas, each tries to migrate at once. Most frameworks lock, but not all.
- A slow migration delays startup and can trip health checks.
- A failed migration leaves the container in a crash loop.

Acceptable for single-instance apps with fast migrations.

## Option 2: a release step before switching traffic

Run a one-off container with the new image that only migrates, then start the new app containers. Heroku's release phase works this way. If the migration fails, the deploy stops and the old version keeps serving.

This is the best default for most teams.

## Option 3: manual, outside deploys

A person runs migrations by hand. Useful for very large tables where you want to watch progress, but it does not scale as a habit.

## Make migrations backward compatible

Whatever runs them, the old code will briefly run against the new schema. Use the expand and contract pattern:

1. **Expand**: add new columns or tables, keep old ones
2. Deploy code that writes to both and reads from the new one
3. Backfill data
4. **Contract**: remove old columns in a later deploy

Never rename or drop a column in the same deploy that stops using it.

## Long-running migrations

Adding an index on a big table can lock writes. In Postgres, use `CREATE INDEX CONCURRENTLY`. Adding a column with a default is fast in modern Postgres, but backfilling millions of rows should happen in batches.

## Preview environments

Preview deploys for pull requests should use their own database or a disposable copy. Running a branch migration against the shared staging database breaks everyone else's previews.

## Rollback plan

Before each risky migration, take a fresh backup and write down what rollback means. Often the answer is "deploy the previous code, the schema stays", which only works if the migration was additive.

## Log it

Record which migration ran, when and by which deploy. When something looks odd a week later, that history saves time.

To see how a platform queues deployments, handles rollbacks and isolates pull request previews, read the docs on [deployments and previews](https://peon.sh/docs/deployments-and-previews).

Disclosure: I work on [Peon](https://peon.sh/).
