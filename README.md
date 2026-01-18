# Sagittarius Deployment Files

This Git repository acts as a database of sorts for the Docker Compose stacks running on Sagittarius through Portainer.

## Docker Networks

These stacks expect two pre-defined, **attachable** Docker networks to already be configured (ideally through Portainer):

1. `proxy`: for communication between Traefik and other stacks
2. `metrics`: so Prometheus can scrape metrics from other stacks

The exact configuration of these networks really doesn't matter, as long as they're **attachable** and use the `bridge` driver.

## Blackstart Order

When initially bootstrapping the server, stacks need to be started in a specific order to ensure dependencies are met:

1. [`metrics`](/metrics): Deploys Prometheus, Grafana and several exporters to monitor the host and other containers
2. [`proxy`](/proxy): The reverse proxy that routes incoming HTTP(S) requests to the correct stack
3. [`auth`](/auth): LDAP and OIDC server for central authentication across other apps
4. Everything else: All other stacks can be started in any order