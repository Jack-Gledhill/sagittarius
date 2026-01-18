# Sagittarius Deployment Files

This Git repository acts as a database of sorts for the Docker Compose stacks running on Sagittarius through Portainer.

## Blackstart Order

When initially bootstrapping the server, stacks need to be started in a specific order to ensure dependencies are met:

1. [`networks`](/networks): Creates the Docker bridge networks needed for communication between stacks (e.g. for Traefik and Prometheus)
2. [`metrics`](/metrics): Deploys Prometheus, Grafana and several exporters to monitor the host and other containers
3. [`proxy`](/proxy): The reverse proxy that routes incoming HTTP(S) requests to the correct stack
4. [`auth`](/auth): LDAP and OIDC server for central authentication across other apps
5. Everything else: All other stacks can be started in any order