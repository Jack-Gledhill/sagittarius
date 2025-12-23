# Stack: OpenCloud

Deploys a full OpenCloud stack, including the following components:

- OpenCloud itself with various web extensions
- WOPI server for accessing document editors
- Collabora Online for editing documents
- Radicale for CalDAV and CardDAV
- ClamAV for antivirus scanning
- Tika for full-text search

## Config Files

Radicale and OpenCloud both have need of a few configuration files, all of which can be found in `./files`.
These should be automatically mounted into the relevant containers by Portainer, provided `Enable relative path volumes` is enabled when deploying the stack.

## Container Memory Limits

Most of the containers have been given memory limits to avoid OOMKills by the host machine.
These limits are defined in `./extends/limits.yml` and automatically imported into the relevant containers.

## Environment Variables

Most of the environment variables are static and don't really need changing, but a few do.
Example values for these are available in `example.env`, and should be passed to Portainer when deploying the stack.