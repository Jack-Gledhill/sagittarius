# Sagittarius Deployment Files

This Git repository acts as a database of sorts for the Docker Compose stacks running on Sagittarius through Portainer.

## Deployment

Portainer's UI can be accessed at [`sagittarius.starsystem.dev:9000`](https://sagittarius.starsystem.dev:9000) (VPN required).
The `Stacks` menu will show all existing stacks and allow the addition of new stacks.

### Environment Variables

Any file ending in `.env` will automatically be ignored during commits through the `.gitignore` file. 
This is intentional.

Most environment variables should already be included in the stack's Compose file under each service's `environment` element, but secrets should NOT be included in there directly.
Instead, you should add an environment variable whose value is equal to another environment variable, which you will populate within Portainer.

Example:
```yaml
# compose.yml
services:
  postgres:
    image: postgres/postgres:18
    # other elements removed for brevity
    environment:
      DB_PASSWORD: ${DB_PASSWORD}
```
```
# .env
DB_PASSWORD=reallySecretPassword
```

**Note:** you can't use [Docker Compose Secrets](https://docs.docker.com/compose/how-tos/use-secrets/) with Portainer's stack deployment system.

### New Stacks

New stacks can be added using the `Add stack` button.
Stacks should be added using the `Repository` build method with the following parameters:

- **Repository URL:** https://github.com/Jack-Gledhill/sagittarius
- **Repository reference:** `refs/heads/<branch_name>` or `refs/tags/<tag_name>`
- **Compose path:** relative path to the stack's `.yml` file (e.g. `opencloud.yml`)
- **GitOps updates:** on/off (on is recommended)
- **Enable access control:** on
- **Access control:** restricted (recommended)

### Existing Stacks

Existing stacks will be listed where access rights allow, and can be modified or redeployed by clicking on a stack.
If `GitOps updates` is enabled on a stack, changes will be pulled from this Git repository roughly every 5 minutes.