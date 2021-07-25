# Metricbeat

This is useful tool for monitoring different aspects of a server like
CPU/RAM usage, networkings, etc.

## Environment Variables

Use one of `.env.sample` or `.env.fish.sample` files to create your own
environment variables. You don't need to source your `.env` file because
the variables will be picked up by docker upon initialization of the
service. But in case you needed to directly source your environment
variables, use:

```bash
. .env

# Only for fish shell:
. .env.fish
```
