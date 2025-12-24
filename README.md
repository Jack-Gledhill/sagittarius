# Sagittarius Deployment Files

This Git repository acts as a database of sorts for the Docker Compose stacks running on Sagittarius through Portainer.

## File Organisation

Each stack should have its own directory at the root of the repository, which contains the following:

- A `README.md` file, describing the stack and any other relevant information for yourself or others
- A `compose.yml` file, containing the stack itself
- A subdirectory called `includes` which contains any additional Compose files included by the main `compose.yml` file using the [`include`](https://docs.docker.com/compose/how-tos/multiple-compose-files/include/) element
- A subdirectory called `files` which contains any additional files required by the stack which can be automatically mounted into containers with relative `volumes` when `Enable relative path volumes` is enabled
- An optional `stack.env` file containing PUBLIC environment variables that can be used by the stack
- An optional `example.env` file which contains example values for secret environment variables required by the stack
- Any other `*.env` files containing any secret environment variables required by the stack (this won't be committed to the repository and is only for your own reference)

This structure should keep everything neat, maintainable and easy to understand.

## Deployment

Portainer's UI can be accessed at [`portainer.starsystem.dev`](https://portainer.starsystem.dev) (2FA required).
The `Stacks` menu will show all existing stacks and allow the addition of new stacks.

### Environment Variables

Any file ending in `.env` (except `stack.env` and `example.env`) will automatically be ignored during commits through the `.gitignore` file. 
This is intentional.

Most environment variables should already be included in the stack's Compose file or `stack.env`, but secrets should NOT be included in there directly.
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

**Warning:** Portainer seems to have a particularly annoying bug where environment variables with commas in their values are not loaded into the container properly when relative path mounts is enabled. 
The workaround for now is to escape commas with `\` until the bug is fixed. 
See [this issue for details](https://github.com/portainer/portainer/issues/11091).

### New Stacks

New stacks can be added using the `Add stack` button.
Stacks should be added using the `Repository` build method with the following parameters:

- **Repository URL:** https://github.com/Jack-Gledhill/sagittarius
- **Repository reference:** `refs/heads/main`
- **Compose path:** relative path to the stack's `compose.yml` file (e.g. `opencloud/compose.yml`)
- **GitOps updates:** on/off (on is recommended)
- **Enable relative path volumes:** on (if you need it)
- **Enable access control:** on
- **Access control:** restricted (recommended)

### Existing Stacks

Existing stacks will be listed where access rights allow, and can be modified or redeployed by clicking on a stack.
If `GitOps updates` is enabled on a stack, changes will be pulled from this Git repository roughly every 5 minutes.